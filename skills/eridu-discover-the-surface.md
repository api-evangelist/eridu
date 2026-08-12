---
name: Discover the Eridu API surface
description: >-
  Read Eridu's live self-describing route index before calling anything else, because Eridu publishes
  no developer documentation and its route set is a function of the WordPress plugins installed on
  eridu.ai at that moment.
api: openapi/eridu-discovery-api-openapi.yml
operations: [getApiIndex, listTypes, getTaxonomy, listTaxonomies, listStatuses]
generated: '2026-08-12'
method: generated
source: openapi/eridu-discovery-api-openapi.yml, conventions/eridu-conventions.yml
---

# Discover the Eridu API surface

Eridu is an AI networking silicon company. It ships **no developer program, no docs, no portal, no
SDKs and no OpenAPI**. The only machine-readable interface on `eridu.ai` is the WordPress core REST
API behind the corporate site, and the only authoritative description of it is the API's own index.
Start there every time — do not pin routes from a previous run.

**Base URL:** `https://eridu.ai/wp-json`
**Auth:** none. Every operation in this skill is anonymously readable.

## Steps

1. **Read the index.** `getApiIndex` — `GET /`.
   Returns `name`, `url`, `namespaces[]`, `authentication` and a `routes` object keyed by route
   pattern, each carrying its `methods` and per-endpoint `args` (the real parameter contract).
   At capture: 167 routes across 7 namespaces — `wp/v2`, `oembed/1.0`, `wp-site-health/v1`,
   `wp-block-editor/v1`, `wp-abilities/v1`, `contact-form-7/v1`, `redirection/v1`.

2. **Confirm the namespace you need still exists.** Only `wp/v2` and `oembed/1.0` are core. The other
   five come from plugins or a specific WordPress version and can vanish on any site update, with no
   changelog and no deprecation notice (`lifecycle/eridu-lifecycle.yml`). If your namespace is gone,
   stop — do not fall back to a guessed path.

3. **Enumerate the content model.** `listTypes` — `GET /wp/v2/types`.
   Returns each registered type with its `rest_base` and `rest_namespace`. At capture eridu.ai
   registers **only stock WordPress types** — `post`, `page`, `attachment` and the editor internals.
   There is no company-specific custom post type, so there is no Eridu product data on this surface.

4. **Enumerate the vocabularies.** `listTaxonomies` — `GET /wp/v2/taxonomies`, then `getTaxonomy` for
   any you will filter on. At capture: `category`, `post_tag`, `nav_menu`, `wp_pattern_category`. No
   custom taxonomy.

5. **Check publication statuses.** `listStatuses` — `GET /wp/v2/statuses`. Anonymous callers only ever
   see `publish`; requesting any other status is rejected.

## Rules

- **Never send `context=edit`.** It returns `401 rest_forbidden_context` for anonymous callers.
  Use the default `context=view`.
- **`wp-abilities/v1` is a dead end anonymously.** The namespace is registered — this is the
  agent-facing Abilities surface WordPress core is standardising on — but
  `GET /wp-json/wp-abilities/v1/abilities` returns **401**. Its ability list cannot be enumerated
  without a WordPress application password. Do not infer abilities; there is nothing to read.
- **Do not call the `redirection/v1` or `contact-form-7/v1` write routes.** They are site
  administration and form submission, not a public API.
- **Errors** are the WordPress envelope `{code, message, data:{status}}`, not RFC 9457. Match on
  `code`, never on `message`. See `errors/eridu-problem-types.yml`.
- **There is no rate-limit signal.** No `RateLimit-*`, no `Retry-After`. Self-throttle; there is no
  published budget to read (`rate-limits/eridu-rate-limits.yml`).
