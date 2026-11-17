# orsel skills

Agent skills for [orsel](https://github.com/egeuysall/orsel): use any website as a typed API from an agent, with approvals for anything that writes.

```bash
npx skills add egeuysall/orsel-skills
```

Install one: `npx skills add egeuysall/orsel-skills --skill orsel-explore`. List them first with `--list`.

| skill | use it for |
| --- | --- |
| `orsel` | start here: find, read, act, explore, the safety rules, and which skill to load next |
| `orsel-explore` | turn a website into an API: read, list, search, forms, its own published API |
| `orsel-approvals` | anything that is not a plain read: risk classes, approvals, secrets |
| `orsel-sdk` | TypeScript and Python: `site()`, typed calls, errors, retries |
| `orsel-mcp` | the MCP tools: which to call, what a model may and may not do |
| `orsel-operate` | runs, the ledger, usage, reliability, watches, policy |

These files are generated from one source in the orsel repository. Edit them there, not here.
