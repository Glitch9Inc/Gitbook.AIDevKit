---
description: Run local GGUF models through LM Studio and connect from AI Dev Kit
icon: desktop
---

# Self-Hosting with LM Studio

## Install LM Studio

Download and install **LM Studio** from [lmstudio.ai](https://lmstudio.ai/).

After installation, launch LM Studio and keep it running while testing from Unity.

***

## Download at least one model

In LM Studio, download a model (GGUF format) from the model catalog.

Use a small model first for quick verification, then switch to larger models later.

***

## Start the local server

Enable LM Studio local server mode so it exposes OpenAI-compatible endpoints.

Default endpoint is typically:

```text
http://localhost:1234
```

AI Dev Kit checks connectivity through:

```text
http://localhost:1234/v1/models
```

***

## Configure AI Dev Kit in Unity

{% hint style="info" %}
Go to **Edit > Preferences > AI Dev Kit > LM Studio**
{% endhint %}

Set:

- **Enabled**: On
- **Base URL**: `http://localhost:1234` (or your custom host/port)

Then use **Test Connection** to verify the server.

***

## Troubleshooting

- If connection fails, confirm LM Studio server mode is running.
- If you changed the port in LM Studio, update the same port in Unity.
- If model list is empty, load or download at least one local model in LM Studio.

