---
description: Why and how to deploy an Enterprise proxy server to keep provider keys off Unity clients
---

# Enterprise Proxy Server Security

If your project handles paid AI traffic in production, the safest model is:

- Unity client does not store provider API keys
- Your backend proxy server stores provider keys in server-side secrets
- Unity sends requests only to your proxy endpoint

This feature is intended for **Enterprise** deployments where security, compliance, and centralized control matter.

---

## Why use a proxy server

Client-side obfuscation (`SecureToken`) raises the attack cost, but the key still exists on end-user devices.

A proxy architecture removes that exposure:

- Provider keys stay on infrastructure you control
- End users cannot extract provider keys from game/app binaries
- You can rotate keys without shipping a client update
- You can enforce policy centrally (allowlist, routing, rate limits, fail-closed behavior)

---

## Security and operations goals

An Enterprise proxy is useful when you need:

- API key protection: no provider key in shipped clients
- Spend control: per-route and per-tenant throttling
- Governance: approved provider/model routing only
- Auditability: request logs and incident response traces
- Compliance posture: separation between client app and secret material

---

## Recommended request flow

1. Unity sends request to your gateway (`/v1/proxy`)
2. Gateway validates client auth/token
3. Gateway selects upstream provider by policy
4. Gateway attaches provider API key from server secret store
5. Gateway forwards request and returns normalized response

Only step 4 touches provider keys, and it happens on server infrastructure.

---

## What this protects vs. what it does not

Protected:

- Provider API keys from client reverse-engineering
- Direct abuse of your provider accounts via leaked client binaries

Not automatically protected:

- Stolen client auth tokens (protect with short-lived tokens + rotation)
- Prompt/data abuse from authenticated users (protect with quotas and moderation)
- Backend misconfiguration (protect with secret management and least privilege)

---

## Deployment checklist (high level)

- Deploy gateway over HTTPS only
- Store upstream API keys in environment secrets or secret manager
- Enable gateway auth for Unity clients
- Restrict upstream targets to registered/approved providers
- Configure rate limits and alerting
- Log request metadata (never raw secrets)
- Keep fail-closed behavior for production

---

## Relationship with API Key Security guides

- If you cannot run a proxy yet, use `SecureToken` and hard key restrictions
- If you can run Enterprise infrastructure, proxy mode is the recommended production architecture

See also:

- [Client-Side Keys](api-key-security/client-side-keys.md)
- [How AIDevKit Protects Your Keys](api-key-security/how-aidevkit-protects-your-keys.md)
- [Best Practices](api-key-security/best-practices.md)
