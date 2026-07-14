---
---

# Image Generator

`ImageGenerator` generates `Texture2D` images from text prompts. It supports OpenAI (DALL·E 3, GPT-Image-1) and Google Imagen providers.

## Component Menu

```
AI Dev Kit / AI Generators / Image Generator
```

## Inspector Settings

| Field | Description |
|---|---|
| **Settings** | `ImageGenerationSettings` asset — model, size, quality, style |
| **Save Outputs** | Persist generated images to the output folder |
| **Output Path** | Folder path for saved images |

### ImageGenerationSettings fields

| Field | Description |
|---|---|
| **Model** | Image generation model (e.g. `dall-e-3`, `gpt-image-1`, `imagen-3.0`) |
| **Size** | Output image size (e.g. `1024x1024`) |
| **Quality** | `Standard` or `HD` (OpenAI models) |
| **Style** | `Vivid` or `Natural` (DALL·E 3 only) |
| **Aspect Ratio** | Portrait / Landscape / Square (Google Imagen) |
| **Person Generation** | Allow or block human faces (Google Imagen) |

## Events

| Event | Signature | Description |
|---|---|---|
| `onOutput` | `UnityEvent<Texture2D>` | Fires when the final image is ready. |
| `onStream` | `UnityStreamEvent<Texture2D>` | Fires on each streaming frame (if supported by model). |
| `onStatusChanged` | `UnityEvent<ResponseStatus>` | Fires on status change. |

## Methods

| Method | Description |
|---|---|
| `Generate(string prompt)` | Fire-and-forget generation from a text prompt. |
| `GenerateAsync(string prompt)` | Awaitable version. Returns `Generated<IImageAsset>`. |
| `Configure(ImageGenerationSettings)` | Override inspector settings at runtime. |

## Code Example

```csharp
using Glitch9.AIDevKit.Generators;
using Cysharp.Threading.Tasks;
using UnityEngine;
using UnityEngine.UI;

public class ImageGenExample : MonoBehaviour
{
    [SerializeField] private ImageGenerator imageGenerator;
    [SerializeField] private RawImage displayImage;

    private void Start()
    {
        imageGenerator.onOutput.AddListener(tex => displayImage.texture = tex);
    }

    public void OnGenerateButtonClicked(string prompt)
    {
        imageGenerator.Generate(prompt);
    }
}
```
