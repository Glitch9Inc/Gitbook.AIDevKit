---
description: Understand the risks of client-side API keys and how AIDevKit helps protect them
icon: shield
---

# API Key Security

API keys are the credentials that authenticate your requests to AI providers. Keeping them safe is critical — a leaked key means unauthorized actors can make API calls billed to your account, potentially racking up large charges or exhausting your quota.

This section covers:

* [**Client-Side Keys**](client-side-keys.md) — The inherent risks of including API keys in a deployed application, and how to minimize exposure when a server-side approach is not an option.
* [**How AIDevKit Protects Your Keys**](how-aidevkit-protects-your-keys.md) — A detailed look at the `SecureToken` encryption system built into AIDevKit, and its honest limitations.
* [**Best Practices**](best-practices.md) — Actionable recommendations for reducing risk at every layer.
