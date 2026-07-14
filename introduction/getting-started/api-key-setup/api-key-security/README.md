---
description: >-
  Understand the risks of client-side API keys and how AIDevKit helps protect
  them
---

# API Key Security

API keys are the credentials that authenticate your requests to AI providers. Keeping them safe is critical - a leaked key means unauthorized actors can make API calls billed to your account, potentially causing large charges or quota exhaustion.

This section covers:

* [**Client-Side Keys**](client-side-keys.md) - The inherent risks of including API keys in a deployed application, and how to minimize exposure when a server-side approach is not an option.
* [**How AIDevKit Protects Your Keys**](how-aidevkit-protects-your-keys.md) - A detailed look at the `SecureToken` obfuscation system built into AIDevKit and its limits.
* [**Best Practices**](best-practices.md) - Actionable recommendations for reducing risk at every layer.
* [**Enterprise Proxy Server Security**](../../../enterprise-proxy-server-security/) - Enterprise-first architecture where provider keys stay on your server instead of client builds.
