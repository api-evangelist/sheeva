---
name: Onboard a driver and vehicle
description: Register a driver and their vehicle on SheevaConnect and set the active driver so in-vehicle payments can begin.
api: openapi/sheeva-openapi.yml
operations: [authentication, registerDriver, registerVehicle, linkDriverToAVehicle, markDriverAsCurrentDriverForAVehicle]
---

# Onboard a driver and vehicle

Use this to bring a new driver and vehicle onto a SheevaConnect program.

## Prerequisites
- A Sheeva-issued `client-id` / `client-secret` and a program code.
- Every partner-api call requires the `x-sheeva-program-code` header.

## Steps
1. **Authenticate** — `authentication` (`GET /v2/auth`). Send `client-id` and `client-secret` headers; receive a JWT `accessToken`. Put it in the `Authorization` header on every subsequent call.
2. **Register the driver** — `registerDriver` (`POST /v2/partner-apis/drivers`). Provide `countryCode`, `phoneNumber`, `firstName`, `lastName` (email/birthDate/gender may be required per program). Capture the returned `driverId`.
3. **Register the vehicle** — `registerVehicle` (`POST /v2/partner-apis/vehicles`). Provide `vin`, `make`, `model`, `fuelTypes` (`PETROL`/`DIESEL`/`ELECTRIC`); add `connectorType` for EVs.
4. **Link driver to vehicle** — `linkDriverToAVehicle` (`POST /v2/partner-apis/drivers/{driverId}/link?vin={vin}`).
5. **Set the active driver** — `markDriverAsCurrentDriverForAVehicle` (`POST /v2/partner-apis/drivers/{driverId}/drive?vin={vin}`).

## Rules
- A `409 Conflict` on register usually means the driver/vehicle already exists — fetch instead of re-creating.
- Errors return `{ "message": ... }` with a standard HTTP status (see `errors/sheeva-problem-types.yml`).
- No idempotency key exists; do not blindly retry POSTs on timeout — re-fetch to confirm state first.
