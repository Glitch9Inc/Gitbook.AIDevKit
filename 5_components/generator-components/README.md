---
icon: lemon
---

# Generator Components

Generator Components are standalone `MonoBehaviour` scripts that perform a single AI task.
Attach one to a `GameObject`, configure it in the Inspector, and call `Generate()` or wire up the `onOutput` event — no scripting required for basic setups.

All generator components inherit from `GeneratorBehaviour<...>` and share this common interface:

| Member | Description |
|---|---|
| `Settings` | Inspector-exposed settings asset (model, voice, size, etc.) |
| `SaveOutputs` | Save generated outputs to the project folder |
| `onOutput` | `UnityEvent<TOutput>` fired when generation completes |
| `onStream` | `UnityStreamEvent<TOutput>` fired on each streaming chunk |
| `onStatusChanged` | `UnityEvent<ResponseStatus>` fired on status change |
| `Generate(input)` | Starts generation (fire-and-forget, safe from UI buttons) |
| `GenerateAsync(input)` | Awaitable version returning `Generated<TResult>` |

## Available Components

| Component | Input | Output | Menu Path |
|---|---|---|---|
| [Image Generator](image-generator.md) | `string` prompt | `Texture2D` | AI Dev Kit / AI Generators / Image Generator |
| [Speech Generator](speech-generator.md) | `string` text | `AudioClip` | AI Dev Kit / AI Generators / Speech Generator |
| [Transcriber](speech-transcriber.md) | `AudioClip` / mic | `string` | AI Dev Kit / AI Generators / Transcriber |
| [Voice Changer](voice-changer.md) | `AudioClip` / mic | `AudioClip` | AI Dev Kit / AI Generators / Voice Changer |
| [Moderator](content-moderator.md) | `string` text | `Moderation` | AI Dev Kit / AI Generators / Moderator |
