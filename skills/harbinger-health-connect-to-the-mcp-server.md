---
name: Connect to the Harbinger Health MCP server
description: >-
  Complete the OAuth 2.1 flow that guards Harbinger Health's Model Context Protocol server and
  enumerate its real tool catalogue, using only endpoints the provider publishes in its RFC 8414
  and RFC 9728 metadata documents.
api: mcp/harbinger-health-mcp.yml
operations: []
generated: '2026-08-04'
method: generated
---

# Connect to the Harbinger Health MCP server

Harbinger Health publishes a real MCP server on its own origin. Unusually for a company with no
developer programme, it is fronted by a genuine OAuth 2.1 deployment with RFC 8414 and RFC 9728
discovery documents. This skill is how to reach it legitimately.

**This skill enumerates nothing on your behalf.** The tool catalogue is auth-gated and has never
been observed by API Evangelist, so no tool names, descriptions or input schemas appear in this
repository. Discover them yourself with the steps below; do not assume any.

## Endpoints (all from the provider's own metadata)

| Purpose | URL |
|---|---|
| Protected resource metadata | `https://harbinger-health.com/.well-known/oauth-protected-resource` |
| Authorization server metadata | `https://harbinger-health.com/.well-known/oauth-authorization-server` |
| Authorize | `https://harbinger-health.com/oauth/authorize` |
| Token | `https://harbinger-health.com/oauth/token` |
| Revoke | `https://harbinger-health.com/oauth/revoke` |
| MCP server (OAuth) | `https://harbinger-health.com/wp-json/mcp/mcp-oauth-server` |
| MCP server (adapter default) | `https://harbinger-health.com/wp-json/mcp/mcp-adapter-default-server` |

## Steps

1. **Discover, do not hardcode.** `GET /.well-known/oauth-protected-resource` on the MCP host. It
   returns `resource`, `authorization_servers` and `scopes_supported`. Follow
   `authorization_servers[0]` to `/.well-known/oauth-authorization-server` and read the endpoints
   from there. Both are anonymous and return 200 `application/json`.

2. **Register by client-ID metadata document.** The server advertises
   `client_id_metadata_document_supported: true` and no dynamic-registration endpoint. Host your
   client metadata document at an HTTPS URL you control and use that URL as your `client_id`.

3. **Authorize with PKCE.** `code_challenge_methods_supported` is `["S256"]` and
   `token_endpoint_auth_methods_supported` is `["none"]` — this is a public client, so PKCE is
   mandatory, not optional. Request `scope=mcp`; it is the only scope the server advertises.
   `response_types_supported` is `["code"]`.

4. **Exchange and refresh.** `grant_types_supported` is `["authorization_code", "refresh_token"]`.
   Store the refresh token; there is no published token lifetime, so refresh on 401 rather than on a
   schedule you invented.

5. **Call the server.** POST JSON-RPC 2.0 to the MCP endpoint with
   `Authorization: Bearer <token>` (`bearer_methods_supported` is `["header"]` — do not put the token
   in a query parameter) and `Accept: application/json, text/event-stream`.

   Start with `initialize`, then `tools/list`. Without a token, `tools/list` returns
   HTTP 401 `{"code":"mcp_unauthorized","message":"MCP authentication required."}`.

6. **Cross-check the tool source.** The tools are almost certainly assembled from the WordPress
   Abilities API registered on the same host. With the same credential, `GET
   /wp-json/wp-abilities/v1/abilities` and `/wp-abilities/v1/categories` (both 401 anonymously) should
   line up with what `tools/list` returned. Record any divergence into
   `mcp/harbinger-health-tool-crosswalk.yml`.

## Rules and cautions

- **The single `mcp` scope is coarse.** There is no read/write split and no per-resource scoping. The
  Abilities `run` route accepts GET, POST, PUT, PATCH and DELETE, so a token that can list tools may
  also be able to invoke write-capable abilities. Treat every tool as potentially mutating until you
  have read its schema, and keep a human in the loop on first use.
- **Blast radius is the WordPress site**, not any clinical system. Nothing about Harbinger HX or
  RESOLVE is reachable. But the corporate website is still production — do not exercise write tools
  against it.
- **No idempotency.** There is no `Idempotency-Key` contract anywhere on this host. Never blind-retry
  a tool call that may have mutated state.
- **No OIDC.** `/.well-known/openid-configuration` is 404. There is no ID token and no userinfo
  endpoint — this is bare OAuth, so do not attempt to derive an identity from the token.
- **No documentation.** Harbinger Health publishes nothing about this server. If you need terms for
  automated access, `info@harbinger-health.com` is the only route, and
  `https://harbinger-health.com/terms-of-use/` is the only governing document.

Scope detail: `scopes/harbinger-health-scopes.yml`.
Auth profile: `authentication/harbinger-health-authentication.yml`.
Server manifest: `mcp/harbinger-health-mcp.yml`.
