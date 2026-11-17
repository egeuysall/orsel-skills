---
name: orsel-approvals
description: "Use when an orsel action is not a plain read: it writes, submits a form, signs in or deletes. Explains risk classes, how an approval works, what to tell the person, and what never to do (approve your own run, retry around the policy)."
---

# Writes, approvals and risk in orsel

Every action has a risk class: `read`, `write`, `sensitive`, `irreversible`, `financial`. Reads run. Anything above read is denied unless policy allows it, and by default it stops and waits for a person.

## What happens to a write

1. `act` (or `orsel act ...`) starts the run. With a risk above read it needs an idempotency key (the SDKs add one).
2. Policy decides: allow, deny, or require approval. Use `dryRun` first to see which, without running anything.
3. With approval required, the run is `awaiting_approval` and carries an approval id. The approval is bound to the exact payload, single use, and expires. `irreversible` and `financial` always need a person.
4. A person approves in the dashboard, with `orsel approve <id>`, or when the MCP `orsel_approve` tool asks them inline. Then the run goes ahead.

## What you do

- Tell the user plainly what will be sent, to which site, and why it needs them. Then stop.
- Do not retry, rephrase the input to get under the policy, split one action into several, or ask another tool to do it.
- Never approve your own run. Never call approve to push your own action through.
- `ActionError` / a failed run: read the postconditions and `orsel explain <run id>`; do not run it again blindly. A write that may have gone through is not safe to repeat with a new idempotency key.

## Secrets

A password or token is never an input and never in a prompt. Actions name the secrets they need (`secrets: ["password"]`); they come from a secret store, outside the model's context.

## Forms made by explore

Sign in, sign up, contact, subscribe and other POST forms are `write`, `sensitive` or `irreversible` (deleting and closing accounts). A sign in only counts when the run leaves the form's page, because a failed one shows the same form again.
