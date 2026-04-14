---
description: A look inside the Agent - from Unity component to API response
---

# How Agent Works

You don't need to understand this page to get started, but knowing the architecture makes it much easier to customize or debug the Agent.

---

## The Layer Cake

```
+-----------------------------------------------+
|         Your MonoBehaviour / UI               |  <- You write this
+-----------------------------------------------+
|              AgentBehaviour                   |  <- Unity Component
+-----------------------------------------------+
|                  Agent                        |  <- Core runtime (pure C#)
+--------------+-------------+-----------------+
| Conversation |   Tools     |  Audio (opt.)   |  <- Internal controllers
| Controller   | Controller  |  Controller     |
+--------------+-------------+-----------------+
| ChatCompletions / Responses / Assistants /    |  <- Chat API adapter
|                 Realtime API                  |
+-----------------------------------------------+
```

**`AgentBehaviour`** is the Unity MonoBehaviour you attach to a GameObject. It creates and owns the `Agent` instance throughout the scene lifetime.

**`Agent`** is a pure C# class with no Unity dependencies. It orchestrates all sub-systems and is the main entry point for sending messages.

---

## Startup: What Happens in Awake

When your scene loads, `AgentBehaviour.Awake()` runs automatically:

1. **Resolve settings** - reads `AgentSettings` and `AgentProfile` from the Inspector
2. **Build dependencies** - creates `AgentDependencies`: chat service, conversation store, audio components, tool routing model, and instructions updater
3. **Instantiate Agent** - `new Agent(settings, config, dependencies, saveDataStore, logger)`
4. **Auto-initialize** *(if enabled)* - immediately calls `Agent.InitializeAsync()`

During `InitializeAsync()`:

* Saved runtime data (last model, voice, conversation ID) is loaded from the save store
* `ToolManagerBase` components on the GameObject and its children are discovered and their tools are registered
* The conversation is created or loaded from the `ConversationStore`
* All services (audio, moderation, MCP access tokens) are initialized

{% hint style="warning" %}
**Don't call `Agent.SendAsync()` before initialization completes.** Either enable **AutoInit** in AgentBehaviour's Inspector, or await `Agent.InitializeAsync()` yourself before your first message.
{% endhint %}

---

## What Happens When You Send a Message

Calling `agent.SendAsync("Hello")` triggers this pipeline:

### Step 1 - Validation

The Agent verifies it is initialized, a model is set, and no other request is already in flight.

### Step 2 - Context Assembly

The `ConversationController` prepares the full message list to send to the model:
- Your new message is appended to the conversation history
- Recent messages are included up to `MaxContextMessages`
- If RAG is enabled, semantically relevant older messages are retrieved from the local vector store and injected into the context

### Step 3 - API Call

The assembled context is passed to the chat service adapter (`ChatCompletionsApi`, `ResponsesApi`, etc.).
- If **streaming** is on, token deltas arrive in real time and fire `OnTextDelta` events on your listener
- If **streaming** is off, the full response arrives as a single result

### Step 4 - Response Handling

Once the response is complete:
- The response message is saved to the conversation
- Token usage is tracked and reported via `OnUsageEvent`
- If the response contains **tool calls**, the `ToolController` takes over (Step 5)
- Otherwise, the Agent fires completion events and returns to `Ready`

### Step 5 - Tool Call Loop *(only when the model requests tools)*

```
Model response contains ToolCall(s)
         |
ToolController finds matching executor(s)
         |
Executors run -> outputs collected
         |
All outputs submitted back to model as ToolOutput(s)
         |
Model generates final response -> back to Step 4
```

---

## Internal Controllers

The `Agent` delegates work to four internal controllers. You don't create these - they are built automatically.

| Controller | Responsibility |
|---|---|
| `ConversationController` | Stores messages, assembles context, manages the `Conversation` object |
| `ToolController` | Registers tools, dispatches tool calls to executors, batch-submits outputs |
| `AudioController` | Wraps `SpeechToTextController` + `TextToSpeechController` for voice I/O |
| `McpController` | Manages MCP tool OAuth tokens and user approval flows |

---

## ChatApiType Reference

The `ChatApiType` field on `AgentSettings` controls which underlying protocol is used:

| ChatApiType | Protocol | Best for |
|---|---|---|
| **ChatCompletions** | HTTP REST | Standard text chat, widest provider support |
| **Responses** | HTTP REST | OpenAI stateful responses, tool use, web search |
| **Assistants** | HTTP REST | OpenAI Assistants API with persistent Threads |
| **Realtime** | WebSocket | Low-latency voice (OpenAI only) |

{% hint style="info" %}
AIDevKit auto-selects `ResponsesApi` or `ChatCompletionsApi` based on the active model's provider. You only need to set `ChatApiType` manually when using **Assistants** or **Realtime**.
{% endhint %}

---

## Agent Status

`Agent.Status` always reflects what the Agent is currently doing:

| Status | Meaning |
|---|---|
| `None` | Not yet initialized |
| `Initializing` | Running `InitializeAsync()` |
| `Ready` | Idle, waiting for input |
| `GeneratingResponse` | API call in progress |
| `WaitingForToolOutput` | Waiting for manual tool output submission |
| `Cancelling` | A cancellation was requested |
| `InitializationFailed` | Setup failed - check logs for the reason |

---

## How Events Reach Your Code

The Agent never calls your MonoBehaviour methods directly. Instead it dispatches events you subscribe to:

| Event | Fired when |
|---|---|
| `OnTextDelta` | A streaming text chunk arrives |
| `OnAgentStatusChanged` | The Agent transitions between states |
| `OnToolStatus` | A tool call starts or finishes |
| `OnUsageEvent` | Token usage is reported |
| Conversation events | Conversation is created, loaded, or updated |

See [Agent Events](../events/README.md) for the full list and usage examples.
