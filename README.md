# Eridu

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Eridu is an AI networking infrastructure company founded in 2024 and headquartered in Saratoga,
California. It is building a clean-sheet network switch architecture — silicon, systems and software
together — aimed at the interconnect bottleneck that limits how efficiently GPUs exchange data inside
large AI training clusters. Co-founded by CEO Drew Perkins (previously Lightera Networks, acquired by
Ciena, and Infinera) with Chief Product Officer Omar Hassen, it emerged from stealth in March 2026
with an oversubscribed Series A of more than $200M led by Socratic Partners, bringing total funding to
approximately $230M.

- Website: https://eridu.ai/
- Company: https://eridu.ai/company/ · Leadership: https://eridu.ai/company/leadership/
- Resources: https://eridu.ai/resources/ · Careers: https://ats.rippling.com/eridu-ai/jobs

## What this profile found

Eridu is a semiconductor and systems company, not a software vendor. **It publishes no developer
program** — no developer portal, no API reference, no product API, no SDKs, no pricing, no GitHub
organization, no MCP server, no A2A agent card, no webhooks and no `/.well-known/` documents. Every
one of those is a measured absence recorded in the artifacts below, not an unchecked gap.

The one machine-readable interface Eridu serves is the **WordPress core REST API** behind its
corporate site at `https://eridu.ai/wp-json` — anonymous, effectively read-only, and carrying company
content rather than product data. It is catalogued here for discovery. The seven OpenAPI documents in
`openapi/` are API Evangelist derivations of the provider's own live self-describing route index
(saved verbatim to `openapi/_original/eridu-wp-json-index.json`); Eridu publishes no OpenAPI.

Two findings worth surfacing:

- **`/wp/v2/media` reports a total it will not serve.** `X-WP-Total` says 115 attachments; an
  anonymous caller receives at most 21, however it pages. WordPress computes the total before
  applying the per-attachment read filter. A client that drives its paging loop from the header will
  request permanently empty pages. Recorded in `examples/eridu-examples.yml`.
- **The `wp-abilities/v1` namespace is registered but auth-gated.** This is the agent-facing Abilities
  surface WordPress core is standardising on; `GET /wp-json/wp-abilities/v1/abilities` returns 401
  anonymously, so its ability list could not be enumerated and nothing was inferred from it.

## Artifacts

| Directory | What is in it |
|---|---|
| `apis.yml` | APIs.json 0.20 profile — company identity, 7 API entries, artifact pointers |
| `openapi/` | 7 OpenAPI 3.1 documents, 20 operations, derived from the live route index |
| `openapi/_original/` | The provider's own `/wp-json/` route index, verbatim |
| `overlays/` | OpenAPI Overlay 1.0.0 per spec, recording API Evangelist enhancements |
| `authentication/` | Auth profile — HTTP Basic application passwords, writes only |
| `conventions/` | Pagination, field selection, hypermedia, tracing, versioning, caching |
| `errors/` | The WordPress error envelope and five live-captured problem types |
| `examples/` | 10 worked requests + 5 error cases, all captured live |
| `data-model/` | Entity graph and relationships, derived from `_links` and the type registry |
| `conformance/` | 19 standards assessed — what it does and does not conform to |
| `lifecycle/` | Versioning, stability risk, and the absence of status page / deprecation policy |
| `rate-limits/` | Measured zero — no rate-limit headers are returned |
| `plans/` | Measured zero — no pricing of any kind is published |
| `packages/` | Measured zero — with the two same-named third-party packages explicitly excluded |
| `well-known/` | Probe record: every `/.well-known/` path returned 404 |
| `security/` | TLS/DNS posture probe (no HSTS, no DNSSEC, no CAA; SPF and DMARC present) |
| `mcp/` | Candidate tool manifest + tool crosswalk — Eridu operates no MCP server |
| `agentic-access/` | Recommended `x-agentic-access` contracts for all 20 operations |
| `skills/` | 4 packaged Agent Skills grounded in real operationIds |
| `llms/` | `llms.txt` generated for this profile (Eridu serves none) |

Not present because the provider has no such surface: `asyncapi/`, `a2a/`, `graphql/`, `grpc/`,
`scopes/`, `sandbox/`, `cli/`, `components/`, `changelog/`.
