# Yoco (yoco)

Yoco is a South African fintech providing card acceptance and payments infrastructure for small and medium businesses - card machines, point of sale, and online payments. Its developer platform exposes REST APIs for accepting online card payments and reading payment data.

## Access Model (Honest Summary)

- **Self-serve.** Any Yoco merchant generates **secret integration keys** from the Yoco app under **Sales -> Payment Gateway** - no sales call or partner approval to start building.
- **Test and live keys.** Secret keys are prefixed `sk_test_` (test mode, no real money) and `sk_live_` (live mode, real ZAR). The versioned API also offers a sandbox host (`api.yocosandbox.com`).
- **Two API surfaces:**
  - **Checkout API** at `https://payments.yoco.com/api` - secret-key Bearer auth. Create hosted checkouts, issue refunds, manage webhooks. **Call server-side only** to protect the secret key.
  - **Versioned Yoco API** at `https://api.yoco.com/v1` - JWT Bearer auth with scopes (e.g. `business/orders:read`). Read payments, refunds, and payment links.
- **Webhooks, not WebSockets.** Asynchronous outcomes (e.g. `payment.succeeded`, `refund.succeeded`) arrive as **signed outbound webhooks** (HMAC-SHA256 in the `webhook-signature` header). There is **no documented public WebSocket API**.
- **Pricing is per transaction**, not per API call: the online gateway is **2.95% excl VAT** per local card transaction with no monthly fee (lower with volume). See [Plans](plans/yoco-plans-pricing.yml).

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/yoco/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/yoco/refs/heads/main/apis.yml)

## Tags

- Payments
- Fintech
- Payment Gateway
- Card Payments
- South Africa
- Online Payments
- Checkout
- Point of Sale
- SMB
- Financial Infrastructure

## Timestamps

- **Created:** 2026-07-12
- **Modified:** 2026-07-12

## APIs

### Yoco Checkout API

Server-side hosted checkout for accepting online card payments. Create a checkout with an amount (in cents) and currency, redirect the customer to the returned `redirectUrl`, and confirm the outcome via webhook. Supports display-only line items, tax and discount totals, metadata, and an `Idempotency-Key` header.

- **Human URL:** [https://developer.yoco.com/online/api-reference/checkout/payments/accept-payments/](https://developer.yoco.com/online/api-reference/checkout/payments/accept-payments/)
- **Base URL:** `https://payments.yoco.com/api`
- **Confirmed:** `POST /checkouts`

### Yoco Refunds API

Refund a completed checkout in full or in part by posting to the checkout's refund sub-resource. A `refund.succeeded` webhook confirms the final state. A separate versioned endpoint reads refund records.

- **Human URL:** [https://developer.yoco.com/online/api-reference/checkout/refunds/accept-refunds/](https://developer.yoco.com/online/api-reference/checkout/refunds/accept-refunds/)
- **Base URL:** `https://payments.yoco.com/api`
- **Confirmed:** `POST /checkouts/{checkoutId}/refund`, `GET /v1/refunds/{refundId}` (on `api.yoco.com`)

### Yoco Webhooks API

Register, list, and delete webhook endpoints that Yoco calls with signed event notifications. Deliveries carry `webhook-id`, `webhook-timestamp`, and `webhook-signature` headers verified with an HMAC-SHA256 signature.

- **Human URL:** [https://developer.yoco.com/online/api-reference/webhooks/list-all-webhooks](https://developer.yoco.com/online/api-reference/webhooks/list-all-webhooks)
- **Base URL:** `https://payments.yoco.com/api`
- **Confirmed:** `POST /webhooks`, `GET /webhooks`

### Yoco Payments API

Versioned read API for querying payment records. `GET /v1/payments` supports cursor pagination and filters by status and created/updated timestamps. JWT Bearer credentials scoped `business/orders:read`. Sandbox host available.

- **Human URL:** [https://developer.yoco.com/api-reference/yoco-api/payments/list-payments-v-1-payments-get](https://developer.yoco.com/api-reference/yoco-api/payments/list-payments-v-1-payments-get)
- **Base URL:** `https://api.yoco.com`
- **Confirmed:** `GET /v1/payments`

### Yoco Payment Links API

Versioned API for shareable payment links. `GET /v1/payment_links/{paymentLinkId}` fetches a link's details. JWT Bearer credentials scoped `business/orders:read`.

- **Human URL:** [https://developer.yoco.com/api-reference/yoco-api/payment-links/fetch-payment-link-v-1-payment-links-payment-link-id-get](https://developer.yoco.com/api-reference/yoco-api/payment-links/fetch-payment-link-v-1-payment-links-payment-link-id-get)
- **Base URL:** `https://api.yoco.com`
- **Confirmed:** `GET /v1/payment_links/{paymentLinkId}`

## Confirmed vs Modeled

**Confirmed** against live Yoco developer-hub reference pages: `POST /checkouts`, `POST /checkouts/{checkoutId}/refund`, `POST /webhooks`, `GET /webhooks`, `GET /v1/payments`, `GET /v1/refunds/{refundId}`, `GET /v1/payment_links/{paymentLinkId}`.

**Modeled** (inferred companions, marked `x-status: modeled` in the OpenAPI - reconcile before relying on them): `GET /checkouts/{checkoutId}`, `DELETE /webhooks/{webhookId}`, `GET /v1/payments/{paymentId}`, `POST /v1/payment_links`.

No machine-readable OpenAPI download was confirmed from Yoco (its docs are Fern-powered); `openapi/yoco-openapi.yml` here is hand-authored from the documented endpoints.

## Artifacts

- [OpenAPI](openapi/yoco-openapi.yml)
- [Postman Collection](collections/yoco.postman_collection.json)
- [Open Collection](collections/yoco.opencollection.json)
- [Authentication](authentication/yoco-authentication.yml)
- [Plans](plans/yoco-plans-pricing.yml)
- [Rate Limits](rate-limits/yoco-rate-limits.yml)
- [FinOps](finops/yoco-finops.yml)
- [Domain Security](security/yoco-domain-security.yml)
- [Review](review.yml)

## Common Properties

- [LinkedIn](https://www.linkedin.com/company/yoco)
- [Website](https://www.yoco.com/za/)
- [Documentation](https://developer.yoco.com)
- [Plans](plans/yoco-plans-pricing.yml)
- [Rate Limits](rate-limits/yoco-rate-limits.yml)
- [Fin Ops](finops/yoco-finops.yml)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
