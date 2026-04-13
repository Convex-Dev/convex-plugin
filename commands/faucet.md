---
name: faucet
description: Request testnet coins from the Convex peer faucet for the given address. Defaults to 1 Gold (1,000,000,000 copper) if no amount is specified.
argument-hint: "<address> [amount-in-copper]"
---

Request testnet faucet funds for the address in $ARGUMENTS.

Steps:

1. Parse $ARGUMENTS: the first token is the address (a `#NNN` reference or a bare integer). The optional second token is an amount in copper; default to `1000000000` (1 Gold) if omitted.
2. Call the `createAccount` MCP tool on `convex-testnet` if the user supplied a public key instead of an existing address, **or** use `transfer` from a funded account if the address already exists. Prefer `createAccount` with `faucet` set when the user has only a key, because a single call both creates and funds.
3. Confirm the resulting balance with `getBalance` and report the address, new balance in copper, and equivalent CVM (copper / 1e9).

If the peer rejects the faucet request (`:FUNDS` or similar), explain the likely cause (faucet exhausted, per-request cap, auth required) and suggest checking `peerStatus` or the peer's configuration.

Never invoke the faucet against `convex-local` unless the user explicitly asks — local peers typically have no faucet configured.
