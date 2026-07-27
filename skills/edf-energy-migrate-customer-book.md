---
generated: '2026-07-27'
method: generated
name: Migrate a customer book onto Kraken
description: >-
  Move accounts from a losing supplier onto the EDF GB Kraken platform — create and
  validate the import process, run it, poll transfer status, then import the account's
  history. Partner-facing; requires a migration relationship with EDF.
api: openapi/edf-energy-kraken-data-import-openapi.yml
operations:
  - V1 Create Or Update Account Import Process
  - V1 Validate Account
  - V1 Process Account Import Process
  - V1 Get Account Transfer Status
  - V1 Get Pending Account Import Processes
  - V1 Get Imported Accounts
  - V1 Get Meter Point Statuses For Account
  - V1 Create Historical Statements
  - V1 Create Transactions
  - V1 Create Account Notes
  - V1 Create Payment Instruction
  - V1 Send Registration Flows
  - V2 Schedule Account Creation
  - V2 Validate Account
  - V2 Account Import Status
source: >-
  operationIds verified verbatim in openapi/edf-energy-kraken-data-import-openapi.yml
  (16 paths, 139 component schemas, harvested from
  https://api.edfgb-kraken.energy/data-import/schema/ at HTTP 200 on 2026-07-27).
---

# Migrate a customer book onto Kraken

This is the contract by which a supplier's customer book moves onto Kraken. EDF ran its
own 5.8 million accounts through this platform in fifteen months. It is the highest
consequence surface in this repository: these calls move real households' energy supply.

## Base URL

`https://api.edfgb-kraken.energy/` — paths carry their own `/v1/data-import/` and
`/v2/data-import/` prefixes.

## Auth

Every operation requires authentication. Declared schemes:
`DataImportViewerAPIKeyAuthentication` (`Authorization: Token <api-key>`) and
`DRFKrakenTokenAuthentication` (a Kraken JWT). There is no signup form — you need a
migration relationship with EDF. See `authentication/edf-energy-authentication.yml`.

## Key identifiers

- `import_supplier_code` — the losing supplier whose book is being imported.
- `external_account_number` — the account number as held by the losing supplier.

Every operation is keyed on one or both.

## Steps

1. **Stage the account** — `V1 Create Or Update Account Import Process`
   (`POST /v1/data-import/account-import-process/create-or-update/`). Upsert-shaped:
   safe to re-send with corrected data. Responses: `200`, `201`, `400`.
2. **Validate before committing** — `V1 Validate Account`
   (`POST /v1/data-import/validate-account/`), or `V2 Validate Account`
   (`POST /v2/data-import/accounts/validate/`) on the v2 path. Dry-run; fix every `400`
   here rather than after step 3.
3. **Process the import** — `V1 Process Account Import Process`
   (`POST /v1/data-import/account-import-process/process/`). **This is the irreversible
   step.** Responses: `201`, `400`, `429` — the only rate-limited response in either
   document, so back off on `429` rather than retrying tightly.
   On the v2 path use `V2 Schedule Account Creation` (`POST /v2/data-import/accounts/`),
   which is asynchronous.
4. **Poll the outcome** — `V1 Get Account Transfer Status`
   (`GET /v1/data-import/account-transfer-status/{import_supplier_code}/{external_account_number}/`),
   or `V2 Account Import Status`
   (`GET /v2/data-import/accounts/{import_supplier_code}/{external_identifier}/`).
   Both return `404` when the account is unknown to the platform.
5. **Check the supply points** — `V1 Get Meter Point Statuses For Account`
   (`GET /v1/data-import/meterpoint-statuses-for-account/{import_supplier_code}/{external_account_number}/`).
6. **Import the history** — once the account exists:
   - `V1 Create Historical Statements` (`POST /v1/data-import/historical-statements/create/`)
   - `V1 Create Transactions` (`POST /v1/data-import/transactions/create/`)
   - `V1 Create Account Notes` (`POST /v1/data-import/notes/create/`)
   - `V1 Create Payment Instruction` (`POST /v1/data-import/payment-instruction/create/`)
     — creates a real payment instruction such as a direct debit; treat as
     human-approved, not agent-autonomous.
7. **Trigger customer comms** — `V1 Send Registration Flows`
   (`POST /v1/data-import/send-registration-flows/{import_supplier_code}/{external_account_number}/`).
   This messages a real customer; send once.
8. **Reconcile the batch** — `V1 Get Pending Account Import Processes`,
   `V1 Get Imported Accounts` and `V1 Get All Account Import Processes`, each
   `GET .../{import_supplier_code}/`.

## Idempotency

There is none on this API. No `Idempotency-Key` header or parameter is accepted anywhere
in the document. The only replay protection is the upsert semantics of step 1 — so make
step 1 repeatable, make step 3 exactly-once in your own orchestration, and never blind-retry
a `Process`, a `Create Payment Instruction` or a `Send Registration Flows`. (Idempotency
on this platform exists only as a GraphQL `idempotencyKey` input field on money
mutations; see `conventions/edf-energy-conventions.yml`.)

## Errors

`400` on validation, `404` on unknown accounts, `429` on the process step, `500` on
payment-instruction creation. The declared responses are in
`errors/edf-energy-problem-types.yml`; the platform-wide `KT-CT-*` vocabulary is in
`errors/edf-energy-error-codes.yml`.

## Notes

- 33 request parameters and schema properties in this document are flagged
  `deprecated: true` with no replacement named and no removal date — unlike the GraphQL
  schema, where every deprecation carries both. Check
  `lifecycle/edf-energy-lifecycle.yml` and the announcements feed before building on a
  flagged field.
- This entire API is REST-only. There is no GraphQL equivalent of any of it — see
  `mcp/edf-energy-tool-crosswalk.yml`.
