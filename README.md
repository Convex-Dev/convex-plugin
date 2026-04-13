# Convex Plugin for Claude Code

Connect Claude Code to a [Convex](https://convex.world) peer via the Model Context Protocol. Once installed, Claude can query the lattice, run transactions, manage accounts and keys, resolve CNS names, and watch on-chain state in real time. The plugin also injects Convex terminology and conventions so generated code and explanations stay consistent with the platform.

Convex is a lattice-based decentralised network with a lambda-calculus virtual machine, Convergent Proof of Stake consensus, and millisecond finality. See the [documentation](https://docs.convex.world) for background.

## Install

```bash
claude plugin install https://github.com/Convex-Dev/convex-plugin
```

Or, for local development against an unpublished copy:

```bash
claude --plugin-dir ./convex-plugin
```

## What you get

- **MCP tools** served by the peer — the full current set, including queries, transactions, cryptography, encoding, account management, signing-service operations, and state watches.
- **MCP prompts** — guided workflows such as account exploration, network status, creating accounts, deploying contracts, and transferring funds.
- **CLAUDE.md context** — Convex terminology (juice not gas, copper, peer not miner, lattice not blockchain), British English spelling, and tool-selection hints so Claude picks the right MCP tool the first time.
- **Slash commands** — `/convex:explore`, `/convex:faucet`, `/convex:sign`, `/convex:transact` wrap common flows. The sign/transact pair delegates Ed25519 signing to your local `convex-cli` keystore so private keys never leave your machine.

## Configuration

The plugin registers two MCP servers so you can switch between networks without editing anything:

| Server            | Default URL                                              | Override env var          |
| ----------------- | -------------------------------------------------------- | ------------------------- |
| `convex-testnet`  | `https://mikera1337-convex-testnet.hf.space/mcp`         | `CONVEX_PEER_URL`         |
| `convex-local`    | `http://localhost:8080/mcp`                              | `CONVEX_LOCAL_URL`        |

Auth tokens (optional — read-only tools work without one):

| Variable                   | Applies to        |
| -------------------------- | ----------------- |
| `CONVEX_AUTH_TOKEN`        | `convex-testnet`  |
| `CONVEX_LOCAL_AUTH_TOKEN`  | `convex-local`    |

Example — point the testnet binding at your own hosted peer:

```bash
export CONVEX_PEER_URL=https://peer.example.com/mcp
export CONVEX_AUTH_TOKEN=your_token_here
claude
```

Disable a server you don't need via Claude Code's `/mcp` panel.

## Running a local peer

The `convex-local` binding connects to `http://localhost:8080/mcp` — the default REST API port of a Convex peer. To run one locally:

```bash
# Download or build convex.jar (see https://github.com/Convex-Dev/convex)
java -jar convex.jar local start
```

That starts a single-peer local lattice with the peer binary protocol on `18888` and the REST/MCP endpoint on `8080`. Open Claude Code and the `convex-local` tools will be live.

Notes for local development:

- **No HTTPS.** The plugin does not block seed-based tools over `http://localhost`, but you should treat local keys as disposable. Do not point `CONVEX_LOCAL_URL` at a plain-HTTP non-localhost peer.
- **Faucet.** `convex local start` configures a faucet by default; `/convex:faucet` works against `convex-local` once you override it to target the local server.
- **Signing service.** Not enabled by default on local peers — the 13 `signing*` tools will be absent unless you configure one. The core 22 tools always work.
- **Custom port.** If you run the peer on a non-default port, set `CONVEX_LOCAL_URL=http://localhost:NNNN/mcp` before launching Claude Code.

## Security notes

- **HTTPS is required for seed-based tools** (`transact`, `signAndSubmit`, `transfer`) since they transmit Ed25519 seeds. Do not point the plugin at a plain-`http://` peer for any signing work beyond local development.
- **The signing service keeps private keys server-side** and never returns raw seeds; elevated operations (import, export, delete, change-passphrase) require an interactive browser confirmation.
- **Choose a peer you trust.** A malicious peer can return misleading query results or refuse to submit your transactions. For production use, run your own peer.

## Troubleshooting

**"Connection refused" / peer unreachable** — check that your peer is running and reachable from the machine Claude Code is on: `curl $CONVEX_PEER_URL/.well-known/mcp`. For local peers, confirm the port matches `CONVEX_LOCAL_URL`.

**Tools don't appear** — restart Claude Code after changing env vars. Use `/mcp` to list active servers and inspect connection status.

**Slow testnet** — the public HF testnet can be slow to cold-start. Raise the MCP timeout:

```bash
MCP_TIMEOUT=30000 claude
```

**`401 Unauthorized`** — the peer requires auth. Set `CONVEX_AUTH_TOKEN` (or `CONVEX_LOCAL_AUTH_TOKEN`) to a valid bearer token.

**`403 Forbidden: invalid origin`** — the peer is enforcing Origin-header restrictions. Claude Code sends no Origin header by default, so this usually means the peer is misconfigured; see [CAD041 security notes](https://docs.convex.world/docs/cad/041_mcp) on origin handling.

**Elevated signing operation opened a browser** — this is expected for `signingImportKey`, `signingExportKey`, `signingDeleteKey`, and `signingChangePassphrase`. Approve in the browser and the tool call will resume.

## Requirements

- Claude Code installed (`claude --version`)
- A reachable Convex peer serving the MCP endpoint (part of `convex-restapi` since 2025)
- Java is not required on the client — the plugin only speaks HTTP to the peer

## Related

- [Convex core](https://github.com/Convex-Dev/convex) — peer implementation and MCP server
- [CAD041: Model Context Protocol](https://docs.convex.world/docs/cad/041_mcp) — specification
- [MCP specification](https://modelcontextprotocol.io/specification/2025-11-25)

## Licence

Apache-2.0. See [LICENSE](LICENSE).
