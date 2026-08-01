---
name: preauth-create-preauthorization
description: Create a Preauth order and pre-authorize (reserve) funds on a buyer's card.
api: openapi/preauth-instacash-orders-openapi.yml
generated: '2026-07-20'
method: generated
operations:
  - createOrder
  - getOrder
---

# Create a pre-authorization with Preauth

Reserve funds on a buyer's card without capturing them yet.

## Steps

1. **Create the order** — `POST https://api.preauth.io/v1/order` with header
   `x-auth-token: <api-token>` and a JSON body containing `country` (ISO 3166-1 alpha-2),
   `currency` (ISO 4217), `amount` (minor units, e.g. cents), `reference` (your merchant
   reference), and `limit_date` (YYYY-MM-DD). Operation: `createOrder`.
2. **Read the returned `id`.** The order starts in status `created` with no card attached.
3. **Present the widget** — load `https://cdn.preauth.io/preauth.js`, call
   `preauth("init", { order: "<id>", onSuccess, onError })` then `preauth("start")` so the
   buyer enters card details and the hold is placed.
4. **Confirm the hold** — after `onSuccess`, call `getOrder`
   (`GET /v1/order/{id}`) and verify `status` is `in_progress`.

## Rules
- Amounts are integers in the smallest currency unit; there is no decimal field.
- Auth is the `x-auth-token` header on every request (no OAuth). See
  `authentication/preauth-instacash-authentication.yml`.
- No idempotency key exists — dedupe on your own `reference`. See
  `conventions/preauth-instacash-conventions.yml`.
