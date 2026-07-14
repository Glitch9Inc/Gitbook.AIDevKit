---
description: Generate, edit, and clean up image assets directly inside the Unity Editor.
---

# AI Image Studio

**AI Image Studio** lets you generate, edit, clean up, and reuse image assets directly inside the
Unity Editor. Instead of switching between external AI tools, image editors, background removers, and
Unity import workflows, you handle common image production tasks in one place — and save results
straight back into your project folder.

Use it for character concepts, item icons, UI graphics, backgrounds, sprites, transparent cutout
assets, prototype textures, and placeholder art.

> Built on the free AI DevKit base package.

## Extra providers

AI Image Studio adds **2 provider integrations — Stability AI and Replicate** — as provider families
in the same Unified API workflow (`SetModel(...).ExecuteAsync()`). Provider-specific options (Style
Preset, Inference Steps, Grow Mask, Creativity, etc.) are exposed in the editor only when compatible
with the selected model and operation.

## The AI Image Studio window

A dedicated Unity Editor window organizes every operation:

* **Generation** — Text-to-Image, Image-to-Image
* **Editing** — Inpaint, Outpaint, Search and Recolor, Search and Replace, Erase
* **Guided generation** — Sketch, Structure, Style Guide, Style Transfer
* **Utilities** — Upscale, Remove Background, Replace Background and Relight

The window validates inputs per operation (prompt required for generation, source image required for
everything except Text-to-Image, painted mask required for Inpaint/Erase, object-target + edit
prompts required for Search and Recolor/Replace), and provides mask painting (left-drag paint,
right-drag erase), outpaint canvas-expansion handles, and live cancellable progress polling.

Save is tuned for iteration: generate ops save into the active folder (or default path) with optional
**Save As**; edit ops support **overwrite-original** or **Save As**; and after saving, the result can
become the new source texture for the next edit.

## New Unified API request types

Image Studio adds **11 request classes** on top of the base package's `ImageGenerationRequest` and
`ImageInpaintingRequest`:

| Request | Extension | Purpose |
| --- | --- | --- |
| Outpaint | `GENOutpaint(...)` | Expand an image beyond its bounds and generate the extended area |
| Upscale | `GENUpscale(...)` | Increase resolution with AI upscaling and detail enhancement |
| Background Removal | `GENBackgroundRemoval(...)` | Segment foreground and remove the background |
| Search and Recolor | `GENSearchAndRecolor(...)` | Find an object by prompt and recolor it, no manual mask |
| Search and Replace | `GENSearchAndReplace(...)` | Find an object by prompt and replace it, no manual mask |
| Erase | `GENErase(...)` | Remove selected regions/objects |
| Sketch | `GENSketch(...)` | Use a sketch/contour image as generation guidance |
| Structure | `GENStructure(...)` | Use a structure/control image as generation guidance |
| Style Guide | `GENStyleGuide(...)` | Use a style reference image to guide generation |
| Style Transfer | `GENStyleTransfer(...)` | Transfer style from a reference onto the source image |
| Replace Background & Relight | `RequestType.ImageReplaceBgAndRelight` | Replace the background and relight the subject |

## Editor quick actions

* **Scene View overlay** — for selected GameObjects with a `SpriteRenderer`, `UI.Image`, or
  `UI.RawImage`, an *AI Image Studio* overlay adds one-click **Generate**, **Edit**, **Upscale**,
  **Apply Style**, and **Remove BG** buttons. Toggle their visibility under
  *Project Settings → AI DevKit → Image Studio → Scene View*.
* **Project window — folder context menu** — `Assets/AI Image Studio/Generate in AI Image Studio`
  opens the editor pre-targeted to the active folder.
* **Project window — image asset context menu** — with a single `Texture2D` selected, run one-click
  **Upscale** or **Remove Background** (with a before/after **Overwrite** / **Save As** dialog), or
  **Edit in AI Image Studio** to open the full editor with the texture preloaded.

## Requirements

* AI DevKit base package (Lite or higher)
* Unity 6 (6000.x)

## Links

<!-- TODO: replace with the AI Image Studio Asset Store URL once available -->
<!-- {% embed url="https://assetstore.unity.com/packages/..." %} -->

<!-- TODO: replace with the AI Image Studio documentation URL once available -->
<!-- {% embed url="https://glitch9.gitbook.io/..." %} -->
