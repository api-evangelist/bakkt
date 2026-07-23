---
name: Open a Bakkt account and buy crypto
description: Create an investor account, complete KYB/KYC, then place and confirm a crypto buy order on Bakkt Crypto Solutions.
api: openapi/bakkt-crypto-openapi.yml
operations: [login, createAccount, triggerKYBScreening, getAccountStatus, getInstruments, submitOrder, getOrder]
method: generated
generated: '2026-07-18'
---

# Open a Bakkt account and buy crypto

Use this flow to onboard an investor and place their first buy order on the Bakkt Crypto Solutions API (`/apex-crypto/api/v2`). All calls must be over HTTPS.

## Prerequisites
- Provisioned client credentials (logonId/password), environment, and IP whitelisting (obtained via sales@bakkt.com).

## Steps
1. **Authenticate** — call `login` with your `logonId`/`password`. Store the returned `token` and send it in the `Authorization` header on every subsequent call.
2. **Create the investor account** — call `createAccount` with a client-unique `accountId` that identifies the investor on your platform.
3. **Screen the account** — call `triggerKYBScreening` for the new account. Watch for the `PARTY_EVENT` webhook: `partyStatus: ACTIVE` / `reasonCode: ENROLLMENT` means cleared; `RISK_KYC`/`RISK_OFAC`/`DOCUMENT_VERIFICATION` means additional action is required.
4. **Confirm trading eligibility** — call `getAccountStatus` with the `accountId` to verify the account can trade in its jurisdiction.
5. **Look up the instrument** — call `getInstruments` and select the target symbol (e.g. `BTCUSD`).
6. **Place the order** — call `submitOrder` with a client-unique `clientOrderId`. Use `notional` for a USD amount or `quantity` for an exact crypto amount. Orders process **asynchronously**.
7. **Confirm** — poll `getOrder` by `clientOrderId`, or consume the Execution Report / event webhook for the fill.

## Rules
- **Idempotency:** `clientOrderId` de-duplicates order submissions. Retries must NOT change order details.
- **Minimums:** notional must be > $1.00 unless selling an entire position; price may not be > 10% through the market.
- **Maintenance window:** orders placed 16:30–17:05 CT are rejected as expired.
- **Errors:** see `errors/bakkt-problem-types.yml` and `errors/bakkt-error-codes.yml` (API Order Reject Reasons).
