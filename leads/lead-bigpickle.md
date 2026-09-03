## 2026-09-03 16:43:31 UTC [target] (model bigpickle)
[HYP] Mirror-domains share wallet/auth/API trust boundary -> cross-domain ATO via client-api
class: AUTH
asset: gamdom80006.com /client-api (and gamdom.eu, gamdom.io, gamdom.vip, gamdom.win)
confidence: 52
reasoning: All 7 enumerated mirror hosts serve byte-identical casino app, identical CSP (frame-ancestors 'self' https://*.faucetcrypto.com), nginx/Varnish, and identical POST-only `/client-api` proxy (400 "only POST" on every path incl /v1 /v2 /health /graphql). UptimeRobot status page lists them as one "Gamdom Official Domains" account group. Live probes confirm corporate control; sharing implies a single upstream API/identity origin.
evidence_needed: Two mirrors resolving to distinct upstream; or a POST to one mirror /client-api returning a session/token usable on another mirror; or browser-context CSRF (since frame-ancestors allows faucetcrypto subdomains) reaching /client-api cross-origin.
verify_steps: Read-only first — GET /client-api and / on all mirrors (done, all 200/400). Requires AUTH_HELPED/HUMAN: authenticate on one mirror, confirm the auth cookie/session is honored by the others' /client-api. Passive only: DNS A/AAAA resolution comparison of mirrors vs main (canonical CDN).
impact: Cross-domain account/session takeover surface if identity is shared; attacker-controlled persistence for phishing via lookalike mirrors (medium-high).
testability: HUMAN_ONLY
[HYP] UptimeRobot status-page product on gamdommirrors.com has write/incident or admin interface reachable without auth
class: MISCONFIG
asset: gamdommirrors.com /api/status-page/gamdom-domains (and /api/badge/, /api/status-page/heartbeat/)
confidence: 40
reasoning: gamdommirrors.com is a dedicated status subproduct NOT sharing the casino /client-api proxy (root returns SPA, /client-api returns SPA page not the casino 400). It exposes UptimeRobot statuspage REST: /api/status-page/<slug> returns full monitor config JSON; heartbeat endpoints return empty. This is a distinct, thinner attack surface with its own admin/threshold API that may lack auth if exposed.
evidence_needed: Presence of authless write/mutation endpoints (e.g. POST /api/status-page/.../incidents, POST /api/monitor/, /api/push/) that accept incident text or monitor toggles; or an admin panel hidden path returning 200 with controls.
verify_steps: Passive GET enumeration of /api/status-page/*, /api/badge/*, /api/doc/* (read-only) to map the public keyspace; confirm no tokens/IDs exposed (heartbeat empty on 13,2). No POST yet (would be mutating third-party UptimeRobot SaaS — REJECTED class risk).
impact: Would deface the official "Gamdom Official Domains" trust page (brand/SEO/trust impact; users routed to lookalike mirrors); low-med. Mostly inventory/trust value.
testability: HUMAN_ONLY
[HYP] /client-api proxy reflects caller with insufficient input validation -> potential injection/SSRF on upstream (money-flow functions)
class: OTHER
asset: gamdom.com /client-api
confidence: 45
reasoning: /client-api is a catch-all POST proxy (400 "only POST" for all paths incl /v1, /v2, /health, /graphql, /openapi.json). Its request body format is not statically recoverable from the minified bundle; bundle shows an api factory (o.kT with base "/client-api") that calls methods by action name. If the proxy forwards an attacker-controlled subpath/payload to an upstream identity/wallet service, classic SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST to /client-api with a crafted body returning an upstream internal error/stack/truthy status that differs by injected subpath; or the bundle resolving the action->endpoint table.
verify_steps: Passive: enumerate client-api behavior via status-code differential on GET/OPTIONS variants (done — all 400). No live POST until authorized; the necessary proof is a request that must include a valid session token to reach wallet/auth — REJECTED as no-auth-bypass-against-live-data. Await AUTHORIZED tester.
impact: If a wallet/deposit_withdrawal/auth endpoint is reachable via /client-api without correct authorization checks -> fund theft or ATO (critical) — but currently unproven.
testability: HUMAN_ONLY
[NEXT] PROBE: GET (read-only) `https://gamdommirrors.com/api/status-page/gamdom-domains/incidents` to pull the historical incident object (UptimeRobot status page exposes past incidents; if any reference internal monitoring URLs/tokens/script tags they may leak upstream endpoints not otherwise published).
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POSt to live identity/wallet proxy is prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing.
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public UptimeRobot status page of an in-scope org service is legitimate passive recon that resolves true operating domains (bypasses wildcard/CDN ambiguity).
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com is not operated by Gamdom; only reportable as brand-jacking/phishing.
[RISK] gamdom: 58 reason: Expanded confirmed operating surface to 7 mirror domains sharing a single POST-only `/client-api` identity/wallet proxy; reliable high-value attack surface but no confirmed HIGH-class vuln this pass — best leads need authenticated, authorized POST testing against a scoped identity endpoint.
