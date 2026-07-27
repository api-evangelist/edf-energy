---
generated: '2026-07-27'
method: generated
name: Read a customer's electricity and gas consumption
description: >-
  Retrieve metered consumption for a named EDF customer's electricity and gas supply
  points, using a token the account holder issued or an OAuth application they authorised.
  Requires consent — this data is not open.
api: openapi/edf-energy-kraken-openapi.yml
operations:
  - Get Electricity Meter Point
  - List consumption for an electricity meter
  - List consumption for a gas meter
source: >-
  operationIds verified verbatim in openapi/edf-energy-kraken-openapi.yml; OAuth scopes
  from well-known/edf-energy-openid-configuration.json.
---

# Read a customer's electricity and gas consumption

Great Britain has no consumer energy data-portability mandate — no Consumer Data Right,
no Green Button. This flow exists because EDF chose to ship it, and every step is gated
on the customer's own consent.

## Base URL

`https://api.edfgb-kraken.energy/v1/`

## Auth

Required on every step. One of:

- `KeyAuthentication` — `Authorization: Token <api-key>`
- `DRFKrakenTokenAuthentication` — `Authorization: <kraken-jwt>`
- An OAuth 2.0 access token from `https://auth.edfgb-kraken.energy/`, carrying the
  `request:consumption-data` scope (add `view:smartflex-data` for flexibility data).

OAuth applications are not self-service: contact EDF/Kraken with your client type, grant
type, redirect URIs and the resources you need. Authorization code with PKCE (S256),
client credentials, device code and token exchange are all supported. See
`authentication/edf-energy-authentication.yml` and `scopes/edf-energy-scopes.yml`.

## Steps

1. **Identify the supply point** — `Get Electricity Meter Point`
   (`GET /v1/electricity-meter-points/{mpan}/`). The `mpan` is the national Meter Point
   Administration Number for the property. Capture the meter `serial_number`.
2. **Read electricity consumption** — `List consumption for an electricity meter`
   (`GET /v1/electricity-meter-points/{mpan}/meters/{serial_number}/consumption/`).
   Declared query parameters: `period_from`, `period_to`, `group_by`, `order_by`, `page`,
   `page_size`.
3. **Read gas consumption** — `List consumption for a gas meter`
   (`GET /v1/gas-meter-points/{mprn}/meters/{serial_number}/consumption/`), keyed by the
   gas MPRN. Same query parameters.

## Conventions

- **`period_from` / `period_to` are ISO 8601 and you must include the timezone.** Half-hourly
  energy data read without a timezone will silently shift by an hour across the BST
  boundary — the REST guide warns about exactly this.
- Use `group_by` to aggregate rather than pulling raw half-hourly intervals you will
  discard; the platform budgets on volume, not just on request count.
- `conventions/edf-energy-conventions.yml` has the full profile.

## Rate limits

Three budgets apply at once on the platform: per-request query complexity (200), an
hourly points allowance (50,000 for an account user, 100,000 for an organisation,
300,000 for an OAuth application), and per-field static or dynamic limits. Exceeding them
returns `KT-CT-1188`, `KT-CT-1199` or `KT-CT-1189`. An authenticated viewer can read its
remaining budget with the GraphQL `rateLimitInfo` query. See
`rate-limits/edf-energy-rate-limits.yml`.

## Errors

- `KT-CT-1112` — `Authorization` header not provided.
- `KT-CT-1111` / `KT-CT-1132` — the viewer is not authorized for this query.
- `KT-CT-1120` — the Kraken Token has expired; refresh and retry.

Full registry: `errors/edf-energy-error-codes.yml`.

## Notes

- The GraphQL surface is richer here and is where the granular data lives:
  `smartMeterTelemetry`, `annualElectricityConsumption`,
  `extendedAnnualElectricityConsumption`, `estimatedSupplyPointReadings`,
  `smartMeterDataPreferences` and `readingConsentGranularity`. None of those have a REST
  equivalent. If you need half-hourly telemetry or the consent granularity the customer
  granted, use GraphQL. See `mcp/edf-energy-tool-crosswalk.yml`.
- Treat this as consented personal data. Log the scope you used and the consent that
  authorised it.
