---
description: Enterprise proxy server security guide for AIDevKit Gateway
---

# Enterprise Proxy Server Security

If your app is distributed to real users, the most secure production pattern is:

- Unity client does not contain upstream provider API keys
- Your gateway server stores provider keys in server-side secrets
- Unity calls only your gateway endpoint (for example `/v1/proxy`)

This section explains what the gateway server is, why Enterprise teams use it, and how to run it with the built-in wizard.

## Read this section in order

1. [What Is The Gateway Server?](what-is-a-proxy-server.md)
2. [Why Enterprise Teams Use It](why-enterprise-uses-proxy.md)
3. [Run It With Wizard (Local Test)](run-with-wizard-local-test.md)

## Scope

This guide focuses on the Enterprise server-gateway workflow available in Unity editor:

- `Window/AI Dev Kit/Server Gateway (Enterprise)/Server Gateway Export Wizard`

For quick security background, see [API Key Security](../api-key-security/README.md).

Each sub-guide includes inline screenshot placeholders so you can replace them with real captures while authoring.
