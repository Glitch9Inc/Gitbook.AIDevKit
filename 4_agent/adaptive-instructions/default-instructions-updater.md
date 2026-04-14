# Default Instructions Updater

The Default Instructions Updater is an LLM-driven adaptive instructions system that automatically detects topic shifts and generates contextually appropriate system instructions without requiring manual rule definition.

## Overview

Instead of manually defining rules for different conversation scenarios, the Default Updater:

1. **Embeds each user message** to create a semantic representation
2. **Compares similarity** to the last message that triggered instruction generation
3. **Detects topic shifts** when similarity falls below a threshold
4. **Calls an LLM** to generate new, context-appropriate instructions
5. **Caches the result** until the next topic shift occurs

This approach provides zero-configuration adaptive behavior that works for any conversation topic.

## How It Works

### Topic Shift Detection Flow

```
User Message
    ↓
Embed message (cheap, ~$0.0001)
    ↓
Compare to cached embedding
    ↓
Similarity ≥ Threshold?
    ├─ Yes → Reuse cached instructions (no cost)
    └─ No → Generate new instructions (LLM call, ~$0.001-0.01)
           ↓
       Cache new embedding + instructions
           ↓
       Use new instructions for this turn
```

### Embedding-Based Detection

The system uses **cosine similarity** between normalized embedding vectors:

- **Similarity = 1.0**: Identical topics
- **Similarity = 0.9**: Very similar contexts
- **Similarity = 0.8**: Related but shifting topic
- **Similarity = 0.6**: Different topics

Default threshold: **0.82** (good balance between sensitivity and cost)

## Configuration

### In AgentProfile Inspector

The Default Updater is the default implementation used by `AdaptiveInstructionsSettings`:

```
Agent Profile > Adaptive Instructions
─────────────────────────────────────
☑ Enable
Embedding Model: "text-embedding-3-small"
Generation Model: "gpt-4o-mini"
Min Message Length: 10
Min Question Length: 5
Meta Instructions: [Your agent context]
Topic Shift Threshold: 0.82
Max Tokens: 400
```

### Configuration Properties

#### Enable
Toggle the entire adaptive instructions system on/off.

#### Embedding Model
Model used for topic shift detection. Recommendations:
- `text-embedding-3-small` - Fast and cost-effective (recommended)
- `text-embedding-ada-002` - Reliable, widely supported

#### Generation Model
LLM used to generate new instructions when a topic shift is detected. Recommendations:
- `gpt-4o-mini` - Balanced cost and quality (recommended)
- `gpt-4o` - Highest quality
- `gpt-3.5-turbo` - Most cost-effective

#### Min Message Length (5-30)
Messages shorter than this value (in characters) are skipped. Prevents updates for:
- Acknowledgments: "ok", "thanks"
- Brief responses: "yes", "no"

Default: **10 characters**

#### Min Question Length (5-30)
Questions shorter than this value are skipped. Prevents updates for:
- Single-word questions: "why?", "how?"
- Unclear queries: "???"

Default: **5 characters**

#### Meta Instructions
Additional context describing your agent's role and purpose. This is prepended to the default meta prompt sent to the LLM.

Example:
```
This agent provides customer support for our SaaS project management platform.
The agent should be professional, empathetic, and solution-focused.
Common topics include: subscription billing, feature requests, bug reports, integrations.
```

#### Topic Shift Threshold (0-1)
Cosine similarity threshold below which a topic shift is declared:

| Threshold | Behavior | Use Case |
|-----------|----------|----------|
| 0.75-0.78 | Very sensitive | Highly varied conversations |
| 0.80-0.84 | Balanced | General purpose (recommended) |
| 0.85-0.90 | Conservative | Reduce API calls/costs |

Default: **0.82**

#### Max Tokens
Maximum number of tokens for the generated system instructions.

- **Recommended**: 300-500 tokens
- **Default**: 400 tokens

## LLM Instruction Generation

### Default Meta Prompt

When no meta instructions are provided, the system uses:

```
You are a system instructions generator for an AI assistant.
When given a user message, output concise and effective system instructions
that will make the assistant respond optimally to that message and similar ones.
Output ONLY the system instructions text – no explanation, no preamble, no markdown.
```

### With Meta Instructions

When you provide meta instructions, they are appended:

```
You are a system instructions generator for an AI assistant.
When given a user message, output concise and effective system instructions
that will make the assistant respond optimally to that message and similar ones.
Output ONLY the system instructions text – no explanation, no preamble, no markdown.

Agent context:
This agent provides customer support for our SaaS project management platform.
The agent should be professional, empathetic, and solution-focused.
Common topics include: subscription billing, feature requests, bug reports, integrations.
```

### Example Generated Instructions

**User message**: "I can't figure out how to add teammates to my project"

**Generated instructions**:
```
You are a helpful customer support assistant for a project management platform.
The user needs help with adding team members to projects.
Provide clear, step-by-step guidance. Be patient and encouraging.
Ask clarifying questions if needed (e.g., their current subscription tier).
```

## Usage Examples

### Basic Setup

```csharp
using Glitch9.AIDevKit.Agents;
using UnityEngine;

public class SupportBot : MonoBehaviour
{
    [SerializeField] private AgentProfile profile;
    
    private void Start()
    {
        // Configure in inspector:
        // Capabilities: ☑ Adaptive Instructions
        // Adaptive Instructions Settings:
        //   Enable: true
        //   Embedding Model: "text-embedding-3-small"
        //   Generation Model: "gpt-4o-mini"
        //   Meta Instructions: "Customer support agent for SaaS platform"
        
        // The agent will automatically use adaptive instructions
    }
}
```

### Monitor Topic Shifts

```csharp
public class AdaptiveMonitor : AgentBehaviour
{
    private int topicShiftCount = 0;
    private string lastInstructions;
    
    protected override void OnEnable()
    {
        base.OnEnable();
        
        if (Agent != null)
        {
            Agent.OnInstructionsUpdated += HandleInstructionsUpdate;
        }
    }
    
    private void HandleInstructionsUpdate(string newInstructions)
    {
        topicShiftCount++;
        lastInstructions = newInstructions;
        
        Debug.Log($"Topic shift #{topicShiftCount}");
        Debug.Log($"New instructions:\n{newInstructions}");
    }
}
```

### Dynamic Threshold Adjustment

```csharp
public class DynamicThresholdAgent : AgentBehaviour
{
    private float baseThreshold = 0.82f;
    
    // Adjust threshold based on conversation context
    public void SetSensitivity(bool highSensitivity)
    {
        float newThreshold = highSensitivity ? 0.75f : 0.88f;
        
        // Note: Can't modify threshold at runtime in current version
        // This would require custom IInstructionsUpdater implementation
        
        Debug.Log($"Threshold change requested: {newThreshold}");
    }
}
```

### Custom Implementation

For full runtime control, implement `IDefaultInstructionsUpdaterSettings`:

```csharp
using Glitch9.AIDevKit.Agents;

public class CustomAdaptiveSettings : IDefaultInstructionsUpdaterSettings
{
    public Model EmbeddingModel { get; set; }
    public Model GenerationModel { get; set; }
    public float TopicShiftThreshold { get; set; } = 0.82f;
    public int MaxTokens { get; set; } = 400;
    public string MetaInstructions { get; set; }
    public int MinMessageLength { get; set; } = 10;
    public int MinQuestionLength { get; set; } = 5;
    
    public CustomAdaptiveSettings(Model embedModel, Model genModel)
    {
        EmbeddingModel = embedModel;
        GenerationModel = genModel;
    }
}

// Usage
public class CustomAdaptiveAgent : AgentBehaviour
{
    protected override void Start()
    {
        base.Start();
        
        var settings = new CustomAdaptiveSettings(
            "text-embedding-3-small",
            "gpt-4o-mini"
        )
        {
            TopicShiftThreshold = 0.80f,
            MetaInstructions = "Support agent for mobile game",
            MaxTokens = 300
        };
        
        var updater = new DefaultInstructionsUpdater(settings, Logger);
        
        // Set custom updater (requires direct Agent access)
        // Agent.SetInstructionsUpdater(updater);
    }
}
```

## Performance Optimization

### Cost Analysis

Assuming:
- Embedding: $0.0001 per message
- Generation: $0.005 per update

| Conversation | Messages | Topic Shifts | Total Cost |
|--------------|----------|--------------|------------|
| 10 messages | 10 | 2 | $0.011 |
| 50 messages | 50 | 5 | $0.030 |
| 100 messages | 100 | 8 | $0.048 |

### Optimization Strategies

#### 1. Use Efficient Models

```csharp
// Fast, cheap embedding
Embedding Model: "text-embedding-3-small"

// Balanced generation quality/cost
Generation Model: "gpt-4o-mini"
```

#### 2. Increase Threshold

```csharp
// Reduce topic shift sensitivity
Topic Shift Threshold: 0.85  // vs default 0.82
```

Result: ~30% fewer LLM calls

#### 3. Filter Short Messages

```csharp
Min Message Length: 15  // Skip "ok", "thanks", brief responses
Min Question Length: 10  // Skip single-word questions
```

Result: ~20% fewer embedding operations

#### 4. Reduce Max Tokens

```csharp
Max Tokens: 250  // vs default 400
```

Result: ~40% cost reduction per generation call

## Best Practices

### 1. Write Effective Meta Instructions

**Good**:
```
This agent helps users troubleshoot technical issues with our IoT devices.
The agent should be technical but accessible, patient, and methodical.
Always ask for device model and firmware version first.
Common issues: connectivity, battery drain, sensor calibration.
```

**Bad**:
```
Help users
```

### 2. Choose the Right Threshold

Test different values with your conversation patterns:

```csharp
// Start conservative
Topic Shift Threshold: 0.85

// Monitor for 10-20 conversations
// If topics are missed: lower to 0.80
// If updates are too frequent: raise to 0.88
```

### 3. Monitor Initial Generation

The first message always triggers generation. Make sure:
- Your embedding model is configured correctly
- Your generation model is accessible
- Meta instructions are clear

### 4. Handle Generation Failures

```csharp
private void HandleInstructionsUpdate(string newInstructions)
{
    if (string.IsNullOrEmpty(newInstructions))
    {
        Debug.LogWarning("Instructions update returned null - using previous instructions");
        return;
    }
    
    Debug.Log($"Instructions updated: {newInstructions.Length} chars");
}
```

### 5. Cache Invalidation

If you need to force regeneration:

```csharp
// Access the updater (requires custom implementation)
if (instructionsUpdater is DefaultInstructionsUpdater defaultUpdater)
{
    defaultUpdater.Invalidate(); // Clears cache
}
```

## Debugging

### Enable Detailed Logging

The updater logs all topic shift decisions:

```
DefaultInstructionsUpdater: No cache. Generating initial instructions...
DefaultInstructionsUpdater: Instructions generated (324 chars).
DefaultInstructionsUpdater: Topic unchanged (similarity=0.891). Reusing cached instructions.
DefaultInstructionsUpdater: Topic shift detected (similarity=0.753 < threshold=0.820). Regenerating...
```

### Test Embedding Similarity

```csharp
private async void TestSimilarity()
{
    var msg1 = "I need help with billing";
    var msg2 = "My credit card was charged twice";
    var msg3 = "How do I export project data?";
    
    var emb1 = await EmbedMessage(msg1);
    var emb2 = await EmbedMessage(msg2);
    var emb3 = await EmbedMessage(msg3);
    
    Debug.Log($"billing vs charge: {CosineSim(emb1, emb2):F3}");  // ~0.85 (similar)
    Debug.Log($"billing vs export: {CosineSim(emb1, emb3):F3}");  // ~0.65 (different)
}
```

### Track Update Patterns

```csharp
private List<(string message, bool updated)> updateLog = new();

private void HandleMessage(string message)
{
    int countBefore = updateLog.Count(u => u.updated);
    
    // ... conversation happens ...
    
    int countAfter = updateLog.Count(u => u.updated);
    bool wasUpdated = countAfter > countBefore;
    
    updateLog.Add((message, wasUpdated));
    
    // Analyze patterns
    if (updateLog.Count >= 10)
    {
        float updateRate = updateLog.Count(u => u.updated) / (float)updateLog.Count;
        Debug.Log($"Update rate: {updateRate:P0}");
    }
}
```

## Limitations

### Current Limitations

1. **No conversation history**: Generated instructions don't see full conversation context, only the current message
2. **No caching across sessions**: Cache is cleared when agent restarts
3. **Fixed meta prompt**: Can't customize the meta prompt format
4. **Single threshold**: All topics use the same threshold

### Workarounds

**Limitation**: No conversation history
**Workaround**: Provide rich meta instructions that describe common conversation patterns

**Limitation**: No caching across sessions
**Workaround**: Use rule-based updater for predictable scenarios

**Limitation**: Fixed meta prompt
**Workaround**: Implement custom `IInstructionsUpdater`

## See Also

- [Adaptive Instructions Overview](README.md) - Main adaptive instructions guide
- [Custom Instructions Updater](custom-instructions-updater.md) - Rule-based alternative
- [Agent Profile](../components/agent-profile.md) - Agent configuration
- [Conversation Summary](../memory/conversation-summary.md) - Related context management
