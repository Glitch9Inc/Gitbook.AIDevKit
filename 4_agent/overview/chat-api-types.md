# Chat API Types

AI Dev Kit supports multiple chat API types, each designed for different use cases and capabilities. Understanding the differences between these API types will help you choose the right one for your agent.

## Overview

There are four main chat API types available:

| API Type | Provider | Best For | Real-time | State Management |
|----------|----------|----------|-----------|------------------|
| **Chat Completions** | Multiple | General purpose chatbots | No | Stateless |
| **Responses** | OpenAI | Enhanced chat with structured outputs | No | Stateless |
| **Assistants** | OpenAI | Persistent conversations with tools | No | Stateful |
| **Realtime** | OpenAI | Voice-based interactions | Yes | Stateful |

---

## Chat Completions API

**Provider:** OpenAI, Google, Anthropic, and many others

The Chat Completions API is the most widely supported and flexible option. It provides a straightforward request-response pattern for text-based conversations.

### Key Features
- **Multi-provider support** - Works with most AI providers
- **Stateless** - You manage conversation history
- **Function calling** - Supports tool usage
- **Vision support** - Can process images (with compatible models)
- **Streaming** - Real-time response streaming

### When to Use
- Building general-purpose chatbots
- Need multi-provider flexibility
- Want full control over conversation state
- Building custom conversation management

### Learn More
[Chat Completions API Reference](https://developers.openai.com/api/reference/chat-completions/overview)

---

## Responses API

**Provider:** OpenAI only

The Responses API is an enhanced version of Chat Completions with better structured output support and improved reliability.

### Key Features
- **Structured outputs** - Better JSON schema compliance
- **Improved reliability** - More consistent responses
- **Function calling** - Enhanced tool integration
- **Stateless** - Similar to Chat Completions

### When to Use
- Need reliable structured data extraction
- Working with complex JSON schemas
- Building data-driven applications
- Require high consistency in outputs

### Differences from Chat Completions
- Better adherence to JSON schemas
- More reliable function calling
- Enhanced output formatting
- Currently OpenAI-only

### Learn More
[Responses API Reference](https://developers.openai.com/api/reference/responses/overview)

---

## Assistants API

**Provider:** OpenAI only

The Assistants API provides built-in state management and advanced features for creating persistent AI assistants.

### Key Features
- **Stateful** - Built-in conversation persistence
- **Thread management** - Automatic message history
- **Code Interpreter** - Can run Python code
- **File search** - Built-in RAG (Retrieval Augmented Generation)
- **Function calling** - Tool integration support
- **Multi-turn conversations** - Persistent context across sessions

### When to Use
- Building persistent AI assistants
- Need built-in state management
- Want code execution capabilities
- Require file search/RAG out of the box
- Multi-session conversations

### Trade-offs
- OpenAI-only (no multi-provider support)
- Higher latency than Chat Completions
- Additional complexity
- Usage costs may be higher

### Learn More
[Assistants API Overview](https://platform.openai.com/docs/assistants/overview)

---

## Realtime API

**Provider:** OpenAI only

The Realtime API enables low-latency, voice-based interactions with multimodal capabilities.

### Key Features
- **WebSocket-based** - Persistent bi-directional connection
- **Voice input/output** - Native speech-to-speech
- **Low latency** - Near real-time interactions
- **Function calling** - Tool integration during conversations
- **Interruption handling** - Can be interrupted mid-response
- **Multimodal** - Text and audio in same conversation

### When to Use
- Building voice assistants
- Need real-time interactions
- Voice-to-voice conversations
- Interactive applications with low latency requirements
- Natural speech interruptions

### Technical Requirements
- WebSocket connection management
- Audio streaming handling
- More complex integration
- Requires audio processing

### Learn More
[Realtime API Guide](https://platform.openai.com/docs/guides/realtime)

---

## Comparison & Selection Guide

### Choose **Chat Completions** if you:
- Want the widest provider support
- Need flexibility and control
- Building text-based chatbots
- Want to manage your own state

### Choose **Responses** if you:
- Need reliable structured outputs
- Working with complex data schemas
- Require high consistency
- Can use OpenAI exclusively

### Choose **Assistants API** if you:
- Need persistent conversations
- Want built-in state management
- Require code execution or file search
- Don't need multi-provider support

### Choose **Realtime API** if you:
- Building voice assistants
- Need low-latency interactions
- Want speech-to-speech conversations
- Can handle WebSocket complexity

---

## Implementation in AI Dev Kit

In AI Dev Kit, you can configure the chat API type in your Agent Profile:

```csharp
// The agent profile determines which API type is used
var profile = ScriptableObject.CreateInstance<AgentProfile>();
profile.ChatApiType = ChatApi.ChatCompletions; // or Responses, Assistants, Realtime
```

The API type affects:
- Available providers
- Tool compatibility
- State management behavior
- Latency and performance
- Feature availability

---

## Next Steps

- Learn about [Tool Compatibility](../../tools/what-are-tools.md) with different API types
- Explore [Creating Your First Agent](creating-your-first-agent.md)
- Understand [Agent Events](../../events/overview.md)
- Read about [Conversation Management](../../conversations/how-conversations-work.md)
