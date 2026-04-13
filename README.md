# Convex Plugin for Claude Code

Connect Claude Code to a [Convex](https://convex.world) peer via the Model Context Protocol. Once installed, Claude gains around 34 tools for querying the lattice, running transactions, managing accounts and keys, resolving names, and watching on-chain state in real time, plus a set of guided prompts for common workflows.

Convex is a lattice-based decentralised network with a lambda-calculus virtual machine, Convergent Proof of Stake consensus, and millisecond finality. See the [documentation](https://docs.convex.world) for background.

## Install

```bash
claude plugin install https://github.com/Convex-Dev/convex-plugin
```

Or, for local development:

```bash
claude --plugin-dir ./convex-plugin
```

## Configuration

By default the plugin connects to the public HuggingFace testnet peer:

```
https://mikera1337-convex-testnet.hf.space/mcp
```

Point it at a different peer by setting environment variables before launching Claude Code:

| Variable             | Purpose                                    | Default                                                |
| -------------------- | ------------------------------------------ | ------------------------------------------------------ |
| `CONVEX_PEER_URL`    | MCP endpoint of your Convex peer           | `https://mikera1337-convex-testnet.hf.space/mcp`       |
| `CONVEX_AUTH_TOKEN`  | Bearer token for authenticated operations  | _(empty — read-only tools work without it)_            |

Example:

```bash
export CONVEX_PEER_URL=https://peer.example.com/mcp
export CONVEX_AUTH_TOKEN=your_token_here
claude
```

## What you get

Tool categories exposed by the peer:

- **Queries and state** — `query`, `queryState`, `describeAccount`, `getBalance`, `lookup`, `resolveCNS`, `peerStatus`, `getTransaction`
- **Transactions** — `transact`, `prepare` / `submit`, `signAndSubmit`, `transfer`
- **Accounts** — `createAccount` (optional faucet funding)
- **Cryptography** — `keyGen`, `sign`, `validate`, `hash`
- **Encoding** — `encode`, `decode` (CVM literals ↔ CAD3 binary)
- **State watching** — `watchState`, `unwatchState` (real-time notifications over SSE)
- **Signing service** _(if the peer is configured with one)_ — server-side key management, elevated operations guarded by browser confirmation

Prompts cover onboarding, exploring accounts, checking network status, creating accounts, deploying contracts, and transferring funds.

## Security notes

- **HTTPS is required for seed-based tools** (`transact`, `signAndSubmit`, `transfer`) since they transmit Ed25519 seeds. Do not point the plugin at a plain-`http://` peer for any signing work.
- **The signing service keeps private keys server-side** and never returns raw seeds; elevated operations (import, export, delete) require an interactive browser confirmation.
- **Choose a peer you trust.** A malicious peer can return misleading query results or refuse to submit your transactions. For production use, run your own peer.

## Requirements

- Claude Code installed (`claude --version`)
- A reachable Convex peer running the MCP endpoint (part of `convex-restapi` since 2025)
- Java is not required on the client — the plugin only speaks HTTP to the peer.

## Related

- [Convex core](https://github.com/Convex-Dev/convex) — peer implementation and MCP server
- [CAD041: Model Context Protocol](https://docs.convex.world/docs/cad/041_mcp) — specification
- [MCP specification](https://modelcontextprotocol.io/specification/2025-11-25)

## Licence

Apache-2.0, matching the core Convex project.
