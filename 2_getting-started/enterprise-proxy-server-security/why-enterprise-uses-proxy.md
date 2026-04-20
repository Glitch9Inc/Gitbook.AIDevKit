---
description: Why Enterprise teams deploy a proxy server for security, governance, and operations
---

# Why Enterprise Teams Use It

## 1) Key exposure reduction

The biggest benefit is removing upstream provider keys from distributed client binaries.

- Client build compromise no longer leaks provider keys
- Reverse-engineering impact is reduced to client auth scope

## 2) Centralized policy and governance

A gateway gives one control plane for all AI calls:

- Restrict allowed providers/models
- Restrict target APIs to registered providers
- Enforce fail-closed behavior in production

## 3) Cost and abuse control

Server-side controls are easier to enforce than client-side controls:

- Global and per-route rate limits
- Per-tenant quotas/budgets
- Unified usage logging and anomaly detection

## 4) Operational flexibility

You can change backend behavior without shipping a new client:

- Rotate provider keys
- Switch providers/models
- Roll out routing policy changes

## 5) Compliance and audit posture

Many Enterprise teams need auditable traffic controls:

- Centralized logs for incident analysis
- Separation of client app and secret material
- Clearer internal security boundaries

## When this is most recommended

Strongly recommended when your app has one or more of:

- Real end users and paid model traffic
- Shared corporate billing account
- Compliance/security review requirements
- Multi-provider routing needs

## Screenshot to add

- **Capture name**: `gateway-policy-and-security-benefits`
- **Where to capture**: Wizard step or settings screen showing provider registration and routing profile controls
- **Placement suggestion**: after "Centralized policy and governance"
- **Caption suggestion**: "Gateway policy controls in one place: routing profile, auth, and registered provider restrictions."
