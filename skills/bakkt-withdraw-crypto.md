---
name: Withdraw crypto to an external wallet
description: Validate a destination wallet, request a fee estimate, submit an on-chain withdrawal, and track it to completion on Bakkt Crypto Solutions.
api: openapi/bakkt-crypto-openapi.yml
operations: [login, postReceiveAddress, verifyWalletAddress, lookupVasp, postWithdrawFee, postWithdraw, getWithdraw, getPositions]
method: generated
generated: '2026-07-18'
---

# Withdraw crypto to an external wallet

Use this flow to move an investor's crypto off-platform on the Bakkt Crypto Solutions API (`/apex-crypto/api/v2`). All calls over HTTPS.

## Steps
1. **Authenticate** — call `login`; send the `token` in the `Authorization` header.
2. **Check holdings** — call `getPositions` to confirm the investor holds enough of the coin (amounts are crypto quantities, not notional).
3. **Validate the destination** — call `verifyWalletAddress` to confirm the address is valid, and optionally `lookupVasp` to resolve the VASP name for the address. (For deposits instead, `postReceiveAddress` returns a receive address — never cache it.)
4. **Request a fee estimate** — call `postWithdrawFee`. Present the fee to the investor for confirmation. Estimates are honored only up to a grace period; after it expires, request a new one.
5. **Submit the withdrawal** — call `postWithdraw` using the ID of the accepted fee estimate.
6. **Track it** — rely on the transfer WebSocket/event mechanism for timely updates; `getWithdraw` can be polled for on-demand status. Blockchain confirmation adds a network-dependent time lag.

## Rules
- A **fee estimate is required** before `postWithdraw`; estimates expire after a grace period.
- Withdrawals complete only after **on-chain confirmation** — always reconcile via the transfer event stream.
- **Errors:** see `errors/bakkt-problem-types.yml`.
