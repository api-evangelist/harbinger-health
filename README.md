# Harbinger Health

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
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Harbinger Health is a Cambridge, Massachusetts biotechnology company founded out of Flagship
Pioneering's Flagship Labs in 2018, building blood-based early cancer detection on the Harbinger HX
platform — cell-free DNA methylation assay chemistry tuned for low tumour fraction, paired with
machine learning operating under biologically informed constraints — and its clinical application,
RESOLVE. Testing runs in a CLIA-certified, CAP-accredited high-complexity laboratory.

- Website: https://harbinger-health.com/
- RESOLVE: https://harbinger-health.com/resolve/
- Platform technology: https://harbinger-health.com/platform-technology/
- News and insights: https://harbinger-health.com/news-insights/

## API posture (profiled 2026-08-04)

**There is no developer API.** No developer portal, no documentation, no OpenAPI published by the
company, no SDKs, no CLI, no pricing, no sign-up, no status page, no changelog, no bug-bounty or
security.txt. The `developer.`, `docs.`, `api.`, `portal.`, `status.` and `trust.` subdomains do not
resolve. No FHIR, HL7 or GA4GH surface exists — nothing about specimens, assays, results, orders or
patients is publicly reachable, and nothing in this repository describes RESOLVE or Harbinger HX
data.

What the company *does* serve from its own host is unusual for a company with no developer
programme, and is what this profile captures:

| Surface | Status |
|---|---|
| `GET /wp-json/` — WordPress route discovery, 268 routes / 15 namespaces | 200, anonymous |
| `wp/v2` content API — posts, pages, an `events` custom post type, media, search | 200, anonymous |
| `/wp-json/mcp/mcp-oauth-server` — Model Context Protocol server | 401 `mcp_unauthorized` |
| `/wp-json/mcp/mcp-adapter-default-server` — second MCP server | 401 `rest_forbidden` |
| `/wp-json/wp-abilities/v1/abilities` — WordPress Abilities API (probable tool source) | 401 |
| `/.well-known/oauth-authorization-server` — RFC 8414, OAuth 2.1 + PKCE S256, scope `mcp` | 200 |
| `/.well-known/oauth-protected-resource` — RFC 9728, names the MCP server | 200 |
| `/.well-known/security.txt`, `/openid-configuration`, `/api-catalog`, `/agent-card.json`, `/agent.json`, `/llms.txt`, `/openapi.json`, `/graphql` | 404 |

The MCP tool catalogue is **auth-gated and has not been observed**, so no tool names, descriptions or
input schemas are recorded anywhere in this repository. The OpenAPI in `openapi/` was **derived by
API Evangelist** from the live WordPress route-discovery document — it is not a Harbinger Health
artifact, and it describes a corporate-website content API.
