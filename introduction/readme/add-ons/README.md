---
description: Optional packages that extend AI DevKit with dedicated editor tools and workflows.
---

# Add-ons

**Add-ons** are optional packages built on top of the free AI DevKit base package. Each one adds a
dedicated Unity Editor workflow — a specialized window, provider integrations, and quick actions —
while reusing the same Unified API (`SetModel(...).ExecuteAsync()`), API keys, and model library you
already configured in AI DevKit.

Install only the add-ons you need. They layer onto your existing project without changing the base
package.

## Available add-ons

<table data-view="cards">
  <thead>
    <tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>AI Sheets</strong></td>
      <td>A full spreadsheet editor inside Unity for game databases and localization, with an AI layer that generates rows/cells, translates, and runs a Spreadsheet Agent.</td>
      <td><a href="ai-sheets.md">ai-sheets.md</a></td>
    </tr>
    <tr>
      <td><strong>AI Image Studio</strong></td>
      <td>A dedicated image generation and editing window — generate, inpaint, outpaint, upscale, remove backgrounds, and more — with Scene View and Project window quick actions.</td>
      <td><a href="image-studio.md">image-studio.md</a></td>
    </tr>
    <tr>
      <td><strong>AI Pixel Studio</strong> <em>(coming soon)</em></td>
      <td>AI-powered pixel art generation, sprite animation, tilesets, and 2D rigging tools. Currently in development.</td>
      <td><a href="pixel-studio.md">pixel-studio.md</a></td>
    </tr>
  </tbody>
</table>

## How add-ons work

* **Built on the AI DevKit core.** Add-ons use the same Unified API, API keys, and model library.
  Some (such as AI Image Studio) **bundle the AI DevKit core**, so no separate AI DevKit install is
  required.
* **Same Unified API.** New request types (e.g. `GENUpscale`, `GENOutpaint`) plug into the same
  fluent `SetModel(...).ExecuteAsync()` pattern as the base package.
* **Extra providers when relevant.** Some add-ons register additional provider families (for
  example, Stability AI and Replicate for Image Studio) that appear only when compatible with the
  selected model or operation.
* **Editor-first.** Each add-on ships a dedicated Editor window plus context-menu and Scene View
  shortcuts, so common tasks stay inside Unity.
