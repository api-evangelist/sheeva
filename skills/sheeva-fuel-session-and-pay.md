---
name: Run a fueling session and pay
description: Locate a Sheeva Pay fuel hub, confirm the service point on an active session, authorize payment, and close the session.
api: openapi/sheeva-openapi.yml
operations: [getServiceHubs, getCurrentlyActiveSessions, confirmServicePoint, authorizePayment, markServiceComplete]
---

# Run a fueling session and pay

SheevaConnect opens a session automatically when vehicle telemetry indicates a service need (e.g. `LOW_FUEL_DETECTED`). This flow drives it to completion.

## Steps
1. **Find nearby hubs** (optional) — `getServiceHubs` (`GET /v2/partner-apis/serviceHubs?latitude=..&longitude=..&radius=..&serviceType=FUEL`).
2. **Get the active session** — `getCurrentlyActiveSessions` (`GET /v2/partner-apis/sessions/current-session?serviceType=FUEL&vin={vin}`). Read `sessionId` and `status`.
3. **Confirm the service point** — `confirmServicePoint` (`POST /v2/partner-apis/sessions/{sessionId}/confirm-service-point?demoMode=0`) once the driver is at the pump.
4. **Authorize payment** — `authorizePayment` (`POST /v2/partner-apis/payments/authorize-transaction?sessionId={sessionId}`) with `purchaseAmount` and one or more `paymentInstruments` (`creditCard`, `rewards`, `upi`). Use `demoMode: 1` in sandbox.
5. **Complete the service** — `markServiceComplete` (`POST /v2/partner-apis/sessions/{sessionId}/mark-service-complete`).

## Rules
- Branch on session status codes and payment status codes in `errors/sheeva-problem-types.yml`.
- `demoMode` is set from `sandbox/sheeva-sandbox.yml`; keep it `0` in production.
- Subscribe to `session-status-update` / `payment-status-update` webhooks (`asyncapi/sheeva-webhooks.yml`) instead of polling where possible.
