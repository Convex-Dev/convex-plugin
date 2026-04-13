# Changelog

All notable changes to this plugin will be documented here. Versions follow [semver](https://semver.org/).

## [Unreleased]

### Added

- Two MCP server bindings: `convex-testnet` (public HuggingFace testnet by default) and `convex-local` (localhost:8080 by default), both overridable via environment variables.
- `convex` subagent preloaded with Convex platform context, terminology, and tool-selection guidance.
- `/convex:faucet <address> [amount]` slash command for quick testnet funding.
- `/convex:explore <address-or-cns-name>` slash command for account inspection.
- Plugin-level `CLAUDE.md` injecting Convex terminology and conventions (juice not gas, copper, peer not miner, lattice not blockchain) so generated code stays consistent.
- GitHub Actions workflow validating `plugin.json`, `.mcp.json`, and agent/command frontmatter on every push.
- Troubleshooting section in README covering peer reachability, `MCP_TIMEOUT` tuning, auth errors, and elevated-operation browser confirmations.

## [0.1.0] — 2026-04-13

### Added

- Initial plugin scaffold targeting the Convex peer MCP endpoint (CAD041).
