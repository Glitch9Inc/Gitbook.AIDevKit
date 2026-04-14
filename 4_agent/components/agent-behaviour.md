---
---

# Agent Behaviour

`AgentBehaviour` is the central Unity component for integrating AI agents into a scene. It wraps the core `Agent` runtime and exposes an inspector-friendly API for UI, input, and audio components.

## Component Menu

```
AI Dev Kit / Agent / Agent Behaviour
```

## Overview

Attach `AgentBehaviour` to any `GameObject` to enable:

- Streaming AI responses (text, reasoning, refusal, transcription)
- Conversation management (create, load, delete, persist)
- Automatic discovery and routing to [Tool Managers](tool-managers.md)
- Audio I/O integration via `SpeechToTextRecorder` and `TextToSpeechPlayer`

## Setup

1. Add the **Agent Behaviour** component to a `GameObject`.
2. Assign an **Agent Settings** asset (defines model, instructions, API type, etc.).
3. Optionally assign **Input Audio Recorder** and **Output Audio Player** subcomponents.
4. Add one or more [Tool Manager or Tool Call Manager](tool-managers.md) components as children or siblings — they are auto-discovered at runtime.

## Inspector Sections

### General

| Field | Description |
|---|---|
| Auto Initialize | Start the agent automatically on `Awake`. |
| Don't Destroy On Load | Persist this GameObject across scene loads. |
| Save Generation Records | Save all requests and responses to local storage. |
| Conflict Resolution | How simultaneous requests are handled (`Cancel`, `QueueNew`, etc.). |
| Log Level | Verbosity of internal logging. |

### Request Settings

| Field | Description |
|---|---|
| Streaming | Enables real-time streaming responses. |
| Include Usage | Include token usage statistics in streamed responses (requires Streaming). |
| Include Obfuscation | Include obfuscation metadata in streamed responses (requires Streaming). |
| Disable Text Buffering | When enabled, the complete event fires with only the final delta chunk rather than the full accumulated text (requires Streaming). |
| Save Data Store Type | Storage backend for agent save data (`PlayerPrefs`, `LocalFile`, etc.). |

### Conversation

Manages conversation lifecycle — store type, conversation selection mode, and per-conversation events.

### Tool Calls

| Field | Description |
|---|---|
| Unhandled Tool Call Behaviour | Action to take when no handler is found for a tool call. |
| Submit Tool Output Timeout | Timeout in seconds before an unhandled tool call fails. |

### Events

All events are registered via the **Events** foldout. Use the filter dropdown to show only the categories you need.

#### Output Events

| Event | Signature | Description |
|---|---|---|
| `onMessage` | `UnityEvent<string>` | Fires when the AI's response text is complete. |
| `onRefusal` | `UnityEvent<string>` | Fires when the model refuses to respond. |
| `onReasoning` | `UnityEvent<string>` | Fires when reasoning text is complete (reasoning models only). |
| `onReasoningSummary` | `UnityEvent<string>` | Fires when the reasoning summary is complete. |
| `onInputTranscript` | `UnityStreamEvent<string>` | Fires when speech-to-text transcription is complete. |
| `onOutputTranscript` | `UnityStreamEvent<string>` | Fires when text-to-speech transcript is available. |

#### Conversation Events

| Event | Signature | Description |
|---|---|---|
| `onConversationCreated` | `UnityEvent<Conversation>` | A new conversation was created. |
| `onConversationLoaded` | `UnityEvent<Conversation>` | A conversation was loaded from storage. |
| `onConversationDeleted` | `UnityEvent<bool>` | A conversation was deleted. |
| `onConversationsLoaded` | `UnityEvent<Conversation[]>` | Multiple conversations loaded from storage. |
| `onConversationItemsLoaded` | `UnityEvent<ConversationItem[]>` | Items in a conversation were loaded. |
| `onConversationTitleUpdated` | `UnityEvent<string>` | Conversation title changed. |
| `onConversationSummaryUpdated` | `UnityEvent<string>` | Conversation summary changed. |

#### Agent Events

| Event | Signature | Description |
|---|---|---|
| `onAgentStatusChanged` | `UnityEvent<AgentStatus>` | Agent lifecycle state changed. |
| `onAgentDataSaved` | `UnityEvent<AgentSaveData>` | Agent save data was written. |
| `onToolStatusChanged` | `UnityEvent<ToolStatusEvent>` | A tool's status changed (filter by `ToolStatusEvent.Type`). |

#### Utility Events

| Event | Signature | Description |
|---|---|---|
| `onUsageReceived` | `UnityEvent<Usage>` | Token usage statistics received. |
| `onError` | `UnityEvent<Exception>` | An error occurred during agent processing. |

## Key Public API

### Properties

| Property | Type | Description |
|---|---|---|
| `Agent` | `Agent` | Underlying runtime agent instance. |
| `Status` | `AgentStatus` | Current lifecycle state. |
| `IsInitialized` | `bool` | Whether initialization is complete. |
| `Model` | `Model` | Active generative model (read/write). |
| `Instructions` | `string` | System instructions (read/write, session only). |
| `Conversation` | `Conversation` | Active conversation. |
| `Messages` | `List<Message>` | Filtered list of user/assistant messages. |
| `LastMessage` | `Message` | Most recent message. |

### Methods

```csharp
// Initialization
UniTask InitializeAsync(CancellationToken ct = default)
void Initialize()

// Send input
UniTask SendAsync(string message, CancellationToken ct = default)
UniTask SendAsync(AudioClip clip, CancellationToken ct = default)

// Conversation management
UniTask CreateNewConversationAsync()
UniTask LoadConversationAsync(string conversationId)
UniTask DeleteConversationAsync(string conversationId)

// Control
void Cancel()
```

## Tool Auto-Discovery

`AgentBehaviour` automatically finds all `ToolManagerBase` and `IFunctionExecutor` / `ILocalShellExecutor` / `IComputerUseExecutor` / `ICustomToolExecutor` components attached to the same `GameObject` or any of its children using `GetComponentsInChildren`. There is no need to manually register tool managers.

> **Tip:** Place all tool managers as child GameObjects of the Agent for clean hierarchy organization.
