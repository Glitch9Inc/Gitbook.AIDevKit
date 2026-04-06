---
icon: cube
---

# AI Dev Kit 5.1.0

<figure><img src=".gitbook/assets/Cover Image.png" alt=""><figcaption></figcaption></figure>

## The Ultimate AI Suite for Unity

**AI Dev Kit** empowers beginner developers to effortlessly integrate advanced AI functionalities directly into Unity, dramatically simplifying your game development workflow.

With just a few clicks and simple text prompts, you can create code, generate images, produce sound effects, and even synthesize voices. **AI Dev Kit** offers broad API integrations, rich editor tools, extensive voice synthesis options, and unique audio generation capabilities.

***

### From Prompt to Output in One Line

AI Dev Kit gives you instant access to text, image, audio, and code generation — with zero boilerplate.

```csharp
// Write AI-powered NPC backstories
string response = await "Describe a stoic robot farmer on Mars."
    .GENResponse()
    .ExecuteAsync();

// Generate stylized images instantly
Texture2D image = "A rusty sci-fi door with bullet holes"
    .GENImage()
    .ExecuteAsync();

// Create voiceover from text
AudioClip voice = "Welcome back, Commander."
    .GENSpeech()
    .SetVoice(ElevenLabsVoice.Rachel)
    .ExecuteAsync();
```

Just call `.GEN*()` on your Unity objects and chain your desired behavior — it's fast, readable, and production-ready.

Works out of the box. Fully extensible. No extra SDKs required.

***

### **Supported Platforms**

<table><thead><tr><th width="100">Supported</th><th>Platform</th><th data-hidden>All OpenAI APIs (2025.05.18)</th></tr></thead><tbody><tr><td><mark style="color:green;">Fully</mark></td><td>Windows, OSX, Linux</td><td><span data-gb-custom-inline data-tag="emoji" data-code="1f4ac">💬</span> Chat Completions, Completions (Legacy)</td></tr><tr><td><mark style="color:yellow;">Partially (integrated but unstable – testers welcome)</mark></td><td>Unity WebGL</td><td></td></tr><tr><td><mark style="color:green;">Fully</mark></td><td>Android, iOS, Windows Phone/Store</td><td></td></tr><tr><td><mark style="color:green;">Fully</mark></td><td>PlayStation, Xbox, PS Vita/PSM, Switch</td><td></td></tr></tbody></table>
