---
icon: waveform
---

# Voice Changer

`VoiceChanger` transforms a recorded or supplied voice `AudioClip` to a different voice using AI voice conversion. It automatically pairs with a `SpeechToTextRecorder` (for mic input) and optionally a `TextToSpeechPlayer` (for streaming playback).

## Component Menu

```
AI Dev Kit / AI Generators / Voice Changer
```

{% hint style="info" %}
`VoiceChanger` requires a `SpeechToTextRecorder` on the same `GameObject`. It is added automatically if missing.
{% endhint %}

## Inspector Fields

| Field | Description |
|---|---|
| **Settings** | `VoiceChangeSettings` asset — target voice, model |
| **Recorder** | `SpeechToTextRecorder` (auto-assigned from same GameObject) |
| **Player** | *(Optional)* `TextToSpeechPlayer` for streaming playback |
| **Save Outputs** | Persist changed voice audio to the output folder |

## Events

| Event | Signature | Description |
|---|---|---|
| `onOutput` | `UnityEvent<AudioClip>` | Fires when the full converted clip is ready. |
| `onStream` | `UnityStreamEvent<AudioClip>` | Fires for each audio chunk during streaming. |
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
| `StopRecordingAndGenerateAsync()` | Stop recording, convert voice, return `AudioClip`. |
| `Generate(AudioClip clip)` | Convert an existing `AudioClip`. |
| `GenerateAsync(AudioClip clip)` | Awaitable version. Returns `Generated<IAudioAsset>`. |

## Code Example

```csharp
using Glitch9.AIDevKit.Generators;
using Cysharp.Threading.Tasks;
using UnityEngine;

public class VoiceChangerExample : MonoBehaviour
{
    [SerializeField] private VoiceChanger voiceChanger;
    [SerializeField] private AudioSource output;

    public void StartRecording() => voiceChanger.StartRecording();

    public async void StopAndConvert()
    {
        AudioClip changed = await voiceChanger.StopRecordingAndGenerateAsync();
        output.clip = changed;
        output.Play();
    }
}
```
