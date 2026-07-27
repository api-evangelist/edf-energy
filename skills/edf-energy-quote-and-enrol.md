---
generated: '2026-07-27'
method: generated
name: Quote a supply and enrol a customer
description: >-
  Price a supply for a prospective EDF customer, share the quote by email, create the
  account, and renew a business tariff. The commercial write path on the REST API.
api: openapi/edf-energy-kraken-openapi.yml
operations:
  - List Industry Grid Supply Points
  - List Products
  - Create a quote
  - Share a quote via email
  - Create an account
  - Renew a business tariff
source: >-
  operationIds verified verbatim in openapi/edf-energy-kraken-openapi.yml.
---

# Quote a supply and enrol a customer

This is the sales path: from a postcode to a signed supply agreement. Every step after
the product lookup writes real commercial state, so none of it is agent-autonomous.

## Base URL

`https://api.edfgb-kraken.energy/v1/`

## Auth

Reads (`List Products`) are anonymous. Every write requires `KeyAuthentication`
(`Authorization: Token <api-key>`) or `DRFKrakenTokenAuthentication` (a Kraken JWT).
Affiliate and partner organisations have their own HTTP Basic schemes —
`AffiliateAuthentication` and `PartnerUserOnlyAuthentication`. See
`authentication/edf-energy-authentication.yml`.

## Steps

1. **Resolve the region** — `List Industry Grid Supply Points`
   (`GET /v1/industry/grid-supply-points/?postcode=...`) for the GSP group that determines
   regional pricing.
2. **Pick the product** — `List Products` (`GET /v1/products/`), filtering with
   `is_business`, `is_green`, `is_prepay`, `is_tracker`, `is_variable`, `available_at`.
   Capture the product `code`.
3. **Create the quote** — `Create a quote` (`POST /v1/quotes/`). Responses: `201`, `400`.
   Capture the quote `code`.
4. **Share it** — `Share a quote via email`
   (`POST /v1/quotes/{code}/products/{product_id}/`). Responses: `204`, `400`, `404`.
   This sends mail to a real person; send once.
5. **Create the account** — `Create an account` (`POST /v1/accounts/`). Responses: `201`,
   `202`, `400`. A `202` means the enrolment was accepted for asynchronous processing —
   do not treat it as complete. A `400` here means validation errors that cannot be fixed
   by an EDF agent, e.g. a duplicate reference where the existing enrolment was never
   rejected.
6. **Renew a business tariff** — `Renew a business tariff`
   (`POST /v1/accounts/{account_number}/tariff-renewal/`). This is the best-documented
   error surface in the whole document: `201`, `400` (validation or domain error),
   `401` (incorrect authentication), `403` (insufficient permissions), `404` (account
   does not exist), `500`.

## Idempotency

None on REST. No `Idempotency-Key` is accepted on any of these operations. Guard step 3,
step 4 and step 5 with your own exactly-once orchestration — a blind retry of
`Create an account` risks the duplicate-reference `400` described above, and a retry of
`Share a quote via email` mails the customer twice.

## Conventions

Datetimes are ISO 8601 with the timezone included. Pagination on the REST collections is
`page` / `page_size`. See `conventions/edf-energy-conventions.yml`.

## Errors

Declared responses are in `errors/edf-energy-problem-types.yml`; the behavioural
`KT-CT-*` vocabulary — including the enrolment, contract and agreement families — is in
`errors/edf-energy-error-codes.yml`.

## Notes

- GraphQL models enrolment as a process rather than a single POST (`productEnrolment`,
  `productEnrolments`, `joinSupplierProcess`) and renewal as a rollover
  (`agreementRollover`, `agreementsForRollover`, `canRescindAgreement`). The two surfaces
  are not equivalent; see `mcp/edf-energy-tool-crosswalk.yml`.
- Payment against the created account runs through the REST Stripe payment-intent triple
  (`Create a Stripe payment intent`, `Confirm a Stripe payment intent`,
  `Mark a Stripe payment intent as failed`), which also has no idempotency key.
