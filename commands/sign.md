---
name: sign
description: Client-side signed transaction with visible steps — prepare on the peer, sign locally via convex-cli, then submit. Shows the intermediate hash and signature so the user can see what's happening.
argument-hint: "<source> --key <publicKey> --address <addr> [--server testnet|local]"
allowed-tools: Bash(convex key sign:*)
---

Walk the user through a **client-side signed transaction**. The Ed25519 private key stays in their local keystore; the peer only sees the public key, the transaction, and the detached signature.

Parse `$ARGUMENTS`:
- `<source>` — Convex Lisp source expression to execute (required, first positional)
- `--key <publicKey>` — hex public key of the signing key in the local keystore (required)
- `--address <addr>` — address to execute the transaction as, e.g. `#12` (required)
- `--server testnet|local` — which MCP server to use, defaults to `testnet`

**Steps (run them in order, surfacing the intermediate values):**

1. **Prepare** — call the `prepare` MCP tool on `convex-${server:-testnet}` with `source` and `address`. Extract the returned transaction `hash` (hex). Show it to the user as "Transaction hash: 0x…".

2. **Sign locally** — run via Bash:
   ```
   convex key sign --key <publicKey> --hex <hash>
   ```
   The user will be prompted for their keystore passphrase in their own terminal. Capture the signature from stdout. Show it as "Signature: 0x…".

3. **Submit** — call the `submit` MCP tool with the original transaction (address, source) plus the signature. Report the final result: success with the return value, or the CVM error code on failure.

**Error handling:**
- If `prepare` fails, report the error and stop. Do not proceed to signing.
- If `convex key sign` fails (key not found, wrong passphrase), report the error and stop. Do not call `submit`.
- If `submit` fails, report the error code and whatever the peer returned. **Do not retry** — a failed submit with a valid signature usually means the transaction was malformed or the account state changed; retrying blindly is unsafe.

**Safety notes:**
- Never echo or log the user's passphrase or seed.
- The hash shown in step 1 is fine to log — it is the content address of the transaction and carries no secret.
- If the user's `convex` CLI is not installed, direct them to [the convex-cli README](https://github.com/Convex-Dev/convex/tree/develop/convex-cli) and stop.
