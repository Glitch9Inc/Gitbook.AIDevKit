---
icon: waveform-lines
---

# Text to Music

Generate a full musical track from a text description using `.GENMusic()`.

## Basic Usage

```csharp
IAudioAsset music = await "Upbeat electronic background music with a driving beat"
    .GENMusic()
    .ExecuteAsync();

AudioClip clip = await music.ToAudioClipAsync();
audioSource.clip = clip;
audioSource.Play();
```

## Input Types

### String Input

```csharp
IAudioAsset music = await "Calm ambient piano melody"
    .GENMusic()
    .ExecuteAsync();
```

### Prompt Input

```csharp
var prompt = new Prompt("Epic orchestral battle theme in the style of {composer}");
IAudioAsset music = await prompt
    .GENMusic()
    .ExecuteAsync();
```

### Alias Method

```csharp
// TextToMusic() is an alias for GENMusic()
IAudioAsset music = await "Jazz cafe background"
    .TextToMusic()
    .ExecuteAsync();
```

## Configuration

### Duration

```csharp
IAudioAsset music = await "Cinematic trailer music"
    .GENMusic()
    .SetDuration(60.0)  // 60 seconds
    .ExecuteAsync();
```

Duration range depends on the provider (typically 10 – 300 seconds).
If not set, the provider chooses an appropriate length based on the prompt.

### Vocal Type

Control whether the generated track includes vocals:

```csharp
// Instrumental only
IAudioAsset music = await "Peaceful forest ambience"
    .GENMusic()
    .SetVocalType(MusicVocalType.Instrumental)
    .ExecuteAsync();

// Allow vocals (provider default)
IAudioAsset music = await "Pop song about summer"
    .GENMusic()
    .SetVocalType(MusicVocalType.Vocal)
    .ExecuteAsync();
```

| `MusicVocalType` | Description |
|---|---|
| `Unset` | Provider decides (default) |
| `Vocal` | Include vocals if the prompt suits them |
| `Instrumental` | Instrumental track only |

### Model Selection

```csharp
using Glitch9.AIDevKit;

IAudioAsset music = await "Lofi hip-hop study beats"
    .GENMusic()
    .SetModel("elevenlabs_music_v1")
    .ExecuteAsync();
```

## ElevenLabs: Composition Plan

For fine-grained control over song structure, provide a `CompositionPlan` instead of a simple prompt.
When a `CompositionPlan` is set the `Prompt` property is ignored.

```csharp
var plan = new CompositionPlan
{
    // Define sections, instruments, BPM, key, etc.
    // See ElevenLabs documentation for the full schema.
};

IAudioAsset music = await "".GENMusic()
    .SetCompositionPlan(plan)
    .ExecuteAsync();
```

## Provider Support

| Feature | ElevenLabs | Google Lyria *(Agent)* |
|---|---|---|
| Text prompt | ✅ | ✅ |
| Duration control | ✅ | — |
| Vocal / Instrumental | ✅ | — |
| Composition plan | ✅ | — |
| Realtime streaming | — | ✅ |

{% hint style="info" %}
Google Lyria uses a bidirectional WebSocket stream and is integrated via the `LyriaMusicPlayer`
component. It is available in **AIDevKit Agent** only.
{% endhint %}

## Unity Integration Example

```csharp
using Glitch9.AIDevKit;
using Cysharp.Threading.Tasks;
using UnityEngine;

public class MusicExample : MonoBehaviour
{
    [SerializeField] private AudioSource audioSource;

    public async void GenerateAndPlay(string description)
    {
        IAudioAsset music = await description
            .GENMusic()
            .SetVocalType(MusicVocalType.Instrumental)
            .SetDuration(30.0)
            .ExecuteAsync();

        AudioClip clip = await music.ToAudioClipAsync();
        audioSource.clip = clip;
        audioSource.Play();
    }
}
```
