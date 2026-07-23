---
name: Link a bank account and fund with fiat
description: Authenticate, link a consumer bank account via Plaid, move fiat via ACH/Wire, and confirm the balance on Bakkt's Fiat/Partner API.
api: openapi/bakkt-crypto-openapi.yml
operations: [generateAccessToken, addBankAccount, transferFiatCurrency, getFiatAccountBalance, generateWireInstructions]
method: generated
generated: '2026-07-18'
---

# Link a bank account and fund with fiat

Use this flow to onboard fiat funding on the Bakkt Fiat/Partner API (`/partner/v1`). All calls over HTTPS.

## Steps
1. **Authenticate** — call `generateAccessToken` to obtain the access token; send it in the `Authorization` header.
2. **Link the bank account** — call `addBankAccount` with a Plaid processor token to link the consumer's fiat account. (For inbound wire funding, call `generateWireInstructions` to get end-user-specific wire instructions instead — do not cache them.)
3. **Move funds** — call `transferFiatCurrency` to initiate a deposit (or withdrawal) on the linked account.
4. **Track the transfer** — consume the `FIAT_EVENT` webhook for ACH/Wire lifecycle (standard, return, cancelled, decline). ACH returns carry a NACHA `reasonCode` — see `errors/bakkt-decline-codes.yml`.
5. **Confirm balance** — call `getFiatAccountBalance` for the consumer.

## Rules
- **Wire instructions** are per-end-user and may change over time; never cache them.
- **ACH returns** can arrive pre- or post-completion; reconcile against the `FIAT_EVENT` flow.
- **Errors:** see `errors/bakkt-error-codes.yml` (Fiat Funding Onramp Error Codes) and `errors/bakkt-decline-codes.yml`.
