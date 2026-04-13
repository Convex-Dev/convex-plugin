---
name: explore
description: Explore a Convex account — balance, metadata, controller, environment symbols, and actor code if the account is an actor. Accepts either a numeric address or a CNS name.
argument-hint: "<address-or-cns-name>"
---

Explore the Convex account identified by $ARGUMENTS.

If $ARGUMENTS starts with `#` or is a plain integer, treat it as an address. Otherwise resolve it first with `resolveCNS` on `convex-testnet`.

Once you have a numeric address:

1. Call `describeAccount` — report the address, balance (copper and CVM), sequence number, controller, and whether the account is an actor (has callable exports).
2. If it is an actor, list the environment symbols via a targeted `query` such as `(keys (:environment (account #NNN)))` and briefly describe the callable functions.
3. Check for recent activity with `peerStatus` — note the current consensus point so the user can contextualise how fresh the account data is.

Keep the summary tight: one short paragraph, plus a bullet list of the key facts. If anything is unusual (zero balance, non-self controller, large env), flag it.

Default to the `convex-testnet` server unless the user has indicated `convex-local`.
