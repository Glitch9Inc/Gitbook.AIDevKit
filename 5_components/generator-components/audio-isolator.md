---
icon: waveform
---

# Audio Isolator

`AudioIsolator` separates and isolates specific audio sources from a mixed audio recording. It can extract vocals, instruments, or other audio elements.

## Component Menu

```
AI Dev Kit / AI Generators / Audio Isolation
```

## Inspector Settings

| Field | Description |
|---|---|
| **Settings** | `AudioIsolationSettings` asset — model and isolation options |
| **Recorder** | `SpeechToTextRecorder` for recording audio input |
| **Player** | *(Optional)* `TextToSpeechPlayer` for streaming playback |
| **Save Outputs** | Persist isolated audio to the output folder |
| **Output Path** | Folder path for saved audio |

## Recording Methods

| Method | Description |
|---|---|
| `StartRecording()` | Start recording audio from the microphone. |
| `StopRecordingAndGenerateAsync()` | Stop recording and immediately process the audio. |
| `Microphone` | Get or set the microphone device to use. |
| `IsRecording` | Check if currently recording. |

## Generation Methods

| Method | Description |
|---|---|
| `Generate(AudioClip clip)` | Fire-and-forget audio isolation from an AudioClip. |
| `GenerateAsync(AudioClip clip)` | Awaitable version. Returns `Generated<IAudioAsset>`. |
| `Configure(AudioIsolationSettings)` | Override inspector settings at runtime. |

## Events

| Event | Signature | Description |
|---|---|---|
| `onOutput` | `UnityEvent<AudioClip>` | Fires when the isolated audio is ready. |
| `onStream` | `UnityStreamEvent<AudioClip>` | Fires for each audio chunk during streaming. |
| `onStatusChanged` | `UnityEvent<ResponseStatus>` | Fires on status change. |

## Component Requirements

- **Requires**: `SpeechToTextRecorder` component (automatically added)
- **Optional**: `TextToSpeechPlayer` for real-time streaming playback

## Code Example

```csharp
using Glitch9.AIDevKit.Generators;
using UnityEngine;

public class AudioIsolationExample : MonoBehaviour
{
    public AudioIsolator audioIsolator;

    async void Start()
    {
        // Start recording from microphone
        audioIsolator.StartRecording();
        
        // Wait for user input...
        await UniTask.Delay(5000); // Record for 5 seconds
        
        // Stop recording and isolate audio
        AudioClip isolated = await audioIsolator.StopRecordingAndGenerateAsync();
        
        if (isolated != null)
        {
            Debug.Log("Audio isolated successfully!");
        }
    }
    
    // Or process existing AudioClip
    async void IsolateExistingAudio(AudioClip clip)
    {
        Generated<IAudioAsset> result = await audioIsolator.GenerateAsync(clip);
        
        if (result.IsSuccess)
        {
            Debug.Log("Audio isolated from existing clip!");
        }
    }
}
```

## Use Cases

- **Vocal Extraction**: Isolate vocals from background music
- **Instrument Separation**: Extract specific instruments from a mix
- **Noise Removal**: Remove background noise from recordings
- **Karaoke Creation**: Remove vocals to create instrumental tracks

## Related Topics

- [Audio Isolation](../../3_requests/audio/audio-isolation.md)
- [Speech to Text](../../3_requests/audio/speech-to-text.md)
- [Voice Change](../../3_requests/audio/voice-change.md)
