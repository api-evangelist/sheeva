---
name: Register a status webhook
description: Register an HMAC-signed webhook to receive SheevaConnect session and payment status updates, then list and remove them.
api: openapi/sheeva-openapi.yml
operations: [registerStatusWebhook, getRegisteredWebHooks, deleteAWebHook]
---

# Register a status webhook

Receive push notifications for session and payment lifecycle transitions instead of polling.

## Steps
1. **Register** — `registerStatusWebhook` (`POST /v2/partner-apis/webhooks/status-updates`) with `webhookUrl` (HTTPS), `hmacSigningKey` (secret you generate), and `apikey`.
2. **List** — `getRegisteredWebHooks` (`GET /v2/partner-apis/webhooks`) to confirm registration and capture the webhook id.
3. **Delete** — `deleteAWebHook` (`DELETE /v2/partner-apis/webhooks/{webhook-id}`) to remove one.

## Verifying deliveries
- Every delivery is HMAC-signed with your `hmacSigningKey` — recompute the HMAC over the raw body and reject on mismatch.
- Expect `session-status-update` and `payment-status-update` events; status enums are in `errors/sheeva-problem-types.yml`.
- Respond `2xx` quickly and process asynchronously.
