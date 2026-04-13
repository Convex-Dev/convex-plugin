---
name: transact
description: End-to-end client-side signed transaction. Prepares the transaction on the peer, signs it locally with convex-cli, submits the result. Produces one answer — use this when you don't need to see intermediate steps.
argument-hint: "<source> --key <publicKey> --address <addr> [--server testnet|local]"
allowed-tools: Bash(convex key sign:*)
---

Execute a Convex transaction end to end with **client-side signing**. The Ed25519 private key stays in the user's local keystore; the peer never sees a seed.

Parse `$ARGUMENTS`:
- `<source>` — Convex Lisp source expression to execute (required, first positional)
- `--key <publicKey>` — hex public key of the signing key in the local keystore (required)
- `--address <addr>` — address to execute as, e.g. `#12` (required)
- `--server testnet|local` — which MCP server to use, defaults to `testnet`

**Run the full chain silently:**

1. Call the `prepare` MCP tool on `convex-${server:-testnet}` with `source` and `address`. Capture the returned transaction hash.
2. Invoke `convex key sign --key <publicKey> --hex <hash>` via Bash. The user's terminal will prompt for the keystore passphrase. Capture the signature from stdout.
3. Call the `submit` MCP tool with the original transaction plus the signature.
4. **Report only the final result** — the transaction's return value on success, or a clear error message identifying which step failed on failure. Do not narrate the intermediate hash or signature unless an error occurs.

**Error handling:**
- If any step fails, stop immediately. Identify which step: `prepare`, local signing, or `submit`. Include the peer's error code (`:FUNDS`, `:NOBODY`, `:STATE`, etc.) or the CLI's stderr.
- Never retry `submit` with the same signature after a failure — the account state may have changed and retries are unsafe.
- If `convex` CLI is not installed, direct the user to install convex-cli and stop.

**When to use this vs `/convex:sign`:**
- Use `/convex:transact` for routine operations where the user trusts the flow and just wants the outcome.
- Use `/convex:sign` when the user wants to see the transaction hash and signature for audit, debugging, or learning.

**Safety:**
- The user's passphrase and seed are never printed or logged.
- The transaction source `$ARGUMENTS` is shown back in the summary so the user can verify what ran.
