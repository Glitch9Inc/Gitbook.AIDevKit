---
description: How agents remember past messages and retrieve relevant context using RAG
icon: brain
---

# Memory (RAG)

By default, an Agent only "remembers" the last N messages it has seen - a simple sliding window. When a conversation grows long, old messages fall off and the model forgets them entirely.

**RAG (Retrieval-Augmented Generation)** solves this by embedding every message into a local vector store. Before each request, the most semantically relevant past messages are retrieved and injected into the context. The model is reminded of information from hours or even sessions ago - without sending the entire history every time (which would be expensive).

---

## How the Two Systems Work Together

```
              New user message
                     |
      +------------------------------+
      |  Short-Term Context Window   |  <- Last N messages (always included)
      +------------------------------+
                     +
      +------------------------------+
      |   RAG Retrieval (optional)   |  <- Top-K semantically similar past messages
      +------------------------------+
                     |
           Final context sent to model
```

The two systems are additive. Short-term context ensures recency; RAG ensures relevance for older information.

---

## Configuring Memory

Memory is configured in the **Context & Memory** section of your `AgentSettings` asset.

### Basic Settings

| Setting | Description | Default |
|---|---|---|
| **Auto Save** | Persist conversation to disk after each message | On |
| **Max Context Messages** | How many recent messages to always include | 20 |
| **Generate Title** | Automatically generate a title for the conversation | On |

#### Max Context Messages

This is the most important tuning knob. A higher value gives the model more context but increases cost (more tokens per request).

| Range | Typical use case |
|---|---|
| 10-20 | Mobile apps, simple chat UIs |
| 20-50 | Complex multi-turn assistants |
| 50+ | Use only when RAG is also enabled to offset token cost |

---

### RAG Settings

RAG is **disabled by default**. Enable it from **Context & Memory > Use Vector Store**.

| Setting | Description | Default |
|---|---|---|
| **Use Vector Store** | Enable local RAG retrieval | Off |
| **Embedding Model** | Model used to embed messages (e.g. `text-embedding-3-small`) | - |
| **Retrieval Top K** | How many past messages to retrieve per request | 8 |
| **Retrieval Min Similarity** | Minimum cosine similarity to include a result (0-1) | 0.5 |

#### When to Enable RAG

Enable RAG when:
- Conversations span multiple sessions and the user expects the agent to recall earlier topics
- You want to reduce `MaxContextMessages` for cost savings without sacrificing long-term recall
- Your agent acts as a knowledge assistant that needs to reference specific past details

Leave RAG off when:
- Conversations are short and ephemeral (one-shot Q&A, simple support chat)
- You are prioritizing low latency (RAG adds an embedding API call per request)
- Your embedding API budget is limited

---

## Tuning Retrieval Quality

### Retrieval Top K

Controls how many past messages are retrieved per request:

| Top K | Effect |
|---|---|
| 1-5 | Focused - only the most relevant results; lowest token cost |
| **6-12** | **Recommended** - balanced relevance and context coverage |
| 13-32 | Broad retrieval; higher cost, useful for knowledge-dense agents |

### Retrieval Min Similarity

Controls how closely a past message must match the current input before being included:

| Min Similarity | Effect |
|---|---|
| 0.7-1.0 | Strict - only highly relevant matches |
| **0.5-0.7** | **Recommended** - balanced for most use cases |
| 0.25-0.5 | Loose - may include weakly related content |
| < 0.25 | Very loose - likely retrieves noise |

{% hint style="info" %}
Start with the defaults (**Top K = 8, Min Similarity = 0.5**) and only tune if you observe that relevant past messages are being missed or irrelevant ones are showing up.
{% endhint %}

---

## Conversation Title Generation

When enabled, the Agent automatically generates a descriptive title for the conversation after the first exchange. This title is used in conversation list UIs.

| Setting | Description |
|---|---|
| **Enabled** | Turn title generation on or off |
| **Trigger After Message Count** | Generate title after N messages (default: 2) |
| **Regenerate Until Message Count** | Keep regenerating until N messages, then lock (0 = generate once only) |
| **Model** | Lightweight model for title generation (defaults to a small utility model) |

---

## Conversation Store

Memory only persists across sessions if a **ConversationStore** is configured. The store type is set on the `AgentBehaviour` component (not on `AgentSettings`):

| Store Type | Description |
|---|---|
| **Do Not Save** | Conversation is lost when the scene unloads |
| **Local File** | Saved as a JSON file on the device |
| **Threads API (OpenAI)** | Conversation stored in OpenAI's Threads API |
| **Conversations API (OpenAI)** | Stateful conversation via OpenAI Responses API |
| **Custom Store** | Implement `IConversationStore` for cloud storage or your own backend |

For a full guide on saving, loading, and managing multiple conversations, see [Saving & Loading](../conversations/saving-and-loading.md).
