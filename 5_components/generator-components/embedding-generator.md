---
icon: vector-square
---

# Embedding Generator

`EmbeddingGenerator` converts text into vector embeddings (`Embedding`) for semantic search, similarity comparison, and RAG applications.

## Component Menu

```
AI Dev Kit / AI Generators / Embedding Generator
```

## Inspector Settings

| Field | Description |
|---|---|
| **Settings** | `EmbeddingsSettings` asset — model and embedding options |
| **Save Outputs** | Persist generated embeddings to the output folder |
| **Output Path** | Folder path for saved embeddings |

### EmbeddingsSettings fields

| Field | Description |
|---|---|
| **Model** | Embedding model (e.g. `text-embedding-3-small`, `text-embedding-ada-002`) |
| **Dimensions** | Number of dimensions for the embedding vector |
| **Embed Task Type** | Task type for embedding (e.g. `RetrievalQuery`, `RetrievalDocument`) |
| **Max Tokens Per Input** | Maximum tokens per input text |

## Events

| Event | Signature | Description |
|---|---|---|
| `onOutput` | `UnityEvent<Embedding>` | Fires when the embedding is ready. |
| `onStatusChanged` | `UnityEvent<ResponseStatus>` | Fires on status change. |

## Methods

| Method | Description |
|---|---|
| `Generate(string input)` | Fire-and-forget embedding generation from text. |
| `GenerateAsync(string input)` | Awaitable version. Returns `Generated<Embedding>`. |
| `Configure(EmbeddingsSettings)` | Override inspector settings at runtime. |

## Code Example

```csharp
using Glitch9.AIDevKit.Generators;
using UnityEngine;

public class EmbeddingExample : MonoBehaviour
{
    public EmbeddingGenerator embeddingGenerator;

    async void Start()
    {
        string text = "Hello world";
        Generated<Embedding> result = await embeddingGenerator.GenerateAsync(text);
        
        if (result.IsSuccess)
        {
            float[] vector = result.Value.Vector;
            Debug.Log($"Generated embedding with {vector.Length} dimensions");
        }
    }
}
```

## Use Cases

- **Semantic Search**: Find similar content by comparing embedding vectors
- **RAG (Retrieval Augmented Generation)**: Store embeddings in vector databases
- **Similarity Comparison**: Calculate cosine similarity between texts
- **Content Recommendation**: Recommend related content based on embedding similarity

## Related Topics

- [Text Embedding](../../3_requests/embedding/text-embedding.md)
- [Batch Embedding](../../3_requests/embedding/batch-embedding.md)
- [Vector Store Overview](../../4_agent/memory/rag-overview.md)
