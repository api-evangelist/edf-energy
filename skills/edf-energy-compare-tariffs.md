---
generated: '2026-07-27'
method: generated
name: Compare EDF electricity and gas tariffs for a postcode
description: >-
  Resolve a GB postcode to its Grid Supply Point group, list EDF's live retail products,
  and read the unit rates and standing charges of a chosen tariff. Runs anonymously — no
  credential, no account, no registration.
api: openapi/edf-energy-kraken-openapi.yml
operations:
  - List Industry Grid Supply Points
  - List Products
  - Product
  - Electricity Tariff Standard Unit Rates
  - Electricity Tariff Standing Charges
  - Gas Tariff Standard Unit Rates
  - Gas Tariff Standing Charges
source: >-
  operationIds verified verbatim in openapi/edf-energy-kraken-openapi.yml; anonymous
  access verified live on 2026-07-27 (see sandbox/edf-energy-sandbox.yml).
---

# Compare EDF electricity and gas tariffs for a postcode

This is the one EDF flow an agent can run end to end with no credential. It is what EDF
markets as its "open tariff APIs".

## Base URL

`https://api.edfgb-kraken.energy/v1/` — HTTPS only. The served OpenAPI declares no
`servers[]` array; the base URL comes from the REST API basics guide.

## Auth

None required. `List Products`, `Product` and every tariff rate/charge operation declare
an empty security option (`{}`) alongside `KeyAuthentication` and
`DRFKrakenTokenAuthentication`, and answered HTTP 200 anonymously on 2026-07-27.
`List Industry Grid Supply Points` declares only the two token schemes but also resolved
anonymously — treat that as best-effort and be ready to fall back.

## Steps

1. **Resolve the region** — `List Industry Grid Supply Points`
   (`GET /v1/industry/grid-supply-points/`) with `postcode`. Returns the GSP group
   (e.g. `_C` for `SW1A1AA`). Regional unit rates depend on this group, so resolve it
   before comparing prices.
2. **List the live products** — `List Products` (`GET /v1/products/`). Filter with the
   query parameters the spec declares: `brand`, `is_business`, `is_green`, `is_prepay`,
   `is_tracker`, `is_variable`, `is_historical`, `available_at`. Paginate with `page`.
   Capture each `code` — that is the `product_code`.
3. **Open one product** — `Product` (`GET /v1/products/{product_code}/`). The response
   carries the product's electricity and gas tariffs; capture the `tariff_code` for the
   GSP group from step 1.
4. **Read the electricity price** — `Electricity Tariff Standard Unit Rates`
   (`GET /v1/products/{product_code}/electricity-tariffs/{tariff_code}/standard-unit-rates/`)
   and `Electricity Tariff Standing Charges` (same path, `/standing-charges/`). Both take
   `period_from`, `period_to`, `page`, `page_size`.
5. **Read the gas price** — `Gas Tariff Standard Unit Rates` and
   `Gas Tariff Standing Charges` under `/gas-tariffs/{tariff_code}/`.

For Economy 7 tariffs use `Electricity Tariff Day Unit Rates` and
`Electricity Tariff Night Unit Rates` instead of the standard collection. For EV tariffs
use `Electricity Tariff Ev Device Peak Unit Rates` and
`Electricity Tariff Ev Device Off Peak Unit Rates`.

## Conventions

- **Datetimes are ISO 8601.** Always include the timezone on `period_from` / `period_to`.
  If you omit it, `Europe/London` is assumed and results shift between GMT and BST.
- **Pagination** on these REST collections is `page` / `page_size`, not cursors. (The
  GraphQL half uses Relay cursors — different surface, different rules.)
- Full detail in `conventions/edf-energy-conventions.yml`.

## Errors

Most of these read operations declare only a `200` in the spec — there is no
machine-readable contract for failure on those paths. Handle non-2xx defensively. The
platform's behavioural error vocabulary is the `KT-CT-*` registry in
`errors/edf-energy-error-codes.yml` (1,370 codes); REST-declared responses are in
`errors/edf-energy-problem-types.yml`.

## Notes

- The GraphQL equivalent is `energyProducts(postcode: ..., brand: "EDF")`, also anonymous.
  Omitting `brand` returns `KT-GB-9516`; `"EDF"` is the working value.
- These per-tariff rate collections are REST-only: GraphQL reaches rates through the
  customer-scoped agreement path, so this is the only anonymous machine-readable price
  feed EDF publishes. See `mcp/edf-energy-tool-crosswalk.yml`.
