---
name: orsel-explore
description: "Use when a website has no API and a task needs to be done on it repeatedly: orsel explore reads the site's public pages, forms and published API once and makes typed actions you can call (read a page, list a section, search, submit a form with approval), from the CLI, TypeScript or Python."
---

# Turn a website into an API with orsel explore

`explore` surveys a site from one public page (robots.txt first, no model, at most 28 pages, half a second apart) and makes actions. Nothing on the site changes while it runs.

## Steps

1. `orsel explore https://<site>/`. Start from the home page for the most. It prints each action as a route, what it takes and returns, and why anything was not made.
2. Call a read: `orsel act <domain> list_blog`, then `orsel act <domain> read_blog_page --set slug=<slug>`. A list returns `slugs` for the read next to it. `act` waits for the result; `--set key=value` is typed from the action.
3. From code, use the site like an API:

```ts
const blog = await orsel.site('<domain>', { explore: true });
const { slugs } = await blog.list_blog();
const post = await blog.read_blog_page({ slug: slugs[0] });
```

```python
blog = orsel.site("<domain>", explore=True)
post = blog.read_blog_page(slug=blog.list_blog()["slugs"][0])
```

4. For typed inputs and outputs: `orsel typegen <domain> --out src/<domain>.ts`, then `orsel.site<TheActions>(domain)`.

## What it makes

- Per section of the site: a read action (`/blog/{slug}`) and a list action (`/blog`).
- Named pages (`/about`), search when the site has a search form that works with a plain link.
- POST forms (sign in, sign up, contact, subscribe, comments, settings) as actions with a risk class that wait for approval. They are checked against the form and never submitted while exploring.
- The site's API, which is where create, update and delete come from: an OpenAPI or Swagger 2 document, routes listed in its docs, routes its own JavaScript calls, or the routes of the platform it runs (WordPress, Shopify storefront and cart, Discourse, Mastodon). Reads are checked against a real answer. Writes are described from the route, never run while exploring, take their body as `input.body`, name the secrets they need, and wait for approval.

## What it does not make (it says so, with the reason)

- Forms behind a captcha, forms that take card or bank details, file uploads, forms that post to another site.
- Long results: pass `--fields`, `--limit` or `--max-chars` to `act` (MCP: `fields`, `limit`, `maxChars`); what was cut is named in `_shaped`. A read's `content` is Markdown in page order.
- Pages drawn by JavaScript, anything behind a login. A single-page app with no public API and no documented routes (its marketing site, say) stays read-only: for its real API, record the traffic (`orsel import` with a HAR) or use the person's own browser.
- Routes found in a site's JavaScript are inferred and unchecked: their input body may need keys the route does not show. Treat a failed first call as information, not a bug in the site.

## Limits to respect

- Ten explorations an hour per organization. Sites with a verified owner are refused: the owner publishes their own actions.
- A password is a secret, never an input; hosted runs have no secret store yet, so sign in and sign up cannot run there.
- Everything explore makes is a candidate only this organization can run until the daily checks verify it.
