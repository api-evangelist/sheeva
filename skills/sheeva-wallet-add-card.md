---
name: Add a credit card to a vehicle wallet
description: Fetch a vehicle wallet, add a tokenized credit card using a generated public key, and set it as default.
api: openapi/sheeva-openapi.yml
operations: [1GetVehicleWallet, 2GeneratePublicKeyToAddCreditCard, 3AddCreditCardToAWallet, 6MarkCreditCardAsDefault]
---

# Add a credit card to a vehicle wallet

Card data is added against a per-request public key so raw PAN never transits SheevaConnect in the clear.

## Steps
1. **Get the wallet** — `1GetVehicleWallet` (`GET /v2/partner-apis/wallet?vin={vin}`). Capture `walletId`.
2. **Generate a public key** — `2GeneratePublicKeyToAddCreditCard` (`POST /v2/partner-apis/wallet/public-key`). Use the returned key to encrypt the card payload client-side.
3. **Add the card** — `3AddCreditCardToAWallet` (`POST /v2/partner-apis/wallet/cards`) with the encrypted card material.
4. **Set default** (optional) — `6MarkCreditCardAsDefault` (`PATCH /v2/partner-apis/wallet/{walletId}/cards/{cardId}`).

## Rules
- The public key is single-use per add; request a fresh one each time.
- Card ids are prefixed `card_` (see `data-model/sheeva-data-model.yml`).
- All calls require `Authorization` (bearer) + `x-sheeva-program-code`.
