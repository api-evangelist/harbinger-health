---
name: Ground an agent on Harbinger Health from primary sources
description: >-
  Assemble an accurate, citable picture of Harbinger Health — the company, the Harbinger HX
  platform and the RESOLVE test — using only the company's own public content API, and know
  precisely where that surface stops so the agent never over-claims.
api: openapi/harbinger-health-wordpress-wp-v2-openapi.yml
operations:
  - listSearch
  - listPages
  - getPagesById
  - listPosts
  - listTypes
  - listMedia
generated: '2026-08-04'
method: generated
---

# Ground an agent on Harbinger Health

Use this when an agent needs to answer questions about Harbinger Health from the company's own
words rather than from secondary coverage.

Base URL: `https://harbinger-health.com/wp-json`. No authentication.

## Steps

1. **Map the site** — call `listTypes` (`GET /wp/v2/types`) to see which content collections exist
   on this install. On 2026-08-04 that is `post`, `page`, `media`, `events` plus WordPress internals.

2. **Search across everything** — call `listSearch`
   (`GET /wp/v2/search?search=<term>&per_page=20`). This is the cheapest single call and spans posts,
   pages and terms. Each result carries `id`, `title`, `url`, `type` and `subtype`; use `type` to
   decide which detail operation to call next.

3. **Read the canonical pages** — call `listPages` (`GET /wp/v2/pages?per_page=100&_fields=id,slug,link,title`)
   and pull the ones that carry the company's own claims:
   - `about` — founding, Flagship Pioneering, leadership, Cambridge MA
   - `resolve` — the RESOLVE test positioning
   - `platform-technology` — the Harbinger HX platform description
   - `the-science` — the developmental-biology thesis
   - `partnerships`, `job`, `contact`, `terms-of-use`, `privacy-policy`

   Then call `getPagesById` (`GET /wp/v2/pages/{id}`) for `content.rendered`.

4. **Add the dated record** — call `listPosts` for announcements: funding, AACR/ASCO data
   presentations, publications. Sort `orderby=date&order=desc` and cite `link` plus `date`.

5. **Pull assets if needed** — call `listMedia` (`GET /wp/v2/media?parent=<post id>`) for figures and
   PDFs attached to a given article.

## Hard limits — state these rather than guessing past them

- There is **no clinical, laboratory, diagnostic, genomic, specimen, result, order or patient data**
  on any public Harbinger Health surface. No FHIR, no HL7, no GA4GH. If asked for RESOLVE results,
  assay parameters, performance data beyond what a press release states, or anything patient-level,
  the correct answer is that it is not published.
- There is **no developer API, no documentation, no SDK, no pricing and no sign-up**. The
  `developer.`, `docs.`, `api.`, `portal.`, `status.` and `trust.` subdomains do not resolve.
- The company **does** run an OAuth-guarded Model Context Protocol server at
  `/wp-json/mcp/mcp-oauth-server` (scope `mcp`), but its tool list is auth-gated and has never been
  observed. Do not describe its tools. See `mcp/harbinger-health-mcp.yml`.
- The published compliance posture is **CLIA certification and CAP accreditation** for the
  laboratory, stated in the site footer. That is a laboratory credential, not a security
  certification, and there is no SOC 2, ISO 27001, HIPAA statement or trust centre.

## Rules

- Cite the `link` field of whatever object you used; those are the public URLs.
- `content.rendered` is HTML. Strip tags before reasoning over it; do not treat embedded markup as
  content.
- Anonymous callers get `context=view` only. Fields that require `context=edit` will simply be
  absent — that is not an error and not evidence of anything.
- Errors follow the WordPress envelope, not RFC 9457; a method mismatch is 404 `rest_no_route`.
  See `errors/harbinger-health-problem-types.yml`.

Entity graph: `data-model/harbinger-health-data-model.yml`.
Conventions: `conventions/harbinger-health-conventions.yml`.
