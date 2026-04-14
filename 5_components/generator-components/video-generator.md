---
---

# Video Generator

`VideoGenerator` generates `VideoTexture` videos from text prompts. It supports OpenAI and Google Veo providers.

## Component Menu

```
AI Dev Kit / AI Generators / Video Generator
```

## Inspector Settings

| Field | Description |
|---|---|
| **Settings** | `VideoGenerationSettings` asset — model, size, aspect ratio |
| **Save Outputs** | Persist generated videos to the output folder |
| **Output Path** | Folder path for saved videos |

### VideoGenerationSettings fields

| Field | Description |
|---|---|
| **Model** | Video generation model (e.g. `veo-2.0`) |
| **Size** | Output video size (OpenAI models) |
| **Aspect Ratio** | Portrait / Landscape / Square (Google Veo) |
| **Person Generation** | Allow or block human faces (Google Veo) |

## Events

| Event | Signature | Description |
|---|---|---|
| `onOutput` | `UnityEvent<VideoTexture>` | Fires when the final video is ready. |
| `onStatusChanged` | `UnityEvent<ResponseStatus>` | Fires on status change. |

## Methods

| Method | Description |
|---|---|
| `Generate(string prompt)` | Fire-and-forget generation from a text prompt. |
| `GenerateAsync(string prompt)` | Awaitable version. Returns `Generated<IVideoAsset>`. |
| `Configure(VideoGenerationSettings)` | Override inspector settings at runtime. |

## Code Example

```csharp
using Glitch9.AIDevKit.Generators;
using UnityEngine;

public class VideoGenExample : MonoBehaviour
{
    public VideoGenerator videoGenerator;

    async void Start()
    {
        string prompt = "A cat playing piano";
        Generated<IVideoAsset> result = await videoGenerator.GenerateAsync(prompt);
        
        if (result.IsSuccess)
        {
            Debug.Log("Video generated successfully!");
        }
    }
}
```

## Related Topics

- [Text to Video](../../3_requests/video/text-to-video.md)
- [Image to Video](../../3_requests/video/image-to-video.md)
