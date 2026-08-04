---
name: Track Harbinger Health company news and scientific updates
description: >-
  Pull Harbinger Health's press releases, funding announcements and conference/scientific updates
  programmatically from the company's public WordPress REST API, paging correctly and filtering by
  date window or category.
api: openapi/harbinger-health-wordpress-wp-v2-openapi.yml
operations:
  - listPosts
  - getPostsById
  - listCategories
  - listTags
  - listEvents
generated: '2026-08-04'
method: generated
---

# Track Harbinger Health company news

Harbinger Health has no developer programme and no documentation. It does serve a live, anonymously
readable WordPress REST API from its corporate site, and that is the correct way to follow its
announcements without scraping HTML.

**Scope warning.** This is a marketing-site content API. It carries no clinical, laboratory,
diagnostic, genomic or patient data. Nothing about the Harbinger HX platform or the RESOLVE test is
reachable through any operation below. Do not represent anything retrieved here as clinical data.

## Authentication

None. Reads in `view` context require no credential. Do not send an `Authorization` header.

Base URL: `https://harbinger-health.com/wp-json`

## Steps

1. **Discover the categories** — call `listCategories` (`GET /wp/v2/categories?per_page=100`) once
   and keep the `id` → `slug` map. Category slugs are how the company separates press releases from
   scientific updates.

2. **List recent posts** — call `listPosts`
   (`GET /wp/v2/posts?per_page=20&orderby=date&order=desc`). Use `_fields` to keep the payload small:
   `_fields=id,date,modified,slug,link,title,excerpt,categories,tags`.

3. **Page correctly.** Read `X-WP-Total` and `X-WP-TotalPages` from the response headers, or follow
   the RFC 8288 `Link` header's `rel="next"`. Do not guess page counts. `per_page` is capped at 100 —
   asking for more returns HTTP 400 `rest_invalid_param` with
   `data.details.per_page.code = rest_out_of_bounds`.

4. **Incremental sync.** On subsequent runs pass `modified_after=<ISO 8601 of last sync>` rather than
   re-walking the collection. `after` and `before` filter on publication date; `modified_after` and
   `modified_before` filter on last edit.

5. **Fetch one article in full** — call `getPostsById` (`GET /wp/v2/posts/{id}`) when you need
   `content.rendered`. Add `_embed` to expand `_links` into `_embedded` (author, featured media,
   terms) in one round trip instead of three.

6. **Pick up conference presence** — call `listEvents` (`GET /wp/v2/events`). `events` is the one
   custom post type this site registers, and it backs the conference pages (for example the ASCO
   2026 Annual Meeting entry). Treat it as a separate collection from `posts`.

## Rules

- **Retries.** No idempotency contract exists. These are all `GET`s, so retrying is safe by method
  semantics — but never blind-retry any write against this host.
- **Errors.** The envelope is `{"code","message","data":{"status"}}`, not RFC 9457. A wrong path or
  a method mismatch returns **404 `rest_no_route`**, not 405 — so do not infer "resource missing"
  from a 404 without checking the `code`. `404 rest_post_invalid_id` means the id itself is bad.
  Full catalogue: `errors/harbinger-health-problem-types.yml`.
- **Rate limits.** None advertised; no `RateLimit-*` or `Retry-After` header comes back. `robots.txt`
  asks for a 10 second crawl delay — honour it as the ceiling for bulk traversal.
- **Caching.** Collection reads return `Cache-Control: max-age=600, must-revalidate`. Do not poll
  more often than every 10 minutes.
- **No tracing.** No request-id header is returned, so a failure cannot be correlated with the
  provider. Log the full request URL yourself.

Conventions reference: `conventions/harbinger-health-conventions.yml`.
Live response shapes: `examples/_index.yml`.
