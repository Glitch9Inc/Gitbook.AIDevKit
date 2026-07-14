---
description: Configure Sherpa-ONNX for offline speech-to-text and text-to-speech
---

# Setting Up Sherpa-ONNX (Offline STT/TTS)

`Sherpa-ONNX` is a local runtime for speech features.

Use it when you want offline:

- Speech-to-Text (STT)
- Text-to-Speech (TTS)

No cloud API key is required.

***

## 1. Prepare native runtime files

Make sure Sherpa-ONNX native binaries are available for your target platform.

For Windows x64, the required files are:

- `sherpa-onnx-c-api.dll`
- `onnxruntime.dll`

***

## 2. Download STT/TTS model files

Download pretrained models from Sherpa-ONNX model pages:

- STT models: <https://k2-fsa.github.io/sherpa/onnx/pretrained_models/index.html>
- TTS models: <https://k2-fsa.github.io/sherpa/onnx/tts/pretrained_models/index.html>

***

## 3. Place models in your Unity project

Recommended structure:

```text
Assets/StreamingAssets/AIDevKit/SherpaOnnx/STT
Assets/StreamingAssets/AIDevKit/SherpaOnnx/TTS
```

Put the extracted model files into each folder.

***

## 4. Configure AI Dev Kit in Unity

{% hint style="info" %}
Go to **Edit > Preferences > AI Dev Kit > Sherpa-ONNX**
{% endhint %}

Set:

- **STT Model Root Path**: your STT model folder
- **TTS Model Root Path**: your TTS model folder
- Optional model names and runtime options as needed

***

## 5. Verify setup

- Check that model paths resolve correctly.
- Run a short STT test clip.
- Run a short TTS sample text playback.

If setup fails, verify model file completeness and platform-native plugin import settings first.

