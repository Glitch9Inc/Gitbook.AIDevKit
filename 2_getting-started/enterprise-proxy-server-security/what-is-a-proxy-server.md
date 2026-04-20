---
description: What the Enterprise gateway/proxy server is and what role it plays in AIDevKit architecture
---

# What Is The Gateway Server?

A gateway server (proxy server) is a backend service that sits between your Unity client and upstream AI providers.

Instead of this:

- Unity client -> OpenAI/Anthropic/etc directly

You run this:

- Unity client -> Your gateway -> OpenAI/Anthropic/etc

> Screenshot placeholder:
> Add architecture diagram here (Unity Client -> Gateway Server -> AI Providers).
> Suggested caption: "Enterprise routing model: clients call only your gateway; provider keys stay server-side."

## Core role in AIDevKit Enterprise

The gateway server becomes the single entry point for model traffic.

It typically handles:

- Authentication of your Unity client requests
- Provider routing (which upstream API to call)
- Header and key injection on the server side
- Policy checks (allowlist/blocklist, fail-closed behavior)
- Request/response normalization

## Why this matters for key security

If you call providers directly from client builds, key material is eventually present on user devices.

With a gateway model:

- Upstream provider keys stay in server-side secret storage
- User devices receive no upstream provider keys
- Rotating provider keys does not require a client update

## Mental model

Think of it as an API firewall + router for AI traffic:

- Firewall: validates and rejects unauthorized or out-of-policy requests
- Router: forwards only approved traffic to approved providers/models
