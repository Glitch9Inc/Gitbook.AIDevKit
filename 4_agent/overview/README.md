---
description: What an Agent is and how the system is structured
icon: robot
---

# Overview

The **Agent** is the core building block of AIDevKit. It connects your Unity project to any supported AI provider, manages the full conversation lifecycle, and handles everything from streaming responses to tool calls.

***

## What Is an Agent?

Think of an Agent as an AI character or assistant living in your Unity scene. You give it:

* **Instructions** — how it should behave and what role it plays
* **A model** — which AI (GPT-4o, Gemini, Claude, etc.) powers it
* **Tools** _(optional)_ — functions it can invoke to interact with your game world
* **Conversation memory** _(optional)_ — how much context it remembers

The Agent handles everything else: making API calls, streaming tokens, executing tool calls, persisting conversation history, and dispatching Unity events your UI can react to.

***

## Before v4: Multiple Agent Types

In earlier versions of AIDevKit, you had to choose between several specialized classes:

```
[Old Design - Deprecated]
ChatAgent        → text chat only
VoiceAgent       → voice input / output
AssistantsAgent  → OpenAI Assistants API
```

Switching from text chat to voice meant replacing your entire Agent class, and sharing logic between types was awkward.

***

## v4+: One Agent Class

Starting with v4, all of those types are merged into a **single `Agent` class**. Behavior is driven by configuration, not inheritance:

```
[New Design]
Agent ─┬─ ChatCompletions API  (text chat, widest provider support)
       ├─ Responses API        (text + tools, OpenAI stateful)
       ├─ Assistants API       (OpenAI thread-based)
       └─ Realtime API         (voice + WebSocket)
```

You switch modes by changing `ChatApiType` in `AgentSettings`. No code changes needed.

{% hint style="info" %}
**In Unity scenes, you don't use `Agent` directly.** You attach the `AgentBehaviour` MonoBehaviour to a GameObject. It creates and manages the `Agent` for you.
{% endhint %}

***

## The Two Key Assets

Every Agent in Unity is configured through two ScriptableObjects:

| Asset               | Purpose                                                                         |
| ------------------- | ------------------------------------------------------------------------------- |
| **`AgentSettings`** | Technical config: API type, model, audio flags, conversation settings           |
| **`AgentProfile`**  | Identity config: agent name, instructions, personality traits, starting message |

You assign both to the `AgentBehaviour` component in the Inspector. Because they are separate assets, you can swap personalities or backends independently — and the same `AgentProfile` can be shared across multiple agents.

***

## What's in This Section

| Page                                                      | What you'll learn                                              |
| --------------------------------------------------------- | -------------------------------------------------------------- |
| [How Agent Works](how-agent-works.md)                     | Internal architecture and the lifecycle of a message           |
| [Creating Your First Agent](creating-your-first-agent.md) | Step-by-step setup guide from scratch                          |
| [Memory (RAG)](memory.md)                                 | Conversation history, context windows, and long-term retrieval |

Learn the fundamentals of working with agents:

* [How Agents Work](../../AIDevKit/Unity.AIDevKit/4_agent/overview/how-agents-work.md) - Core concepts and architecture
* [Your First Agent](../../AIDevKit/Unity.AIDevKit/4_agent/overview/your-first-agent.md) - Quick start guide
* [Agent vs Request](../../AIDevKit/Unity.AIDevKit/4_agent/overview/agent-vs-request.md) - When to use agents vs direct requests

## Core Components

Dive into the agent system's building blocks:

* [Agent Services](../../AIDevKit/Unity.AIDevKit/4_agent/overview/agent-services.md) - Service architecture and dependencies
* [Controllers](../../AIDevKit/Unity.AIDevKit/4_agent/overview/controllers.md) - Conversation, audio, and image controllers
* [Event Router](../../AIDevKit/Unity.AIDevKit/4_agent/overview/event-router.md) - Advanced event routing and filtering

## Customization

Extend agents with custom implementations:

* [Custom Chat Services](../../AIDevKit/Unity.AIDevKit/4_agent/overview/custom-chat-services.md) - Integrate custom LLM providers

## Architecture

```
Agent
 ├─ AgentControlHub (Status & Events)
 ├─ AgentChatApiAdapter (API Communication)
 ├─ ConversationController (History Management)
 ├─ AudioController (Voice I/O)
 ├─ ImageController (Visual Generation)
 └─ ToolController (Function Calling)
```

## Key Features

### Unified API

Interact with any LLM provider using a consistent interface:

* OpenAI (Chat Completions, Assistants, Responses, Realtime)
* Google Gemini
* Anthropic Claude
* And more...

### Stateful Conversations

Maintain context across multiple interactions:

* Automatic history management
* Multiple conversation support
* Persistent storage options

### Multimodal Support

Handle text, voice, images, and more:

* Voice input/output
* Image generation
* Document understanding
* Vision capabilities

### Tool Integration

Extend agent capabilities with tools:

* Built-in tools (search, calculation, etc.)
* Custom tool creation
* MCP (Model Context Protocol) support
* Automatic function calling

### Event System

Monitor and respond to agent activities:

* Status changes
* Streaming updates
* Tool execution
* Conversation events

## Next Steps

Start building with agents:

1. Create your first agent with [Your First Agent](../../AIDevKit/Unity.AIDevKit/4_agent/overview/your-first-agent.md)
2. Understand the architecture in [How Agents Work](../../AIDevKit/Unity.AIDevKit/4_agent/overview/how-agents-work.md)
3. Learn about [Agent Services](../../AIDevKit/Unity.AIDevKit/4_agent/overview/agent-services.md)
4. Explore [Event Router](../../AIDevKit/Unity.AIDevKit/4_agent/overview/event-router.md) for advanced patterns
