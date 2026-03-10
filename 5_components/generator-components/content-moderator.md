---
icon: shield
---

# Moderator

`Moderator` analyzes text input for policy violations and returns a `Moderation` result. Configure safety categories in the `ModerationSettings` inspector and wire up `onOutput` to handle the result in your UI or game logic.

## Component Menu

```
AI Dev Kit / AI Generators / Moderator
```

## Inspector Fields

| Field | Description |
|---|---|
| **Settings** | `ModerationSettings` asset — model, safety settings list |
| **Save Outputs** | Persist moderation results to the output folder |

### ModerationSettings fields

| Field | Description |
|---|---|
| **Model** | Moderation model to use |
| **Safety Settings** | List of `SafetySetting` entries — category + threshold pairs |

At least one `SafetySetting` must be configured, or generation will throw a `MalformedRequestException`.

## Events

| Event | Signature | Description |
|---|---|---|
| `onOutput` | `UnityEvent<Moderation>` | Fires when the moderation result is ready. |
| `onStatusChanged` | `UnityEvent<ResponseStatus>` | Fires on status change. |

## Methods

| Method | Description |
|---|---|
| `Generate(string text)` | Fire-and-forget moderation check. |
| `GenerateAsync(string text)` | Awaitable version. Returns `Generated<Moderation>`. |
| `Configure(ModerationSettings)` | Override inspector settings at runtime. |

## Code Example

```csharp
using Glitch9.AIDevKit.Generators;
using Cysharp.Threading.Tasks;
using UnityEngine;

public class ChatModerationExample : MonoBehaviour
{
    [SerializeField] private Moderator moderator;

    public async UniTask<bool> IsSafeAsync(string userMessage)
    {
        Generated<Moderation> result = await moderator.GenerateAsync(userMessage);
        Moderation moderation = result.FirstOrDefault();
        return moderation != null && !moderation.IsBlocked;
    }
}
```
