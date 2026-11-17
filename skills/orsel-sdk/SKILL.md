---
name: orsel-sdk
description: "Use when writing code against orsel in TypeScript or Python: the client, site() to use a website as a typed API, typegen, ActionError, waiting for approvals, retries and idempotency, or the REST contract underneath."
---

# orsel from code

REST under `/v1` is the contract; the TypeScript package `orsel` and the Python package `orsel` are thin: same operations, same names. The key is in `ORSEL_KEY`, the address in `ORSEL_URL`, an optional project in `ORSEL_PROJECT`.

## Setup

```ts
import { Orsel } from 'orsel';
const orsel = new Orsel(); // reads the environment
```

```python
from orsel import Orsel   # AsyncOrsel has the same surface as coroutines
orsel = Orsel()
```

## Use a site like an API

```ts
const blog = await orsel.site('egeuysal.com', { explore: true });
const { slugs } = await blog.list_blog();
const post = await blog.read_blog_page({ slug: slugs[0] });
```

```python
blog = orsel.site("egeuysal.com", explore=True)
post = blog.read_blog_page(slug=blog.list_blog()["slugs"][0])
```

One method per action; it waits for the run and returns the typed output. A run that does not succeed raises `ActionError` (`.run`, `.code`, a message that says what failed and how to look closer). A missing input is named before anything is sent. `site.run(id, input)` returns the whole run without raising. Only actions this organization can run are listed.

## Types

`orsel typegen <domain> --out src/<domain>.ts`, then `orsel.site<TheActions>(domain)`: typed inputs and outputs, with each action's risk class in its comment. Regenerate when the site's actions change.

## The operations

`find`, `read`, `explore`, `act`, `explain`, `approve`, `deny`, `revoke`, `watch` are methods on the client; the rest are namespaces: `actions`, `runs`, `approvals`, `sites`, `recipes`, `policy`, `ledger`, `benchmark`, `watches`, `webhooks`, `keys`. `act(input, { wait: true })` waits for the run, and for an approval first.

## Behavior to rely on

- Every act carries an idempotency key (`sdk-<uuid>` unless you pass one). Writes are never retried automatically; reads, finds and dry runs are. To retry a write yourself, reuse the key.
- A write waits for a person: `ApprovalRequiredError` if nobody decides in time (`.approvalId`, `.runId`); approve and wait again.
- Errors are `ApiError` (`.code`, `.status`, `.requestId`), `RateLimitError` (`.retryAfter`), `NetworkError`, `TimeoutError`. Switch on `.code`, not the message.
- The ledger verifier and webhook verification work offline (`orsel/verify`, `orsel/webhooks`).

## REST, when there is no SDK

`POST /v1/act` with `Authorization: Bearer $ORSEL_KEY`, `X-Project-Id` for org-wide keys, and `Idempotency-Key` on anything above read. Errors are `{ error: { code, message, requestId } }`. The OpenAPI document is at `/v1/openapi.json`.
