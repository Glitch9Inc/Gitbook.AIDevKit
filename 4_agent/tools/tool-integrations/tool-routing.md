# Tool Routing

Tool Routing uses embedding-based semantic similarity to intelligently select the most relevant tools for each user message, reducing token usage and improving response quality.

## Overview

The `DefaultToolRouter` implements an embedding-based tool selection strategy that:

1. Embeds all tool descriptions into a vector space
2. Embeds the user's message into the same vector space
3. Finds the top-K most similar tools using cosine similarity
4. Passes only those relevant tools to the LLM

This approach significantly reduces prompt token count when you have a large number of registered tools, while ensuring the LLM has access to the most contextually relevant tools.

## How It Works

### Architecture Flow

```
ToolRegistry (full tool list)
  ↓ embed each tool's description text
ToolEmbeddingIndex (in-memory vector index)
  ↓ embed user message
  ↓ cosine similarity search
  ↓ return top-K tools
Reduced tool list sent to LLM (saves tokens)
```

### When Routing Activates

- **Tool count ≤ topK**: Returns the full list without filtering
- **Tool count > topK**: Performs semantic search and returns top-K most relevant tools
- **No embedding model**: Falls back to full tool list
- **Embedding fails**: Falls back to full tool list

## Configuration

Tool routing is configured in the `AgentProfile` component via `AgentToolRoutingSettings`.

### Enable Tool Routing Capability

First, ensure the `ToolRouting` capability is enabled in your AgentProfile:

```csharp
// In AgentProfile inspector or code
capabilities = AgentCapabilities.ToolRouting;
```

### Set Embedding Model

Configure the embedding model used for semantic search:

```csharp
// In Unity Inspector: AgentProfile > Tool Routing Settings
Embedding Model: "text-embedding-3-small"
```

Or in code:

```csharp
public class MyAgentProfile : AgentProfile
{
    protected override void Awake()
    {
        base.Awake();
        
        // Tool routing is only available when capability is enabled
        if (ToolRoutingSettings != null)
        {
            var router = ToolRoutingSettings.Build();
            // Router will be used automatically by the agent
        }
    }
}
```

## Implementation Details

### DefaultToolRouter

The default implementation uses cosine similarity on normalized embedding vectors:

```csharp
public class DefaultToolRouter : IToolRouter
{
    private Model m_EmbeddingModel;
    private ToolEmbeddingIndex m_Index;
    
    public DefaultToolRouter(Model embeddingModel)
    {
        m_EmbeddingModel = embeddingModel;
    }
    
    public async UniTask<List<Tool>> RouteAsync(
        string userMessage,
        int topK = 12,
        CancellationToken ct = default)
    {
        // Returns full list if no embedding model or count ≤ topK
        if (m_EmbeddingModel == null || allTools.Count <= topK)
            return allTools;
            
        // Build index if needed
        if (!m_IsBuilt)
            await BuildIndexAsync(allTools, ct);
            
        // Embed query and search
        float[] queryVec = await EmbedAsync(userMessage, ct);
        return m_Index.Search(queryVec, topK);
    }
}
```

### Tool Embedding Strategy

Different tool types use different embedding strategies:

#### Function Tools
Uses `"name: description"` format to maximize semantic information:

```csharp
// For a function tool with name "search_database" and description "Searches the user database"
string embedText = "search_database: Searches the user database";
```

#### Built-in Tools
Uses the type name:

```csharp
// For built-in tools like WebSearchTool
string embedText = "WebSearchTool";
```

### ToolEmbeddingIndex

An in-memory vector index using cosine similarity:

```csharp
sealed class ToolEmbeddingIndex
{
    // Stores normalized vectors for fast similarity search
    private List<(Tool tool, float[] vector)> m_Entries;
    
    public List<Tool> Search(float[] queryVector, int topK)
    {
        // 1. Normalize query vector
        // 2. Compute dot product (equals cosine similarity for normalized vectors)
        // 3. Sort by score descending
        // 4. Return top K tools
    }
}
```

## Customization

### Implement Custom Router

Create your own routing strategy by implementing `IToolRouter`:

```csharp
public class RuleBasedToolRouter : IToolRouter
{
    public void Initialize(ToolRegistry registry, ILogger logger)
    {
        // Store registry reference
    }
    
    public async UniTask<List<Tool>> RouteAsync(
        string userMessage, 
        int topK = 12, 
        CancellationToken ct = default)
    {
        // Your custom routing logic
        // e.g., tag-based, keyword matching, etc.
        
        List<Tool> selectedTools = new();
        
        if (userMessage.Contains("database"))
            selectedTools.Add(databaseTool);
        if (userMessage.Contains("weather"))
            selectedTools.Add(weatherTool);
            
        return selectedTools;
    }
    
    public void Invalidate()
    {
        // Clear any cached state
    }
}
```

### Change Embedding Model

Update the embedding model dynamically:

```csharp
public class DynamicToolRoutingProfile : AgentProfile
{
    public void UpdateEmbeddingModel(Model newModel)
    {
        if (ToolRoutingSettings != null)
        {
            var router = ToolRoutingSettings.Build();
            router.SetEmbeddingModel(newModel);
        }
    }
}
```

### Adjust Top-K Value

Change the number of tools returned:

```csharp
// In your agent implementation
List<Tool> tools = await router.RouteAsync(
    userMessage, 
    topK: 8, // Return only 8 most relevant tools
    ct
);
```

## Performance Considerations

### Index Building

The index is built lazily on the first routing request:

- **Built once** when first needed
- **Rebuilt** when `Invalidate()` is called
- **Rebuilt** when embedding model changes

```csharp
// Force index rebuild after adding new tools
toolRegistry.RegisterTool(newTool);
router.Invalidate(); // Index will rebuild on next route
```

### Token Savings

Example token savings with 50 registered tools:

| Scenario | Tools Sent | Approximate Tokens |
|----------|------------|-------------------|
| No Routing | 50 tools | ~5,000 tokens |
| With Routing (topK=12) | 12 tools | ~1,200 tokens |
| **Savings** | - | **~76% reduction** |

### Fallback Behavior

The router automatically falls back to the full tool list if:

- Embedding model is not configured
- Embedding request fails
- Index build fails

This ensures your agent continues functioning even if routing encounters issues.

## Best Practices

### 1. Use Descriptive Tool Names and Descriptions

The router uses tool descriptions for embedding, so make them clear:

```csharp
// Good - clear and descriptive
new Function(
    name: "search_user_database",
    description: "Searches the user database by email, username, or ID"
);

// Less effective - vague
new Function(
    name: "search",
    description: "Searches stuff"
);
```

### 2. Choose Appropriate Embedding Models

Small, fast models work well for tool routing:

```csharp
// Recommended models
"text-embedding-3-small"  // Fast, cost-effective
"text-embedding-ada-002"  // Reliable, widely supported
```

### 3. Monitor Index Invalidation

Invalidate the index when tools change:

```csharp
public class MyToolManager
{
    private IToolRouter m_Router;
    private ToolRegistry m_Registry;
    
    public void AddTool(Tool tool)
    {
        m_Registry.RegisterTool(tool);
        m_Router.Invalidate(); // Rebuild index with new tool
    }
    
    public void RemoveTool(string toolName)
    {
        m_Registry.UnregisterTool(toolName);
        m_Router.Invalidate(); // Rebuild index
    }
}
```

### 4. Adjust TopK Based on Tool Count

Configure topK appropriate to your tool count:

```csharp
int toolCount = registry.Tools.Count;
int topK = toolCount switch
{
    <= 12 => toolCount,      // No routing needed
    <= 50 => 12,             // Default
    <= 100 => 15,            // Slightly more for large sets
    _ => 20                  // Cap at 20 for very large sets
};
```

### 5. Handle Routing Failures Gracefully

The router already includes fallback logic, but you can add logging:

```csharp
public class LoggingToolRouter : DefaultToolRouter
{
    public LoggingToolRouter(Model embeddingModel) 
        : base(embeddingModel) { }
    
    public override async UniTask<List<Tool>> RouteAsync(
        string userMessage, 
        int topK = 12, 
        CancellationToken ct = default)
    {
        var tools = await base.RouteAsync(userMessage, topK, ct);
        
        if (tools.Count < topK)
        {
            Debug.LogWarning($"Routing returned {tools.Count} tools (expected {topK})");
        }
        
        return tools;
    }
}
```

## Debugging

### Log Selected Tools

See which tools were selected for the current message:

```csharp
List<Tool> selectedTools = await router.RouteAsync(userMessage, topK: 12);

Debug.Log($"Selected {selectedTools.Count} tools for: {userMessage}");
foreach (var tool in selectedTools)
{
    if (tool is Function fn)
        Debug.Log($"  - {fn.Name}: {fn.Description}");
    else
        Debug.Log($"  - {tool.GetType().Name}");
}
```

### Verify Embeddings

Check if embeddings are being generated:

```csharp
// Enable detailed logging in DefaultToolRouter
private async UniTask BuildIndexAsync(List<Tool> tools, CancellationToken ct)
{
    m_Index.Clear();
    
    foreach (Tool tool in tools)
    {
        string text = GetEmbedText(tool);
        float[] vec = await EmbedAsync(text, ct);
        
        if (vec != null)
        {
            m_Index.Add(tool, vec);
            Debug.Log($"Embedded tool: {text} ({vec.Length} dimensions)");
        }
        else
        {
            Debug.LogWarning($"Failed to embed tool: {text}");
        }
    }
    
    m_Logger.Info($"Index built: {m_Index.Count}/{tools.Count} tools");
}
```

## Limitations

### Current Limitations

1. **In-Memory Only**: The index is rebuilt on each session start
2. **Single Embedding Model**: Uses one model for all tools
3. **No Caching**: Tool embeddings are regenerated each time
4. **Synchronous Invalidation**: Index rebuild happens on the main thread

### Future Enhancements

These limitations may be addressed in future versions:

- Persistent index storage
- Multi-model routing strategies
- Embedding cache with TTL
- Async index rebuilding

## See Also

- [Tool Settings](tool-settings.md) - Configure tool execution behavior
- [Function Tool](../function-tool.md) - Creating function tools
- [Custom Tool](../custom-tool.md) - Implementing custom tools
- [Tool Call Managers](../../components/tool-call-managers.md) - Managing tool execution
