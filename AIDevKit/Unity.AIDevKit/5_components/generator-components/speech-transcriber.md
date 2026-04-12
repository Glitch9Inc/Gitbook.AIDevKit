---
icon: microphone-lines
---

# Transcriber

`Transcriber` converts speech (microphone or `AudioClip`) to text. It automatically pairs with a `SpeechToTextRecorder` component on the same `GameObject`.

## Component Menu

```
AI Dev Kit / AI Generators / Transcriber
```

{% hint style="info" %}
`Transcriber` requires a `SpeechToTextRecorder` on the same `GameObject`. It is added automatically if missing.
{% endhint %}

## Inspector Fields

| Field | Description |
|---|---|
| **Settings** | `TranscriptionSettings` asset — model, spoken language |
| **Recorder** | `SpeechToTextRecorder` (auto-assigned from same GameObject) |
| **Save Outputs** | Persist transcription results to the output folder |

## Events

| Event | Signature | Description |
|---|---|---|
| `onOutput` | `UnityEvent<string>` | Fires with the completed transcription text. |
| `onStream` | `UnityStreamEvent<string>` | Fires for each text delta during streaming transcription. |
| `onStatusChanged` | `UnityEvent<ResponseStatus>` | Fires on status change. |

## Properties

| Property | Type | Description |
|---|---|---|
| `IsRecording` | `bool` | True while the microphone is recording. |
| `Microphone` | `string` | Get or set the active microphone device name. |

## Methods

| Method | Description |
|---|---|
| `StartRecording()` | Start microphone capture. |
| `StopRecordingAndGenerateAsync()` | Stop recording, transcribe, and return the text. |
| `Generate(AudioClip clip)` | Transcribe an existing `AudioClip`. |
| `GenerateAsync(AudioClip clip)` | Awaitable version. Returns `Generated<Transcript>`. |

## Code Example

```csharp
using Glitch9.AIDevKit.Generators;
using Cysharp.Threading.Tasks;
using UnityEngine;

public class TranscribeExample : MonoBehaviour
{
    [SerializeField] private Transcriber transcriber;

    public void StartListening() => transcriber.StartRecording();

    public async void StopAndTranscribe()
    {
        string text = await transcriber.StopRecordingAndGenerateAsync();
        Debug.Log("Transcribed: " + text);
    }
}
```
