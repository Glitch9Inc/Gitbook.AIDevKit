# SharePoint Tool

RAG-powered document search and retrieval for SharePoint sites.

## Overview

The SharePoint Tool enables AI agents to search and answer questions about SharePoint documents using natural language. Documents are automatically indexed with vector embeddings for semantic search.

## Setup

```csharp
using Glitch9.AIDevKit.Microsoft.Graph.Tools;
using Glitch9.AIDevKit.Agents;

// 1. Create and initialize the tool
var sharePointTool = new SharePointToolManager();
await sharePointTool.InitializeAsync();

// 2. Add to your agent
agent.AddTool(sharePointTool);
```

That's it! Your agent can now search SharePoint documents.

## Usage Examples

### Ask Questions About Documents

```csharp
// Users can ask questions naturally
await agent.SendAsync("What are the latest marketing documents?");
await agent.SendAsync("Find the Q4 budget report");
await agent.SendAsync("Summarize the project proposal from last week");
```

The agent will:
1. Search relevant documents
2. Extract key information
3. Provide answers with source citations

### Index New Documents

```csharp
// Index a SharePoint site for searching
await agent.SendAsync("Index all documents in the Engineering site");
```

## Configuration

### Authentication

See [Authentication & Setup](authentication.md) for Microsoft Graph setup.

### Settings

Configure in **Project Settings → AIDevKit Enterprise → SharePoint**:

| Setting | Description | Default |
|---------|-------------|---------|
| Chunk Size | Characters per chunk | 1000 |
| Chunk Overlap | Overlap between chunks | 200 |
| Max Chunks | Max chunks per document | 100 |
| Embedding Model | Model for vectors | text-embedding-3-small |

### Supported File Formats

- PDF documents
- Word documents (.docx)
- PowerPoint presentations (.pptx)
- Text files (.txt)

## Best Practices

1. **Index Once**: Build document index before querying
2. **Specific Queries**: More specific questions get better answers
3. **Site Scope**: Reference specific sites for faster searches
4. **Update Index**: Re-index after document changes

## Troubleshooting

### No Results Found

**Problem**: Agent says "No relevant documents found"

**Solutions**:
- Ensure documents are indexed
- Make queries more specific
- Check user has access to SharePoint site
- Verify authentication is configured

### Slow Searches

**Problem**: Search takes too long

**Solutions**:
- Reduce chunk size in settings
- Specify site name in query ("Search in Marketing site...")
- Pre-filter documents by indexing only needed sites

### Authentication Errors

**Problem**: "Access denied" or permission errors

**Solutions**:
- Verify API permissions in Azure AD
- Check user has SharePoint access
- See [Authentication Guide](authentication.md)

## Related Topics

- [Authentication & Setup](authentication.md)
- [Vector Store Overview](../../memory/rag-overview.md)
- [Microsoft Graph](README.md)
