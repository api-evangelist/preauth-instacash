---
name: preauth-capture-order
description: Capture all or part of a pre-authorized Preauth order and manage the remaining hold.
api: openapi/preauth-instacash-orders-openapi.yml
generated: '2026-07-20'
method: generated
operations:
  - getOrder
  - captureOrder
  - cancelOrder
  - livenessCheck
---

# Capture a Preauth pre-authorization

Charge money that was previously reserved on the buyer's card.

## Steps

1. **Check state** — `getOrder` (`GET /v1/order/{id}`); confirm `status` is
   `in_progress` and inspect `pending_amount`.
2. **Capture** — `POST /v1/order/{id}/capture` with body `amount` (minor units,
   `<= pending_amount`) and `keep_alive` (boolean: keep holding the remaining balance
   or release it). Operation: `captureOrder`.
3. **Optionally verify the card is still live** before capturing — `livenessCheck`
   (`POST /v1/order/{id}/liveness`); only one check per day unless `force: true`.
4. **Cancel instead** to release all reserved funds — `cancelOrder`
   (`DELETE /v1/order/{id}`). A canceled order is terminal.

## Rules
- Partial captures leave the remainder reserved only when `keep_alive` is `true`.
- Watch webhooks `order.liveness.fail` and `order.desynchronized` — a desynchronized
  card can no longer be re-authorized; capture what remains or ask for a new card.
  See `asyncapi/preauth-instacash-webhooks-asyncapi.yml`.
- Auth is the `x-auth-token` header. See
  `authentication/preauth-instacash-authentication.yml`.
