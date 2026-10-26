# Yoco (yoco)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
