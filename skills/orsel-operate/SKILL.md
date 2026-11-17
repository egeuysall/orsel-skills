---
name: orsel-operate
description: "Use when asked what orsel did or whether it can be trusted: reading a run, explaining a failure, verifying the ledger, usage, reliability numbers, watches, recipes and their status, and what the policy would do."
---

# Operating and auditing orsel

Everything that happens is in a per-project hash-chained ledger. These are the ways to look at it.

## What happened

- `orsel runs` lists recent runs; `orsel runs <id>` shows one: status, tier used, postconditions, trace. Over MCP: `orsel_runs`, `orsel_run`.
- `orsel explain <run id>` says why: the action, the recipe version, the policy rule that fired, the evidence. A run only succeeded if its postconditions passed.
- Failed runs carry a code: `no_recipe` (nothing can run it for this organization: explore the site), `http_404` (the page moved: explore again), `postcondition_failed` (the site answered but not with what was expected), `idempotency_key_required` (a write without a key).

## Can it be trusted

- `orsel verify` recomputes the ledger chain. `ok` means no entry was altered or removed; otherwise it names the first entry that fails.
- `orsel bench` shows measured reliability per action (success rate, postcondition pass rate, latency, drift events). The numbers are measured by canaries and client runs, never estimated; a candidate recipe has none until it is checked.
- `orsel recipes` lists recipes by status: candidate, verified, degraded, quarantined, revoked. Only verified and degraded recipes are shared; a candidate is private to the organization that made it.

## What it would do

- `orsel policy` shows the rules; with `--dry-run` (or `orsel_policy` with domain, actionId, risk) it says what a given action would get: allow, deny or require approval, and which rule fires, without running anything.
- `orsel approvals` lists what is waiting for a person.

## Over time

- `orsel usage`: finds, reads, acts and how many succeeded, approvals requested, search provider calls and cost.
- `orsel watch` watches a page, an action or a search every N minutes (at least 15) and reports changes by webhook to the project.
- `orsel doctor` checks the config, the key, the address and the clock when something looks off.
