---
description: How retrieval-augmented generation extends memory beyond recent messages
icon: search
---

# RAG Overview

By default, an agent keeps only the most recent messages in context.

**RAG (Retrieval-Augmented Generation)** stores past messages as embeddings and retrieves the most relevant ones before each request.

## Why use it

- Keeps long-term context without sending full history every turn
- Improves recall of older facts from previous sessions
- Helps reduce token cost when `Max Context Messages` is lower

## Key settings

- **Use Vector Store**: enable/disable RAG
- **Embedding Model**: model used to create embeddings
- **Retrieval Top K**: number of retrieved messages
- **Retrieval Min Similarity**: relevance threshold

{% hint style="info" %}
Start with `Top K = 8` and `Min Similarity = 0.5`, then tune only if recall quality is poor.
{% endhint %}
