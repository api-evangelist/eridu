---
name: Track Eridu announcements and press
description: >-
  Pull Eridu's news and press archive from the corporate site's content API, with the tags and
  categories needed to filter it, and know why the archive is nearly empty.
api: openapi/eridu-content-api-openapi.yml
operations: [listPosts, getPost, listCategories, listTags, listUsers]
generated: '2026-08-12'
method: generated
source: openapi/eridu-content-api-openapi.yml, openapi/eridu-taxonomy-api-openapi.yml, conventions/eridu-conventions.yml
---

# Track Eridu announcements and press

Eridu emerged from stealth in March 2026. Its Resources section carries a blog and a press-releases
page, both backed by the same WordPress `post` type. **At capture the entire archive is one post** —
this is a young company, not a broken fetch. Treat a small result set as the expected answer.

**Base URL:** `https://eridu.ai/wp-json`
**Auth:** none.

## Steps

1. **List posts newest-first.**
   `listPosts` — `GET /wp/v2/posts?orderby=date&order=desc&per_page=100&_fields=id,date,modified,slug,link,title,excerpt,categories,tags,author`
   - `_fields` keeps the payload small; without it every response carries the full rendered HTML body.
   - Read `X-WP-Total` and `X-WP-TotalPages` from the response headers to size the archive. These are
     reliable on `posts` (unlike `media` — see `examples/eridu-examples.yml`).

2. **Poll incrementally.** On later runs pass `modified_after=<ISO 8601 of your last run>` so you only
   receive changed posts. `after` / `before` filter on publication date instead.

3. **Fetch one post in full.** `getPost` — `GET /wp/v2/posts/{id}`.
   `title.rendered`, `excerpt.rendered` and `content.rendered` are **HTML strings**, not plain text —
   strip tags and unescape entities before using them.

4. **Resolve the relationships in one call instead of four.** Append `&_embed` to step 1 or 3.
   `_embedded` then inlines the author (`listUsers`/`getUser`), the featured image
   (`getMediaItem`) and the terms (`listCategories`/`listTags`) that `_links` points at.

5. **Get the filter vocabulary** when you need to segment the archive.
   `listCategories` — `GET /wp/v2/categories?_fields=id,count,name,slug`
   `listTags` — `GET /wp/v2/tags?_fields=id,count,name,slug`
   Then filter with `?categories=<id>` or `?tags=<id>` on step 1. At capture there is exactly one
   category and one tag, so filtering buys you nothing yet.

## Rules

- **Prefer the RSS feed for simple monitoring.** `https://eridu.ai/feed/` is a supported, cacheable
  alternative and costs one request. Use the REST API when you need ids, taxonomy or media.
- **`per_page` is capped at 100.** `per_page=500` returns `400 rest_invalid_param` with the bound
  stated in `data.params.per_page`. Page with `page=N`, 1-based.
- **Ids are not stable identity.** They are WordPress post ids, meaningful only on eridu.ai and not
  preserved across a site migration. Key on `slug` + `link` if you are storing records.
- **No `ETag` or `Last-Modified`** is returned, so conditional requests are not available. Use
  `modified_after` for incrementality instead.
- **Do not scrape the HTML pages** to fill gaps. `/wp-json/` responses carry `x-robots-tag: noindex`
  and the site's `robots.txt` allows everything but `/wp-admin/`; stay on the API.
