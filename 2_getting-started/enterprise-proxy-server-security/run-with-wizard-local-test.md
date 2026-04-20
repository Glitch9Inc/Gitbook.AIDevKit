---
description: Step-by-step guide to run the Enterprise gateway server locally using the built-in Server Gateway Export Wizard
---

# Run It With Wizard (Local Test)

This is the fastest way to validate your gateway flow before cloud deployment.

## Open the wizard

Open:

- `Window/AI Dev Kit/Server Gateway (Enterprise)/Server Gateway Export Wizard`

## Step flow (recommended)

### Step 1 - Export Template

- Click `Export Server`
- Confirm export path was created

### Step 2 - Select Providers And Generate .env

- Select providers you want to route
- Click `Generate .env`
- (Optional) include locally saved API keys

### Step 3 - Configure .env

- Open `.env`
- Set provider API keys and gateway auth variables
- Save the file

### Step 4 - Local Test Prerequisites

Make sure these checks pass:

- `.NET 8 SDK`
- `Docker Desktop`
- `Docker Compose`
- `Docker daemon running`

Use built-in buttons if needed:

- `Install .NET 8 SDK`
- `Install Docker`
- `Install/Repair Docker Compose`
- `Start Docker Desktop`

### Step 5 - Run Gateway Locally

- Click `Run Gateway Locally`
- Then click `Open /v1/health + /swagger`
- Verify endpoints open and respond

### Step 6 - Configure Unity Gateway Settings

- Step auto-applies `LocalTest` routing profile
- Open `Gateway Settings` and verify:
  - Local Test Base URL (default `http://localhost:8080`)
  - Auth settings (must match your `.env` requirements)

### Step 7 - Run Automated Smoke Test

Click `Run Automated Smoke Test` and pass all checks:

- `/v1/health` reachable
- `/swagger` reachable
- Unity LLM request succeeds

## Common failure points

- `.env` missing or incomplete provider/auth values
- Docker daemon not running
- Unity Local Test auth settings mismatch with `.env`
- Local Test Base URL not matching actual local gateway URL

## Screenshot to add

- **Capture name**: `wizard-overview-stepper`
- **Where to capture**: entire wizard window with left step list visible
- **Placement suggestion**: right below "Step flow (recommended)"
- **Caption suggestion**: "Server Gateway Export Wizard full flow from template export to smoke test."

- **Capture name**: `wizard-local-run-health-swagger`
- **Where to capture**: `Run Gateway Locally` step showing both action buttons and status checklist
- **Placement suggestion**: under "Step 5 - Run Gateway Locally"
- **Caption suggestion**: "Run local bootstrap first, then verify `/v1/health` and `/swagger`."

- **Capture name**: `wizard-smoke-test-pass`
- **Where to capture**: `Run Automated Smoke Test` step with all checkmarks green
- **Placement suggestion**: under "Step 7 - Run Automated Smoke Test"
- **Caption suggestion**: "Smoke test completed: health, swagger, and Unity LLM request all passed."
