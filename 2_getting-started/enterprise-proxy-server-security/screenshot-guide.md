---
description: Recommended screenshot plan for Enterprise Proxy Server Security guides
---

# Screenshot Guide (Where To Capture)

Use this checklist when you add images to the new proxy-server guides.

## 1. Architecture overview image

- **File name**: `gateway-architecture-overview.png`
- **Target page**: `what-is-a-proxy-server.md`
- **What to show**:
  - Unity Client
  - Gateway Server
  - Upstream AI Providers
  - One-way arrows for request flow
- **Goal**: make the "keys stay server-side" concept obvious in one glance

## 2. Policy controls image

- **File name**: `gateway-policy-and-security-benefits.png`
- **Target page**: `why-enterprise-uses-proxy.md`
- **What to show**:
  - Gateway Settings panel
  - Routing profile selection
  - Registered providers section
- **Goal**: show centralized governance controls

## 3. Wizard full-flow image

- **File name**: `wizard-overview-stepper.png`
- **Target page**: `run-with-wizard-local-test.md`
- **What to show**:
  - Server Gateway Export Wizard window
  - Full step list visible from export to smoke test
- **Goal**: orient the reader in the end-to-end process

## 4. Local run verification image

- **File name**: `wizard-local-run-health-swagger.png`
- **Target page**: `run-with-wizard-local-test.md`
- **What to show**:
  - `Run Gateway Locally` button
  - `Open /v1/health + /swagger` button
  - Status checklist
- **Goal**: show exact action sequence for local boot verification

## 5. Smoke test pass image

- **File name**: `wizard-smoke-test-pass.png`
- **Target page**: `run-with-wizard-local-test.md`
- **What to show**:
  - Smoke test status checklist fully passed
  - Progress bar/result state
- **Goal**: define what "ready to proceed" looks like

## Asset path suggestion

Store images under:

- `Gitbook/.gitbook/assets/enterprise-proxy/`

And reference with relative markdown links from each page.
