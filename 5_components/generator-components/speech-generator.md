---
---

# Speech Generator

`SpeechGenerator` converts text to speech, producing an `AudioClip`. Optionally pairs with a `TextToSpeechPlayer` to stream audio in real time as chunks arrive.

## Component Menu

```
AI Dev Kit / AI Generators / Speech Generator
```

## Inspector Fields

| Field | Description |
|---|---|
| **Settings** | `SpeechGenerationSettings` asset — model and voice |
| **Player** | *(Optional)* `TextToSpeechPlayer` for real-time streaming playback |
| **Save Outputs** | Persist generated audio to the output folder |

## Events

| Event | Signature | Description |
|---|---|---|
| `onOutput` | `UnityEvent<AudioClip>` | Fires when the complete audio is ready. |
| `onStream` | `UnityStreamEvent<AudioClip>` | Fires for each audio chunk during streaming. |
| `onStatusChanged` | `UnityEvent<ResponseStatus>` | Fires on status change. |

## Methods

| Method | Description |
|---|---|
| `Generate(string text)` | Fire-and-forget TTS generation. |
| `GenerateAsync(string text)` | Awaitable version. Returns `Generated<IAudioAsset>`. |
| `Configure(SpeechGenerationSettings)` | Override inspector settings at runtime. |

## Streaming Playback

When a `TextToSpeechPlayer` is assigned, audio is streamed to the player chunk by chunk. The player stitches audio buffers in real time so playback starts immediately without waiting for the entire clip.

## Code Example

```csharp
using Glitch9.AIDevKit.Generators;
using UnityEngine;

public class TTSExample : MonoBehaviour
{
    [SerializeField] private SpeechGenerator speechGenerator;

    public void Speak(string text)
    {
        speechGenerator.Generate(text);
    }
}
```
