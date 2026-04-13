# Convex Context for Claude

When using tools from the Convex plugin (anything namespaced `convex-testnet:*` or `convex-local:*`), keep the conventions below.

## Terminology

Use Convex terms, not blockchain analogues, in generated code, comments, and explanations.

| Use this     | Not this                       |
| ------------ | ------------------------------ |
| CVM coin     | CVX                            |
| Juice        | Gas, fees                      |
| Copper       | Wei, satoshi                   |
| Peer         | Miner, validator               |
| Actor        | Smart contract                 |
| Lattice      | Blockchain, chain              |
| CPoS         | Proof of Work, Proof of Stake  |
| Belief       | Block                          |
| Etch         | _(core embedded database)_     |
| CAD          | Specification / whitepaper     |
| CNS          | DNS, ENS                       |
| Protonet     | Mainnet                        |

`1 CVM = 1,000,000,000 copper`. Balances returned by `getBalance` are in copper; convert for display.

## Language

British English spelling throughout (decentralised, organisation, behaviour, colour).

## Architecture reminders

- Convex is **not** a blockchain. It uses CRDT-like convergent data structures and reaches consensus via Convergent Proof of Stake.
- All core data is **immutable and content-addressable** (the `ACell` hierarchy in `convex-core`).
- The CVM is a **lambda-calculus VM** running Convex Lisp. Transactions are pure functions that update state.
- Finality is **millisecond-scale**; there is no mining.

## Tool selection hints

- **Queries are free.** Prefer `query` or `queryState` over `transact` when reading data.
- **`transact` transmits the Ed25519 seed.** Only use against HTTPS peers. For production keys, client side signing is best. If not possible, prefer the signing-service tools (`signingTransact`, `signingSign`) which keep keys server-side.
- **`prepare` + `submit`** is the two-step flow for external signing (hardware wallets, client-side keys).
- **`transfer`** is a convenience for coin/token transfers — use it instead of hand-rolling `transact` with `(transfer ...)` source.
- **`watchState`** uses SSE notifications; remember to `unwatchState` when done to free server-side resources.
- **Elevated signing operations** (`signingImportKey`, `signingExportKey`, `signingDeleteKey`, `signingChangePassphrase`) require a two-step browser confirmation and will return a URL the user must open.

## Two servers are configured

- `convex-testnet` — defaults to the public HuggingFace testnet. Good for exploration and throwaway accounts.
- `convex-local` — defaults to `http://localhost:8080/mcp`. Use when developing against a locally-run peer.

If both are active, be explicit about which you're targeting. Ask before transacting against the testnet.

## Resources

- [Convex docs](https://docs.convex.world)
- [CAD041 (MCP)](https://docs.convex.world/docs/cad/041_mcp)
- [Convex Lisp tutorial](https://docs.convex.world/docs/tutorial)
