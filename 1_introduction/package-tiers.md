---
icon: layer-group
---

# Package Tiers & Provider Support

AI Dev Kit is available in four tiers. Each tier unlocks additional providers, components, and features.

| Tier | Description |
|---|---|
| **Studio** | Core package — essential AI requests (text, image, audio) with the most popular providers |
| **Agent** | Adds AI Agent components, more providers, and advanced runtime features |
| **Enterprise** | Full access — all providers, all components, and exclusive features |
| **Pixel** | Pixel art generation add-on (PixelLab provider) |

***

### Provider Supports

<table><thead><tr><th width="228.7147216796875">Provider</th><th data-type="checkbox">Studio</th><th data-type="checkbox">Agent</th><th data-type="checkbox">Enterprise</th><th data-type="checkbox">Pixel</th></tr></thead><tbody><tr><td>OpenAI</td><td>true</td><td>true</td><td>true</td><td>false</td></tr><tr><td>ElevenLabs</td><td>true</td><td>true</td><td>true</td><td>false</td></tr><tr><td>Google Gemini</td><td>false</td><td>true</td><td>true</td><td>false</td></tr><tr><td>Ollama (Local Server)</td><td>false</td><td>true</td><td>true</td><td>false</td></tr><tr><td>OpenRouter</td><td>false</td><td>true</td><td>true</td><td>false</td></tr><tr><td>DeepSeek</td><td>false</td><td>true</td><td>true</td><td>false</td></tr><tr><td>Sherpa-ONNX (Local Runtime)</td><td>false</td><td>true</td><td>true</td><td>false</td></tr><tr><td>Anthropic Claude</td><td>false</td><td>true</td><td>true</td><td>false</td></tr><tr><td>GroqCloud</td><td>false</td><td>false</td><td>true</td><td>false</td></tr><tr><td>Microsoft Azure</td><td>false</td><td>false</td><td>true</td><td>false</td></tr><tr><td>Perplexity</td><td>false</td><td>false</td><td>true</td><td>false</td></tr><tr><td>xAI Grok</td><td>false</td><td>false</td><td>true</td><td>false</td></tr><tr><td>Cohere</td><td>false</td><td>false</td><td>true</td><td>false</td></tr><tr><td>Mistral</td><td>false</td><td>false</td><td>true</td><td>false</td></tr><tr><td>AI21</td><td>false</td><td>false</td><td>true</td><td>false</td></tr><tr><td>Amazon AWS</td><td>false</td><td>false</td><td>true</td><td>false</td></tr><tr><td>LM Studio (Local Server)</td><td>false</td><td>false</td><td>true</td><td>false</td></tr><tr><td>PixelLab</td><td>false</td><td>false</td><td>false</td><td>true</td></tr><tr><td>RetroDiffusion</td><td>false</td><td>false</td><td>false</td><td>true</td></tr><tr><td>Replicate</td><td>false</td><td>false</td><td>false</td><td>true</td></tr></tbody></table>

***

### AI Agents

**AI Agents** lets you use chat APIs (e.g., **Chat Completions**) in Unity via a drop-in **AIAgent** component.\
Attach it to a GameObject, pick a provider/model, and handle requests/responses through events—no custom wiring.

It also makes **tool calls** trivial: register tools, listen to **Tool Events** (status & output), and optionally pipe results back to the model. Hosted tools (web search, code interpreter, MCP, etc.) are supported with simple event hooks.

<table><thead><tr><th width="284.1431884765625">AI Agent</th><th data-type="checkbox">Studio</th><th data-type="checkbox">Agent</th><th data-type="checkbox">Enterprise</th></tr></thead><tbody><tr><td>Chatbot (Chat Completions API)</td><td>false</td><td>true</td><td>true</td></tr><tr><td>Assistant Agent (Assistants API)</td><td>false</td><td>true</td><td>true</td></tr><tr><td>Voice Agent (Realtime API)</td><td>false</td><td>true</td><td>true</td></tr><tr><td><mark style="color:yellow;"><strong>Response Agent (Responses API)</strong></mark></td><td>false</td><td>false</td><td>true</td></tr></tbody></table>

***

### AI Generators

**AI Generators** are Unity components that make it easy to use Generative AI—such as **image generation**, **voice synthesis (TTS)**, and **speech recognition**—directly in Unity. They're implemented as drop-in components, so you can add them to a GameObject and use them with simple calls or events.

<table><thead><tr><th width="284.1431884765625">AI Generator</th><th data-type="checkbox">Studio</th><th data-type="checkbox">Agent</th><th data-type="checkbox">Enterprise</th></tr></thead><tbody><tr><td>Image Generator</td><td>false</td><td>true</td><td>true</td></tr><tr><td>Speech Generator (TTS)</td><td>false</td><td>true</td><td>true</td></tr><tr><td>Speech Transcriber (STT)</td><td>false</td><td>true</td><td>true</td></tr><tr><td>Content Moderator</td><td>false</td><td>true</td><td>true</td></tr><tr><td>Voice Changer</td><td>false</td><td>true</td><td>true</td></tr><tr><td>Lyria Music Player</td><td>false</td><td>false</td><td>true</td></tr></tbody></table>
