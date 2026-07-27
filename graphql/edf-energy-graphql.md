# EDF Energy (EDF GB) Kraken GraphQL API

EDF Energy's primary API is a GraphQL API, because EDF Energy runs on Kraken — the energy
operating system built by Octopus Energy Group and licensed to EDF, onto which EDF migrated
5.8 million customer accounts. The API surface is therefore Kraken's, branded and hosted for
EDF GB.

The schema in this directory (`edf-energy-schema.graphql`) is **not conceptual**. It was
harvested verbatim from the live endpoint by anonymous GraphQL introspection on 2026-07-27 and
rendered to SDL with `graphql-core` 3.2.11. No type, field, argument or description was added,
removed or renamed.

## Endpoint

`https://api.edfgb-kraken.energy/v1/graphql/`

- `GET` returns HTTP 400 `{"errors":[{"message":"Must provide query string."}]}`
- `POST` with a JSON body returns HTTP 200
- Introspection is **enabled anonymously** — no API key, no account, no registration

## Documentation

- Portal: https://developer.edfgb-kraken.energy/
- GraphQL guides: https://developer.edfgb-kraken.energy/graphql/guides/
- GraphQL reference: https://developer.edfgb-kraken.energy/graphql/reference/
- Changelog: https://developer.edfgb-kraken.energy/graphql/changelog/
- Announcements: https://developer.edfgb-kraken.energy/announcements/
- EDF's own announcement of the open tariff APIs:
  https://www.edfenergy.com/energywise/edfs-open-tariff-apis

## Shape

Verified from the harvested schema:

- 2,492 types
- 246 `Query` fields
- 417 `Mutation` fields
- no `Subscription` type

## Authentication

Two layers, and the distinction is the whole story for this provider.

**Unauthenticated.** Some fields resolve with no credential at all. Verified live on 2026-07-27:

```graphql
{
  energyProducts(postcode: "SW1A1AA", brand: "EDF", first: 2) {
    edges { node { code displayName fullName isVariable } } }
}
```

returned HTTP 200 with real EDF retail tariffs, e.g. `EDF_FOL_06M_B1_1YR_26-07-24_v1`
("Fixed Online 1 Year"). Omitting `brand` returns error `KT-GB-9516`
("We were unable to find any products for this brand"); `brand: "EDF"` is the working value.

**Authenticated.** Everything customer-scoped requires an `Authorization` header. Kraken's
viewers are account users, organisations and OAuth applications. There is a full OpenID Connect
authorisation server at `https://auth.edfgb-kraken.energy/`, whose discovery document is served
anonymously (HTTP 200) at `/.well-known/openid-configuration` and is saved verbatim in this repo
at `authentication/edf-energy-kraken-openid-configuration.json`. It advertises 111 scopes,
including `request:consumption-data`, `view:smartflex-data`, `update:smart-meter-data-preferences`,
`full-customer-access`, `view:sensitive-customer-information`, `manage:ev` and `view:api-key`.

Published rate limits (from the GraphQL basics guide): 50,000 points/hour for account users,
100,000 for organisations, 300,000 for OAuth applications; complexity limit 200 per request;
10,000 nodes per request; rate-limit error code `KT-CT-1199`.

## Notes

- **Harvested, not derived.** Byte-for-byte SDL from a live introspection response.
- **This is a licensed platform API.** EDF did not build it; Kraken Technologies did. The same
  schema shape appears across Kraken licensees. That is the finding, not a caveat.
- **No mandate produced it.** The United Kingdom has no consumer energy data-portability right.
  Every capability in this schema exists because a supplier chose to ship it.
- See `../review.yml` for the full mandate-versus-implementation record and every probed URL
  with its HTTP status.
