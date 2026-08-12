---
name: Harvest Eridu media assets
description: >-
  Pull logos, leadership portraits and press imagery from the eridu.ai media library, and page it
  correctly around a real defect where the total header disagrees with what you can actually read.
api: openapi/eridu-media-api-openapi.yml
operations: [listMedia, getMediaItem, getOembed]
generated: '2026-08-12'
method: generated
source: openapi/eridu-media-api-openapi.yml, examples/eridu-examples.yml, conventions/eridu-conventions.yml
---

# Harvest Eridu media assets

The media library behind eridu.ai holds leadership portraits, careers and team photography, investor
logos and press imagery, each with WordPress-generated size variants.

**Base URL:** `https://eridu.ai/wp-json`
**Auth:** none.

## Steps

1. **List attachments at the maximum page size.**
   `listMedia` — `GET /wp/v2/media?per_page=100&_fields=id,slug,source_url,mime_type,media_type,alt_text,media_details`
   `media_details.sizes` carries every generated variant with its own `source_url`, `width` and
   `height`. Pick the variant you need instead of downloading the full-size original.

2. **Page until the body runs short — not until `X-WP-Total` is satisfied.** See the rule below.

3. **Fetch one attachment.** `getMediaItem` — `GET /wp/v2/media/{id}`, or follow
   `_links.wp:featuredmedia` from a post.

4. **Need an embeddable card rather than a raw file?** `getOembed` —
   `GET /oembed/1.0/embed?url=<absolute eridu.ai post or page URL>`. Returns oEmbed 1.0 JSON with
   `title`, `author_name`, `thumbnail_url` and iframe `html`. Pass `maxwidth`/`maxheight` to size it.
   The URL **must** be an eridu.ai URL; anything else is rejected.

## Rules

- **`X-WP-Total` lies on this collection — do not drive your paging loop from it.**
  Verified 2026-08-12: the header reports **115** attachments, but an anonymous caller receives at
  most **21** items no matter how it pages — `per_page=10` returns 2, `per_page=50` returns 12,
  `per_page=100` returns 21, each still advertising `X-WP-Total: 115`. WordPress computes the total
  before applying the per-attachment read filter and then removes attachments whose parent post is not
  publicly readable. A loop that trusts the header will request permanently empty pages and conclude
  the API is down. **Stop when a page returns fewer items than `per_page`, or when a page is empty.**
- **`_fields` does not fix it.** The shortfall is a permission filter, not a projection.
- **Licensing is not granted by reachability.** These are Eridu's corporate marketing assets,
  including third-party investor logos and identifiable photographs of employees. The site's Terms of
  Use govern them; anonymous read access is not a licence to republish.
- **Errors** use the WordPress envelope; an unknown id returns `404` with
  `code: rest_post_invalid_id`.
