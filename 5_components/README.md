---
---

# Components

AIDevKit provides a set of Unity components that you attach to GameObjects to integrate AI capabilities into your scene. Components are organized into two categories:

## AI Generators

Standalone generator components that perform a single AI task (image generation, speech synthesis, transcription, etc.) without requiring a full agent runtime.

* [Image Generator](image-generator.md)
* [Speech Generator](speech-generator.md)
* [Speech Transcriber](speech-transcriber.md)
* [Voice Changer](voice-changer.md)
* [Content Moderator](content-moderator.md)

## Agent Components

Components for building fully conversational AI agents with tool use, streaming, and conversation management.

* [Agent Behaviour](agent-behaviour.md) — Core agent runtime component.
* [Tool Managers](tool-managers.md) — Extend the agent with web search, file search, MCP, image generation, code interpretation, function calls, shell commands, computer use, and custom tools.
