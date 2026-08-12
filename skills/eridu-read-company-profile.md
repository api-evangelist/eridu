---
name: Read the Eridu company profile
description: >-
  Assemble Eridu's public company facts — what it builds, who runs it, where it is, how to reach it —
  from the pages and search endpoints rather than by scraping the website.
api: openapi/eridu-pages-api-openapi.yml
operations: [listPages, getPage, search, listUsers]
generated: '2026-08-12'
method: generated
source: openapi/eridu-pages-api-openapi.yml, openapi/eridu-search-api-openapi.yml, data-model/eridu-data-model.yml
---

# Read the Eridu company profile

Everything Eridu says about itself lives in 13 WordPress pages. The API gives you the same content the
website renders, already separated from the theme.

**Base URL:** `https://eridu.ai/wp-json`
**Auth:** none.

## Steps

1. **Enumerate the pages.**
   `listPages` — `GET /wp/v2/pages?per_page=100&_fields=id,slug,link,title,parent,menu_order`
   At capture, the 13 published pages are: `home`, `company`, `leadership`, `careers`, `contact`,
   `resources`, `blog`, `press-releases`, `newsletter`, `terms-of-use`, `tcs`,
   `salestermsconditions`, `privacy`.

2. **Pull the ones that carry the facts.** `getPage` — `GET /wp/v2/pages/{id}`.
   - `company` — what Eridu builds: a clean-sheet network switch architecture across silicon, systems
     and software, aimed at the AI interconnect bottleneck.
   - `leadership` — the executive team and board. This page is the authoritative roster; it lists the
     CEO, CPO, CTO, chief systems architect and the board seats held by the lead investors.
   - `contact` — headquarters address and the general contact address.
   - `terms-of-use`, `privacy`, `salestermsconditions` — the legal surface.

3. **Strip the markup.** `content.rendered` is HTML. Remove tags and unescape entities. The leadership
   page in particular is a card grid, so names, titles and LinkedIn links arrive interleaved — parse
   structurally, do not regex a single blob.

4. **Look up a page you cannot name.** `search` — `GET /wp/v2/search?search=<terms>&per_page=20`.
   Returns `{id, title, url, type, subtype}` across posts and pages (14 searchable objects at
   capture). Dereference `_links.self` to fetch the full record.

5. **Get the public authors.** `listUsers` — `GET /wp/v2/users?_fields=id,name,slug,link`.
   Two records at capture. These are CMS accounts, **not** the leadership roster — do not present them
   as the executive team. Use the `leadership` page for people.

## Rules

- **Prefer the API to the rendered HTML.** The homepage is a 49KB theme shell; the same copy arrives
  as clean fields here.
- **Hierarchy:** `blog` and `press-releases` are children of `resources` — follow `parent` and
  `_links.up`, do not infer nesting from the URL.
- **Company facts that are NOT on this surface** — funding, investors, headcount, product roadmap —
  are only in the press releases (see `eridu-track-announcements.md`) and third-party coverage. Do not
  synthesise them from page copy.
- **There is no pricing, no signup, and no product API.** If a task asks you to call an Eridu product
  API, the correct answer is that none is published (`plans/eridu-plans-pricing.yml`).
