# EDF Energy (edf-energy)

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

EDF Energy Ltd is the British integrated energy business wholly owned by Électricité de France, the French state-owned utility. Formed in 2002 and enlarged by the 2009 acquisition of British Energy, it supplies electricity and gas to roughly five million customer accounts and is Britain's largest generator of zero-carbon electricity, operating the country's fleet of operating nuclear power stations and building Hinkley Point C. Great Britain has no consumer energy data mandate — and EDF has one of the better utility APIs in Europe anyway, because it licenses Octopus Energy Group's Kraken platform.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/edf-energy/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/edf-energy/refs/heads/main/apis.yml)

## Tags

- Energy
- United Kingdom
- Utilities
- Electricity
- Gas
- Energy Retailer
- Energy Supplier
- Smart Metering
- Nuclear
- Renewables
- EV Charging
- Demand Response
- Tariffs
- Energy Markets

## Timestamps

- **Created:** 2026-07-27
- **Modified:** 2026-07-27

## Mandate Posture

| Question | Answer |
| --- | --- |
| Mandate regime | Smart-meter **infrastructure** only — the licensed Smart DCC monopoly under the Smart Energy Code, plus Balancing and Settlement Code and REMIT obligations |
| Consumer data mandate | **None.** Great Britain has no Consumer Data Right, no Green Button obligation, no energy data-portability duty |
| Mandate status | **Not applicable** — nothing compels EDF to publish consumer data through an API |
| Data standard | No standard reference found. The contract shape is Kraken's, proprietary. (OCPP appears only as an EV charge-point integration concept in the schema.) |
| Consumer data API | **Yes** — documented, and not mandated. Gated by an account token or an OAuth application |
| Open market data | **No** — EDF operates no open market-data API; its own REMIT XML and CSV export links return HTTP 404 |
| Open tariff data | **Yes, fully anonymous** — `GET /v1/products/` returned 21 live products with no credential |
| Access gate | Self-serve — no signup, no key, no form for the open half |
| Developer portal | [developer.edfgb-kraken.energy](https://developer.edfgb-kraken.energy/) — public, HTTP 200, **not on edfenergy.com** |

EDF is the sector's counter-example. Every mandated utility in this study is tested on whether the obligation became an implementation. EDF has no obligation and a substantial implementation — because it rents the API. In 2023 it licensed Kraken from Octopus Energy Group and completed the migration of 5.8 million customer accounts in fifteen months; what is documented at `developer.edfgb-kraken.energy` is the platform's contract with EDF's logo on it. Britain's unusual result is that one API-native supplier propagated an API into its incumbent competitors by commercial licence, faster and further than a statute did elsewhere. The trade-off is real: EDF's API is better to use and weaker to rely on. There is no legal right for a consumer to compel the transfer, no accreditation regime that makes third-party access a rule rather than a favour, and no standard shape shared across suppliers.

Note also where EDF's own claim failed. Its REMIT transparency page advertises "Download XML" and "Download CSV" for its generation-unavailability disclosures; both return HTTP 404. The working machine-readable channel for exactly the same records is Elexon's BMRS API, where 27 EDF messages under registration code `48X000000000022A` were retrieved anonymously in a single week — Elexon's API, not EDF's.

## APIs

### EDF Kraken GraphQL API

EDF Energy's primary developer API, and the surface EDF markets as its "open tariff APIs". Introspection is enabled anonymously: a standard `IntrospectionQuery` POSTed with no credentials returned HTTP 200 on 2026-07-27, yielding **2,492 types, 246 Query fields and 417 Mutation fields**, saved verbatim as SDL in this repository. Retail product and tariff data resolves without any credential; everything customer-scoped requires an `Authorization` header held by an account user, an organisation, or an OAuth application.

- **Human URL:** [https://developer.edfgb-kraken.energy/graphql/](https://developer.edfgb-kraken.energy/graphql/)
- **Base URL:** `https://api.edfgb-kraken.energy/v1/graphql/`

#### Tags

- GraphQL
- Kraken
- Tariffs
- Energy Products
- Smart Metering
- Consumption
- EV Charging
- United Kingdom

#### Properties

- [GraphQL](graphql/edf-energy-graphql.md) — schema at [`graphql/edf-energy-schema.graphql`](graphql/edf-energy-schema.graphql)
- [Documentation](https://developer.edfgb-kraken.energy/graphql/guides/)
- [API Reference](https://developer.edfgb-kraken.energy/graphql/reference/)
- [Changelog](https://developer.edfgb-kraken.energy/graphql/changelog/)
- [Authentication](authentication/edf-energy-kraken-openid-configuration.json)

### EDF Kraken REST API

The REST half of the platform, described by a first-party **OpenAPI 3.0.3** document EDF serves itself and renders through ReDoc — 27 paths, 57 component schemas, 5 security schemes. Access is split inside the same document: `/v1/products/` carries an empty security option and returned HTTP 200 anonymously with 21 live EDF products, while `/v1/electricity-meter-points/{mpan}/meters/{serial_number}/consumption/` requires a token.

- **Human URL:** [https://developer.edfgb-kraken.energy/rest/reference/](https://developer.edfgb-kraken.energy/rest/reference/)
- **Base URL:** `https://api.edfgb-kraken.energy/v1/`

#### Tags

- REST
- OpenAPI
- Kraken
- Tariffs
- Products
- Meter Points
- Consumption
- Grid Supply Points
- United Kingdom

#### Properties

- [OpenAPI](openapi/edf-energy-kraken-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Documentation](https://developer.edfgb-kraken.energy/rest/guides/api-basics/)
- [API Reference](https://developer.edfgb-kraken.energy/rest/reference/)
- [Authentication](authentication/edf-energy-kraken-openid-configuration.json)

### EDF Kraken Customer Migration (Data Import) API

A second first-party **OpenAPI 3.0.3** document — 16 paths, 139 component schemas — describing how a customer book moves between suppliers onto Kraken: import processes created, validated and processed, transfer status polled per external account number, and historical statements, transactions, notes and payment instructions imported, all keyed by an `import_supplier_code`. The operational counterpart to EDF's own 5.8-million-account migration. Partner-facing: authentication required, and a migration relationship rather than a signup form.

- **Human URL:** [https://developer.edfgb-kraken.energy/rest/guides/data-import/](https://developer.edfgb-kraken.energy/rest/guides/data-import/)
- **Base URL:** `https://api.edfgb-kraken.energy/v1/`

#### Tags

- REST
- OpenAPI
- Kraken
- Customer Migration
- Data Import
- Switching
- United Kingdom

#### Properties

- [OpenAPI](openapi/edf-energy-kraken-data-import-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Documentation](https://developer.edfgb-kraken.energy/rest/guides/data-import/)

## Common Properties

- [Website](https://www.edfenergy.com/)
- [Developer Portal](https://developer.edfgb-kraken.energy/)
- [API Announcements](https://developer.edfgb-kraken.energy/announcements/)
- [Open Tariff APIs announcement](https://www.edfenergy.com/energywise/edfs-open-tariff-apis)
- [REMIT transparency](https://www.edfenergy.com/energy/remit-summary)
- [About](https://www.edfenergy.com/about)
- [Media Centre](https://www.edfenergy.com/media-centre)
- [Privacy & Cookie Policy](https://www.edfenergy.com/terms-conditions/privacy-cookie-policy)
- [GitHub Organization](https://github.com/edfenergy)
- [LinkedIn](https://www.linkedin.com/company/edf-energy)

## Review

See [review.yml](review.yml) for the full mandate-versus-implementation record, every probed URL with its HTTP status, the consumer-data versus market-data split, the access gate, and the authentication model.

## Maintainers

- Kin Lane — kin@apievangelist.com
