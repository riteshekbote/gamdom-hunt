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
## 2026-09-03 19:31:18 UTC [target] (model bigpickle)
[NEW] gamdom80007.com (7th mirror): same Fastly origin, byte-identical app + POST-only /client-api (verified 200 root / 400 GET /client-api / 400 POST /client-api).
[NEW] gamdom4567.com identified as the real CNAME origin behind both gamdom8000x mirrors (root 404, /client-api 400) — exposes the true upstream behind the clone aliases.
[CHANGED] gamdommirrors.com is Uptime **Kuma** (self-hosted, behind Fastly/Varnish), NOT UptimeRobot; status page slug `gamdom-domains` publishes 7 monitors + 24h heartbeats; incidents REST route in Kuma is handled via SPA fallback (embedded in `/api/status-page/<slug>` which returned `incident:null`).
[PRIO] gamdom4567.com,6.1,origin-of-mirror-clone-set (tech_exposure: reveals backend behind aliases; business: root of 8000x phishing-persistent surfaces)
[PRIO] gamdom80007.com,6.0,fresh-7th-casino/mirror-asset (same /client-api surface as 80006)
[PRIO] gamdommirrors.com,4.5,misconfig-status-admin (thin Kuma surface; mutating = 3rd-party/REJECTED)
[HYP] Uptime Kuma instance on gamdommirrors.com exposes unauthenticated socket.io admin/mutation events
class: MISCONFIG
asset: gamdommirrors.com /socket.io (events login, setup, saveStatusPage, addMonitor, deleteMonitor)
confidence: 38
reasoning: Full Kuma frontend served; socket.io EIO=4 issues SID but Fastly load-balancing breaks session persistence so admin events not yet reachable; status page is published (public) and instances frequently leave `getMonitorList`/`saveStatusPage` reachable before auth. No login/setup gating observed in passive reads.
evidence_needed: A persistent socket session returning non-null for `getMonitorList`/`needSetup`/`login` without valid admin token.
verify_steps: Re-run socket.io handshake and POST `42["login","",""]` then poll on a single consistent edge (pin Host/SNI), not cross-pool; or websocket upgrade with EIO=4 — read-only authentication-status checks only. No mutation of third-party Kuma data.
impact: Deface the official "Gamdom Official Domains" trust page / pivot to monitor configs; low-med.
testability: HUMAN_ONLY
[HYP] gamdom4567.com is a shadow origin: identical /client-api to gamdom8000x and main mirrors, revealing a shared upstream identity/wallet origin masking per-region clones
class: OTHER
asset: gamdom4567.com /client-api (origin behind gamdom80006/80007)
confidence: 50
reasoning: gamdom80006 and gamdom80007 both CNAME -> gamdom4567.com -> Fastly (151.101.x.72); gamdom4567 root returns 404 while 8000x return 200 casino app, i.e. 4567 is the shared reverse-proxy/backend for the clone set, not a user-facing host. Identical POST-only /client-api (400) implies shared upstream wallet/identity boundary.
evidence_needed: Confirming gamdom4567 handles upstream origin requests for both clones (via Host header on 4567 returning app or an X-Forwarded bypass differential); or showing a session/token issued on one mirror is honored on 4567 and vice-versa.
verify_steps: Passive — DNS comparisons (done: both CNAME to 4567); compare response headers (etag/Vary) between gamdom80006 and gamdom80007 on / to detect identical static/CDN cache keys -> same origin. No auth-bypass testing.
impact: Establishes single trust boundary across all 7 mirrors (and the real origin), making cross-domain session/CSRF from any mirror valid for all; medium-high surface characterisation.
testability: HUMAN_ONLY
[PARKED] Uptime Kuma unauthenticated admin (conf 38): socket.io admin events not reachable through Fastly pool; no mutation allowed on 3rd-party SaaS; below 40 threshold — parked pending a persistent-session HUMAN test.
[PARKED] gamdom4567.cd shadow-origin SSRF/mass-assignment (conf 50, no verify): proving shared-boundary requires authenticated POST, which is REJECTED class.
[FINAL] ranked survivors:
[NEXT] PROBE: GET (read-only) `https://gamdom4567.com/client-api` with `Host: gamdom80007.com` via a single Fastly edge and compare response headers/body to `https://gamdom80007.com/client-api` to confirm gamdom4567 is the exact origin (shared trust boundary, no mutation, no auth tokens).
[LEARN] ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Domains" status page) and gamdom4567.com as its CNAME origin root.
[LEARN] ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamdom domains — legitimate passive recon resolving true operating mirrors.
[RISK] gamdom: 58 reason: Confirmed 7th mirror (gamdom80007.com) and real origin gamdom4567.com behind the clone set, all sharing one Fastly edge and POST-only /client-api identity/wallet proxy; strong cross-domain trust-boundary surface but no confirmed HIGH-class vuln — top leads (cross-mirror ATO, origin boundary) need authenticated, authorized POST/session testing against scoped identity endpoints.
## 2026-09-03 21:53:01 UTC [target] (model bigpickle)
[PRIO] gamdom4567.com,6.3,origin-of-clone-set (attack:7 business:6 tech:6 gate:7 cloud:6 fresh:6)
[PRIO] gamdom80007.com,6.0,fresh-7th-casino-mirror (attack:7 business:5 tech:6 gate:8 cloud:5 fresh:8)
[PRIO] gamdommirrors.com,4.8,misconfig-status-admin (attack:4 business:5 tech:5 gate:5 cloud:4 fresh:7)
[HYP] gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors
class: OTHER
asset: gamdom4567.com /client-api
confidence: 50
reasoning: gamdom80006 and gamdom80007 both CNAME to gamdom4567.com which resolves to Fastly edge (151.101.x.72); gamdom4567 root returns 404 while clones return 200; identical POST-only /client-api behavior implies shared upstream wallet/identity origin
evidence_needed: Host-header on gamdom4567 returning same app as clones; or identical etag/Vary headers between 4567 and 8000x on /client-api
verify_steps: GET https://gamdom4567.com/client-api with Host: gamdom80007.com and compare headers/body to https://gamdom80007.com/client-api (read-only, single Fastly edge)
impact: Establishes single trust boundary across all mirrors, making any session/CSRF from any mirror valid for all; medium-high
testability: HUMAN_ONLY
[HYP] gamdom80007.com shares exact origin with gamdom80006 enabling cross-mirror session replay
class: AUTH
asset: gamdom80007.com /client-api
confidence: 45
reasoning: Byte-identical app, same CSP, same Fastly edge, same POST-only proxy; session cookies from one mirror may be honored by the other
evidence_needed: Same session cookie accepted across both mirrors
verify_steps: Authenticate on gamdom80006, attempt same cookie on gamdom80007 /client-api (HUMAN only)
impact: Cross-domain account takeover if session cookies not scoped; medium-high
testability: HUMAN_ONLY
[HYP] gamdommirrors.com Kuma status page leaks internal monitor config
class: MISCONFIG
asset: gamdommirrors.com /api/status-page/gamdom-domains
confidence: 38
reasoning: Full Kuma frontend served; socket.io EIO=4 issues SID but Fastly breaks session persistence; no login observed in passive reads
evidence_needed: Non-null response from getMonitorList or needSetup without valid admin token
verify_steps: POST login event via socket.io with empty token on pinned edge (read-only auth check)
impact: Deface official "Gamdom Official Domains" trust page; low-med
testability: HUMAN_ONLY
[PARKED] gamdommirrors.com Kuma admin: conf 38 below 40 threshold, Fastly pool breaks session persistence
[FINAL] survivors:
[NEXT] PROBE: GET (read-only) `https://gamdom4567.com/client-api` with `Host: gamdom80007.com` and compare response headers/body to `https://gamdom80007.com/client-api` to confirm gamdom4567 is the exact origin (shared trust boundary, no mutation, no auth tokens).
[LEARN] ACCEPTED inventory @ gamdom80007.com: verified 7th mirror (same Fastly origin + POST-only /client-api + listed on status page) and gamdom4567.com as CNAME origin root.
[LEARN] ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page publicly publishes full monitor config + 24h heartbeats for all 7 domains.
[RISK] gamdom: 55 reason: Confirmed 7 mirrors sharing one Fastly edge and POST-only /client-api identity/wallet proxy; established shadow origin gamdom4567.com; strong cross-domain trust-boundary surface but no confirmed HIGH-class vuln — top leads need authenticated POST/session testing against scoped identity endpoints.
## 2026-09-03 23:46:29 UTC [target] (model bigpickle)
[PRIO] gamdom4567.com,6.3,origin-of-clone-set (attack:7 business:6 tech:6 gate:7 cloud:6 fresh:6)
[PRIO] gamdom80007.com,6.0,fresh-7th-casino-mirror (attack:7 business:5 tech:6 gate:8 cloud:5 fresh:8)
[PRIO] gamdommirrors.com,4.8,misconfig-status-admin (attack:4 business:5 tech:5 gate:5 cloud:4 fresh:7)
[HYP] gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors
class: OTHER
asset: gamdom4567.com /client-api
confidence: 50
reasoning: gamdom80006 and gamdom80007 both CNAME to gamdom4567.com which resolves to Fastly edge (151.101.x.72); gamdom4567 root returns 404 while clones return 200; identical POST-only /client-api behavior implies shared upstream wallet/identity origin
evidence_needed: Host-header on gamdom4567 returning same app as clones; or identical etag/Vary headers between 4567 and 8000x on /client-api
verify_steps: GET https://gamdom4567.com/client-api with Host: gamdom80007.com and compare headers/body to https://gamdom80007.com/client-api (read-only, single Fastly edge)
impact: Establishes single trust boundary across all mirrors, making any session/CSRF from any mirror valid for all; medium-high
testability: HUMAN_ONLY
[HYP] gamdom80007.com shares exact origin with gamdom80006 enabling cross-mirror session replay
class: AUTH
asset: gamdom80007.com /client-api
confidence: 45
reasoning: Byte-identical app, same CSP, same Fastly edge, same POST-only proxy; session cookies from one mirror may be honored by the other
evidence_needed: Same session cookie accepted across both mirrors
verify_steps: Authenticate on gamdom80006, attempt same cookie on gamdom80007 /client-api (HUMAN only)
impact: Cross-domain account takeover if session cookies not scoped; medium-high
testability: HUMAN_ONLY
[HYP] gamdommirrors.com Kuma status page leaks internal monitor config
class: MISCONFIG
asset: gamdommirrors.com /api/status-page/gamdom-domains
confidence: 38
reasoning: Full Kuma frontend served; socket.io EIO=4 issues SID but Fastly breaks session persistence; no login observed in passive reads
evidence_needed: Non-null response from getMonitorList or needSetup without valid admin token
verify_steps: POST login event via socket.io with empty token on pinned edge (read-only auth check)
impact: Deface official "Gamdom Official Domains" trust page; low-med
testability: HUMAN_ONLY
[PARKED] gamdommirrors.com Kuma admin: conf 38 below 40 threshold, Fastly pool breaks session persistence
[PARKED] gamdom80007.com session replay: requires authenticated session first (AUTH_HELPED), cannot verify passively
[FINAL] survivors:
[NEXT] PROBE: GET (read-only) `https://gamdom4567.com/client-api` with `Host: gamdom80007.com` and compare response headers/body to `https://gamdom80007.com/client-api` to confirm gamdom4567 is the exact origin (shared trust boundary, no mutation, no auth tokens).
## 2026-09-04 02:51:09 UTC [target] (model bigpickle)
[PRIO] gamdom4567.com,6.3,origin-of-clone-set (attack:7 business:6 tech:6 gate:7 cloud:6 fresh:6)
[PRIO] gamdom80007.com,6.0,fresh-7th-casino-mirror (attack:7 business:5 tech:6 gate:8 cloud:5 fresh:8)
[PRIO] gamdommirrors.com,4.8,misconfig-status-admin (attack:4 business:5 tech:5 gate:4 cloud:4 fresh:7)
[HYP] gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors
class: OTHER
asset: gamdom4567.com /client-api
confidence: 50
reasoning: gamdom80006 and gamdom80007 both CNAME to gamdom4567.com (Fastly 151.101.x.72); gamdom4567 root 404 while clones 200; identical POST-only /client-api behavior implies shared upstream identity/wallet origin
evidence_needed: Host-header on gamdom4567 returning same app as clones; or identical etag/Vary headers between 4567 and 8000x on /client-api
verify_steps: GET https://gamdom4567.com/client-api with Host: gamdom80007.com and compare headers/body to https://gamdom80007.com/client-api (read-only, single Fastly edge)
impact: Establishes single trust boundary across all mirrors, making any session/CSRF from any mirror valid for all; medium-high
testability: PASSIVE
[HYP] gamdom80007.com shares exact origin with gamdom80006 enabling cross-mirror session replay
class: AUTH
asset: gamdom80007.com /client-api
confidence: 45
reasoning: Byte-identical app, same CSP, same Fastly edge, same POST-only proxy; session cookies from one mirror may be honored by the other
evidence_needed: Same session cookie accepted across both mirrors
verify_steps: Authenticate on gamdom80006, attempt same cookie on gamdom80007 /client-api (HUMAN only)
impact: Cross-domain account takeover if session cookies not scoped; medium-high
testability: HUMAN_ONLY
[HYP] gamdommirrors.com Kuma status page leaks internal monitor config
class: MISCONFIG
asset: gamdommirrors.com /api/status-page/gamdom-domains
confidence: 38
reasoning: Full Kuma frontend served; socket.io EIO=4 issues SID but Fastly breaks session persistence; no login observed in passive reads
evidence_needed: Non-null response from getMonitorList or needSetup without valid admin token
verify_steps: POST login event via socket.io with empty token on pinned edge (read-only auth check)
impact: Deface official "Gamdom Official Domains" trust page; low-med
testability: HUMAN_ONLY
[PARKED] gamdommirrors.com Kuma admin: conf 38 below 40 threshold, Fastly pool breaks session persistence, verify requires mutation
[PARKED] gamdom80007.com session replay: requires authenticated session first (AUTH_HELPED), cannot verify passively
[FINAL] ranked survivors:
[NEXT] PROBE: GET (read-only) `https://gamdom4567.com/client-api` with `Host: gamdom80007.com` and compare response headers/body to `https://gamdom80007.com/client-api` to confirm gamdom4567 is the exact origin (shared trust boundary, no mutation, no auth tokens). This is the same probe from last run that has not yet been executed — it remains the only actionable PASSIVE probe in the survivor queue.
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80007.com: verified 7th mirror (same Fastly origin + POST-only /client-api + listed on status page) and gamdom4567.com as CNAME origin root
[LEARN] ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page publicly publishes full monitor config + 24h heartbeats for all 7 domains
[RISK] gamdom: 55 reason: Confirmed 7 mirrors sharing one Fastly edge and POST-only /client-api identity/wallet proxy; established shadow origin gamdom4567.com; strong cross-domain trust-boundary surface but no confirmed HIGH-class vuln — top leads need authenticated POST/session testing against scoped identity endpoints.
## 2026-09-04 07:44:28 UTC [target] (model bigpickle)
[PRIO] gamdom4567.com,6.5,origin-of-clone-set (attack:7 business:6 tech:6 gate:7 cloud:6 fresh:7)
[PRIO] gamdom80007.com,6.0,fresh-7th-casino-mirror (attack:7 business:5 tech:6 gate:8 cloud:5 fresh:8)
[PRIO] gamdommirrors.com,4.8,misconfig-status-admin (attack:4 business:5 tech:5 gate:4 cloud:4 fresh:7)
[HYP] gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors
class: OTHER
asset: gamdom4567.com /client-api
confidence: 70
reasoning: All three domains (gamdom4567.com, gamdom80007.com, gamdom80006.com) return byte-identical response headers (server:nginx, via:1.1 varnish, strict-transport-security, content-length:26) and identical body "Invalid request, only POST" on /client-api; Fastly returned 421 on Host-header injection (gamdom4567 cert lacks 8000x SANs), proving edge enforces cert binding; origin backend fingerprint shared across mirrors
evidence_needed: None — origin trust boundary confirmed via passive header comparison
verify_steps: DONE — headers compared across all three domains, same backend confirmed
impact: Single trust boundary across all mirrors means any session/CSRF token minted on one mirror is honored by all; medium-high
testability: PASSIVE
[HYP] gamdom80007.com shares exact origin with gamdom80006 enabling cross-mirror session replay
class: AUTH
asset: gamdom80007.com /client-api
confidence: 50
reasoning: Byte-identical app, complete header fingerprint match on /client-api between 80007, 80006, and 4567; shared Fastly edge pool (151.101.x.72) and shared origin backend; session cookies may be honored across mirrors
evidence_needed: Same session cookie accepted across both mirrors
verify_steps: Authenticate on gamdom80006, replay same cookie on gamdom80007 /client-api (HUMAN only)
impact: Cross-domain account takeover if session cookies not scoped; medium-high
testability: HUMAN_ONLY
[HYP] gamdommirrors.com Kuma status page leaks internal monitor config
class: MISCONFIG
asset: gamdommirrors.com /api/status-page/gamdom-domains
confidence: 38
reasoning: Full Kuma frontend served; socket.io EIO=4 issues SID but Fastly breaks session persistence; no login observed in passive reads
evidence_needed: Non-null response from getMonitorList or needSetup without valid admin token
verify_steps: POST login event via socket.io with empty token on pinned edge (read-only auth check)
impact: Deface official "Gamdom Official Domains" trust page; low-med
testability: HUMAN_ONLY
[PARKED] gamdommirrors.com Kuma admin: conf 38 below 40 threshold, Fastly pool breaks session persistence, verify requires mutation
[PARKED] gamdom80007.com session replay: requires authenticated session first (AUTH_HELPED), cannot verify passively
[FINAL] ranked survivors:
[NEXT] PROBE: GET (read-only) `https://gamdommirrors.com/socket.io/?EIO=4&transport=polling` — capture socket.io handshake response and enumerate namespaces/events; test whether Kuma endpoint leaks monitor data without auth.
[LEARN] ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com is the origin backend serving /client-api for all 8000x mirrors
[RISK] gamdom: 58 reason: Confirmed 7 mirrors sharing one Fastly edge and POST-only /client-api identity/wallet proxy; origin trust boundary now verified via header fingerprinting; strong cross-domain trust-boundary surface but no confirmed HIGH-class vuln — top leads need authenticated POST/session testing against scoped identity endpoints.
## 2026-09-04 12:45:11 UTC [target] (model bigpickle)
## 2026-09-04 16:35:32 UTC [target] (model bigpickle)
[PRIO] gamdom4567.com,6.5,origin-of-clone-set (attack:7 business:6 tech:6 gate:7 cloud:6 fresh:7)
[PRIO] gamdom80006.com,6.0,auth-session-replay (attack:7 business:6 tech:5 gate:5 cloud:5 fresh:7)
[PRIO] gamdommirrors.com,4.8,status-admin-leak (attack:4 business:5 tech:5 gate:4 cloud:4 fresh:7)
[HYP] gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors
class: OTHER
asset: gamdom4567.com /client-api
confidence: 70
reasoning: Byte-identical response headers across gamdom4567/gamdom80007/gamdom80006 on /client-api; Fastly 421 on Host-header injection confirms cert binding at edge; origin backend fingerprint identical; hypothesis evidence_needed is "None" per prior ACCEPTED verdict
evidence_needed: None — confirmed via passive header comparison
verify_steps: DONE
impact: Single trust boundary across all mirrors means any session/CSRF token minted on one mirror is honored by all; medium-high
testability: PASSIVE
[HYP] Mirror-domain session sharing via /client-api enables cross-domain ATO
class: AUTH
asset: gamdom80006.com /client-api
confidence: 50
reasoning: Byte-identical app + shared origin backend + same Fastly pool across all mirrors; session cookies may be honored cross-domain
evidence_needed: Same session cookie accepted across both gamdom80006 and gamdom80007
verify_steps: Authenticate on gamdom80006, replay same cookie on gamdom80007 /client-api (HUMAN only)
impact: Cross-domain account takeover if session cookies not scoped; medium-high
testability: HUMAN_ONLY
[HYP] gamdommirrors.com Kuma status page leaks internal monitor config
class: MISCONFIG
asset: gamdommirrors.com /api/status-page/gamdom-domains
confidence: 38
reasoning: Full Kuma frontend served; socket.io EIO=4 issues SID but Fastly breaks session persistence; no unauthenticated JSON API surface confirmed
evidence_needed: Non-null response from getMonitorList or needSetup without valid admin token
verify_steps: GET https://gamdommirrors.com/socket.io/?EIO=4&transport=polling — capture handshake response
impact: Deface official "Gamdom Official Domains" trust page; low-med
testability: HUMAN_ONLY
[PARKED] gamdommirrors.com Kuma admin leak: confidence 38 below 40 threshold; Fastly pool breaks session persistence; verify requires mutation; no confirmed unauthenticated surface
[PARKED] gamdom80006.com session replay: requires authenticated session first (AUTH_HELPED); cannot verify passively
[FINAL] survivors ranked:
[NEXT] HUMAN: Gamdom80006.com and gamdom4567.com hypotheses are either DONE or blocked on authenticated testing. The only remaining passive probe in the survivor queue (socket.io handshake on gamdommirrors.com) is PARKED at confidence 38 with no clear path to confirm passively. Recommendation: shift to authenticated session testing — log in on gamdom80006.com, extract session cookie, attempt same cookie on gamdom80007.com /client-api to validate cross-mirror session sharing hypothesis. This requires human-interacted browser session. No further passive-only probes remain actionable.
[LEARN] ACCEPTED origin-boundary @ gamdom4567.com: Header fingerprinting confirmed gamdom4567.com as shared origin backend for all 8000x mirrors; Fastly cert validation blocks Host-header injection
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: Blind POST to live identity/wallet proxy prohibited; require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: Public Uptime Kuma status page is legitimate passive recon resolving true operating domains
[RISK] gamdom: 58 reason: Confirmed 7 mirrors sharing one Fastly edge and POST-only /client-api identity/wallet proxy; origin trust boundary verified via header fingerprinting; strong cross-domain trust-boundary surface but no confirmed HIGH-class vuln — top leads need authenticated POST/session testing against scoped identity endpoints.
## 2026-09-04 19:07:55 UTC [target] (model bigpickle)
## 2026-09-04 21:36:53 UTC [target] (model bigpickle)
[HYP] Cross-mirror auth token/session replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80006.com /client-api (shared origin gamdom4567.com, pool B: 80004/80006/80007/4567)
confidence: 55
reasoning: Byte-identical SPA + shared origin backend across all mirrors; /client-api GET 400 with no ACAO on both mirrors and origin (no CORS vector); replay feasibility depends on auth-token transport (cookie vs localStorage), unresolved in main bundles
evidence_needed: Same session/token minted on gamdom80006 accepted by gamdom80007 /client-api; and where token is stored client-side (localStorage=trivial replay+XSS→ATO, HttpOnly cookie=host-based replay)
verify_steps: (passive) fetch AppShell chunk manifest, load request-layer chunk, read /client-api request header + storage key; (AUTH_HELPED) authenticate on 80006, replay bearer/cookie on 80007 /client-api
impact: Cross-domain account takeover across all 8 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] Unlisted alias host widens shared-trust session surface
class: MISCONFIG
asset: gamdom80004.com (302 → gamdom80007.com)
confidence: 45
reasoning: 8th hostname, fastly pool B, 302 to gamdom80007; joins the same origin trust boundary; any session scoped to the mirror set is reachable from an extra hostname absent from official status page
evidence_needed: Whether auth Set-Cookie/headers from 80007 are honored when arriving via 80004; identical session pool
verify_steps: HEAD chain 80004→80007 (done: 302, same pool); compare Set-Cookie during auth on 80004 vs 80007 (HUMAN)
impact: Marginal widening of replay surface + cache-key/host-confusion edge; low
testability: HUMAN_ONLY
[HYP] Kuma status page leaks live heartbeat/IP internals of monitor origins
class: MISCONFIG
asset: gamdommirrors.com /api/status-page/gamdom-domains
confidence: 38
reasoning: Status page publishes 7 monitors + 24h heartbeats; /api/status-page/*, /api/badge/*, /api/heartbeat/* serve SPA HTML; socket.io EIO=4 handshake issues SID but Fastly breaks session persistence; no unauthenticated JSON confirmed
evidence_needed: Non-null getMonitorList/needSetup over socket.io without admin token
verify_steps: GET socket.io polling handshake; compare SID persistence across retries
impact: Deface official "Gamdom Official Domains" trust page / leak monitor IPs; low-med
testability: HUMAN_ONLY
## 2026-09-04 23:18:59 UTC [target] (model bigpickle)
[CHANGED] Cross-mirror session replay hypothesis (gamdom80006) stayed at confidence 55; must now be shifted toward client-side token-storage analysis (localStorage vs HttpOnly cookie) to resolve purely passively — transport question unresolved in main bundles.
[CHANGED] gamdom80004.com 302-chain confirmed to gamdom80007.com; joins Pool B trust boundary as an 8th reachable hostname absent from the official status page (new surface).
[CHANGED] socket.io handshake probe on gamdommirrors.com remains the lone passive survivor — queued by both agents, still confidence 38 (PARKED), no unauthenticated JSON surface confirmed.
[NEW] socket.io handshake probe executed (passive): `GET /socket.io/?EIO=4&transport=polling` on gamdommirrors.com → HTTP 200, `{"sid":"...","upgrades":["websocket"],"pingInterval":25000,...}`, behind Fastly/Varnish (`x-cache: MISS`, `via: 1.1 varnish`) — SID issued but no follow-up surface tested (PARKED admin probe unchanged).
[NEW] Token transport RESOLVED passively from gamdom80006.com `client.41b06529227c4b8b6a1d.js` (597 KB, server's own bundle): request layer uses `credentials:"same-origin"`, zero `Authorization`/`Bearer` strings, zero localStorage token keys (`IS_LOGGED_IN`/`fehash` cookies are UI flags only) → auth is **server-set cookie**, not client bearer token.
[CHANGED] Cross-mirror ATO hypothesis narrowed: cookie-host-agnostic-acceptance on the shared backend is the only replay vector (no localStorage replay branch); document.cookie writes are feature-config junk only.
[HYP] Cross-mirror session replay: cookie auth is hostname-agnostic at the shared origin
class: AUTH
asset: gamdom4567.com /client-api (covers gamdom80004/80006/80007)
confidence: 55
reasoning: JS bundle proves server-set cookie transport (`credentials:"same-origin"`, no Authorization/bearer, no localStorage token); all 4 Pool-B hostnames hit one verified origin backend; cookie validation at backend is plausibly keyed solely on the signed cookie, not the Host header
evidence_needed: session cookie minted on gamdom80006.com accepted by gamdom4567.com or gamdom80007.com /client-api; or backend error varies with Host while cookie constant (indicating host-blind validation)
verify_steps: AUTH_HELPED — authenticate on gamdom80006.com, replay exact cookie to gamdom80007.com/client-api and compare response class; passive fallback: diff POST /client-api error verbosity across hosts with identical invalid cookie (HUMAN decision gate)
impact: cross-domain account takeover over wallet/deposit flows across all mirrors; medium-high
testability: AUTH_HELPED
[HYP] Unlisted alias-gamdom80004.com widens the same-origin cookie trust surface to an 8th hidden host
class: MISCONFIG
asset: gamdom80004.com (302 → gamdom80007.com)
confidence: 45
reasoning: 8th reachable hostname in Pool B, redirects to gamdom80007.com, absent from the official "Gamdom Official Domains" status page; shares the validated origin backend, so a host-agnostic cookie is reachable from a host no defender audits
evidence_needed: auth Set-Cookie/validation behavior on 80004 identical to 80007 (shared session pool)
verify_steps: HEAD chain 80004→80007 (done: 302, same pool); AUTH_HELPED cross-check cookie acceptance on 80004
impact: marginal widening of replay surface + unmonitored host as landing point; low
testability: HUMAN_ONLY
[HYP] Kuma public status page leaks monitor target internals beyond already-accepted recon
class: MISCONFIG
asset: gamdommirrors.com /socket.io + /api/status-page/gamdom-domains
confidence: 38
reasoning: handshake issues SID with websocket upgrade; public status page events (statusPageHeartbeatList) are tokenless by design and already accepted as recon; any admin RPC needs POST — prohibited
evidence_needed: non-null getMonitorList/needSetup without admin token
verify_steps: only via POST socket.io RPC — not permitted (no-auth-bypass/mutate-against-live-data)
impact: deface official trust page / leak monitor IPs; low-med
testability: HUMAN_ONLY
[PARKED] gamdommirrors.com Kuma admin leak: confidence 38 < 40 and verification requires POSTing socket.io RPC (prohibited class); no unauth JSON surface; handshake alone adds nothing.
[PARKED] gamdom80004 alias-host: realistic impact marginal; HUMAN_ONLY proof of the shared pool already vector-verified via 302 + same origin.
[PARKED] XSS-in-bundle lead: 20 dangerouslySetInnerHTML / 1 message listener are polyfill/library sinks, no input module chain — no valid XSS hypothesis.
[FINAL] survivors ranked: [55] cross-mirror cookie replay (AUTH_HELPED, vector transport now evidence-grounded) > [45] gamdom80004 hidden host (PARKED, low impact). Neither is conclusive passively.
[NEXT] PROBE: AUTH_HELPED human gate — this session's passive work is exhausted. Exact passive read-only confirmation left: compare `Diffie`... none. Recommend the authenticated cookie-replay test on gamdom80006→gamdom80007 /client-api. For the next autonomous cycle the only clean passive probe remaining is byte-diff of `POST /client-api` error bodies across gamdom4567/80007 with an identical invalid cookie to detect host-blind validation — but that is POST and thus gated. Emit: [NEXT] HUMAN: authenticate on gamdom80006.com, replay session cookie against gamdom80007.com/client-api; confirm cross-host acceptance to validate shared-trust cookie replay.
[LEARN] ACCEPTED recon @ gamdom80006.com/build/client.<hash>.js: passive fetch of the app's own request-layer bundle (597 KB) proved auth transport is server-set same-origin cookie — no bearer/localStorage token — grounding the cross-mirror replay vector in evidence.
[LEARN] ACCEPTED recon @ gamdommirrors.com/socket.io: GET-only EIO=4 polling handshake issues SID with websocket upgrade; no admin surface reachable without POST RPC, consistent with prior PARKED verdict.
[RISK] gamdom: 58 — confirmed 7-8 mirror hostnames on one Fastly pool verified to share a single origin backend for a cookie-auth identity/wallet proxy; the only remaining ATO class is host-agnostic cookie acceptance (needs authenticated session to confirm). No confirmed HIGH; top lead gated on HUMAN authenticated testing.
## 2026-09-05 01:05:48 UTC [target] (model bigpickle)
[HYP] Cross-mirror session replay: server-set same-origin cookie is validated host-blind at the shared 4567 origin
class: AUTH
asset: gamdom4567.com /client-api (covers gamdom80004/80006/80007)
confidence: 55
reasoning: JS bundle (client.41b06529227c4b8b6a1d.js) proves `credentials:"same-origin"`, no Authorization/bearer/localStorage token → transport is server-set HttpOnly cookie; 4 Pool-B hostnames hit one verified byte-identical origin backend; HTTPS-only surface confirmed (port-80 301); leftover vector is backend session-store keyed on cookie value only, Host-agnostic
evidence_needed: session cookie minted on gamdom80006.com accepted by gamdom80007.com or gamdom4567.com /client-api (cross-host response class differs from invalid-cookie baseline)
verify_steps: AUTH_HELPED — authenticate on gamdom80006.com, replay exact cookie header to gamdom80007.com/client-api, compare response; passive fallback (gated-POST) byte-diff error verbosity across hosts with identical invalid cookie
impact: cross-domain ATO over wallet/deposit flows across all mirrors; medium-high
testability: AUTH_HELPED
[HYP] Unlisted alias host gamdom80004.com widens same-origin cookie-trust surface to an unmonitored 8th hostname
class: MISCONFIG
asset: gamdom80004.com (302→gamdom80007.com, Pool B)
confidence: 45
reasoning: 8th reachable hostname sharing verified origin backend, absent from official "Gamdom Official Domains" status page; browser/privacy-tier DNS sees an extra trusted mirror endpoint no defender audits
evidence_needed: Set-Cookie/validation behavior on 80004 identical to 80007 (shared session pool)
verify_steps: HEAD chain 80004→80007 (done, re-confirmed 302); AUTH_HELPED cross-check cookie acceptance on 80004
impact: marginal widening of replay surface + unmonitored landing host; low
testability: HUMAN_ONLY
[HYP] Kuma status page leaks monitor internals beyond already-accepted recon
class: MISCONFIG
asset: gamdommirrors.com /api/status-page/gamdom-domains + /socket.io
confidence: 38
reasoning: handshake issues SID with websocket upgrade; public status-page heartbeats already accepted recon; any admin RPC needs POST
evidence_needed: non-null getMonitorList/needSetup without admin token
verify_steps: only via POST socket.io RPC — not permitted (no-auth-bypass/mutate-against-live-data)
impact: deface official trust page / leak monitor IPs; low-med
testability: HUMAN_ONLY
[NEXT] HUMAN: authenticate on gamdom80006.com, replay exact session cookie header against gamdom80007.com/client-api (and gamdom4567.com/client-api); confirm cross-host acceptance to validate shared-trust cookie replay — then report, since all passive paths for the ATO class are now exhausted.
[LEARN] ACCEPTED recon @ gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle and stones the certificate of the cookie-replay hypothesis to TLS replay.
[RISK] gamdom: 58 — 7-8 mirror hostnames on one Fastly pool share one verified origin backend for a cookie-auth identity/wallet proxy; sole remaining ATO class is host-agnostic cookie acceptance, which requires an authenticated session to confirm (HUMAN gated). No confirmed HIGH; no new positive surface this cycle.
## 2026-09-05 05:51:53 UTC [target] (model bigpickle)
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at one backend behind every Gamdom domain including flagship
class: AUTH
asset: gamdom.com/client-api (covers io/eu/vip/win + 80006/80007/4567)
confidence: 50
reasoning: all 8 probed hosts return byte-identical 400 signature on GET (proven this cycle); JS bundle proves transport is server-set same-origin cookie; CORS absent (proven) closes client-side replay, leaving protocol-level cookie acceptance as the only open question
evidence_needed: session cookie minted on any one host accepted by /client-api on another (response class differs from invalid-cookie 400/401 baseline)
verify_steps: AUTH_HELPED — authenticate on gamdom80006.com, replay exact Cookie header to gamdom.com and gamdom80007.com/client-api, diff response; passive fallback is gated-POST byte-diff of error verbosity with identical invalid cookie
impact: single cookie theft or single mirror compromise grants account access across the entire Gamdom fleet incl. flagship; medium-high (amplification of any XSS/leak anywhere in the fleet)
testability: AUTH_HELPED
[HYP] gamdom80004.com alias widens trust surface
class: MISCONFIG
asset: gamdom80004.com (302→gamdom80007.com)
confidence: 20
reasoning: PROVEN this cycle it is redirect-only for both / and /client-api — cannot set or serve session cookies, only forwards browsers; residual risk only if A-record remapped to a live backend before the official status page is updated
evidence_needed: 80004 serving 400 signature or Set-Cookie directly (currently false)
verify_steps: GET /client-api on 80004 periodically; GET gamdommirrors.com status page for an 8th monitor
impact: none today; latent unmonitored landing host; low
testability: PASSIVE
[HYP] Kuma admin leak via status-page internals
class: MISCONFIG
asset: gamdommirrors.com /socket.io + /api/status-page/gamdom-domains
confidence: 35
reasoning: REST publicGroupList now empty; heartbeats/monitor config only reachable via socket.io RPC which is POST-only
evidence_needed: tokenless getMonitorList/needSetup via socket
verify_steps: only via POST socket.io RPC — not permitted (no-auth-bypass/mutate-against-live-data)
impact: deface official trust page/leak monitor IPs; low-med
testability: HUMAN_ONLY
[PARKED] gamdom80004 hidden-host: confidence 20; disproven as API-serving host this cycle (302-only).
[PARKED] Kuma admin leak: confidence 35<40; verification requires prohibited POST RPC; REST view now empty.
[PARKED] Prior [55] cross-mirror replay as standalone: CORS absence (no ACAO, preflight 400) removes the attacker-page vector; reframed as fleet-wide origin trust pool requiring an existing cookie.
[FINAL] survivors ranked: [50] brand-wide origin trust pool cookie replay (AUTH_HELPED, now spans gamdom.com) — sole survivor; both lower leads park.
[NEXT] HUMAN: authenticate on gamdom80006.com, replay the exact session Cookie header to gamdom.com/client-api AND gamdom80007.com/client-api; log the response vs an invalid-cookie baseline to prove host-blind validation across the fleet — then report; absent a session, all remaining passive probes for the AUTH class are exhausted.
[LEARN] ACCEPTED recon @ gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend signature spans the entire brand, not just mirrors; raises business value of the trust-pool hypothesis.
[LEARN] ACCEPTED recon @ gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory that implied an 8th API-serving mirror.
[LEARN] ACCEPTED recon @ gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credentialed replay is blocked, closing the client-side branch of the replay vector.
[RISK] gamdom: 52 — one verified backend serves POST-only cookie-auth /client-api for all 8 brand hosts incl. flagship gamdom.com, so any single stolen/leaked session cookie is fleet-wide; however no standalone reachable flaw: client-side replay is CORS-blocked, 80004 is redirect-only, and host-blind cookie acceptance remains unverified and HUMAN-gated. No confirmed HIGH.
## 2026-09-05 09:56:15 UTC [target] (model bigpickle)
## 2026-09-05 13:18:35 UTC [target] (model bigpickle)
[NEW] BREADTH-SCAN flagship/subdomain surface (previously under-probed): dashboard.gamdom.com resolves on flagship Fastly Pool A but is 403-locked at edge everywhere (/ /login /api /graphql /health /static → all 403, Varnish Error 54113 origin-restrict) — genuine scoped admin hostname, access-controlled, no bypass.
[NEW] click.gamdom.com → CNAME eu-proxy-1.symplifymail.com → eu-iv-1.symplifymail.com (192.165.55.11, third-party SymplifyMail email provider) serving stock nginx default page (200, X-Robots noindex, Last-Modified 2025-04) — in-scope subdomain pointed at external provider; currently serves content so NOT dangling, but subdomain-takeover WATCHLIST.
[NEW] help.gamdom.com → Intercom-hosted help center (x-intercom-version, /en/ 302, Intercom CSP) — standard third-party helpdesk SaaS, benign.
[PRIO] dashboard.gamdom.com,5.3,flagship-admin-host (attack:4 business:6 tech:6 gate:3 cloud:6 fresh:9)
[PRIO] click.gamdom.com,4.9,3rd-party-subdomain-takeover (attack:5 business:5 tech:4 gate:4 cloud:6 fresh:8)
[HYP] click.gamdom.com third-party subdomain is a takeover/dangling-CNAME candidate
class: OTHER
asset: click.gamdom.com (CNAME eu-proxy-1.symplifymail.com)
confidence: 25
reasoning: in-scope brand subdomain CNAMEs to external SymplifyMail email provider IP 192.165.55.11; provider currently serves stock nginx default page (200) so reference is NOT dangling today; takeover only if provider hostname/account lapses
evidence_needed: provider hostname returning NXDOMAIN/404 ownerless content, or CNAME repoint to attacker-controlled; confirm whether symplifymail host is brand-owned vs shared
verify_steps: periodic PASV: GET click.gamdom.com/ + re-resolve CNAME chain; check symplifymail host ownership/DNS
impact: full control of *.gamdom.com subdomain if the third-party reference lapses (phishing, OAuth credential harvesting under trusted host); medium if it transitions
testability: PASSIVE
[HYP] dashboard.gamdom.com edge 403 is a misconfigured admin host exposing denied-but-routable admin DNS
class: MISCONFIG
asset: dashboard.gamdom.com
confidence: 30
reasoning: genuine scoped hostname on flagship Fastly pool (151.101.x.52) but origin/edge denies all paths with Varnish Error 54113; no bypass found via common paths; indicates an admin/dashboard vhost exists upstream but is ACL'd
evidence_needed: any path/verb/Host returning non-403 (bypass) on the dashboard vhost; or backend internal endpoints
verify_steps: PASSIVE/HEAD only — enumerate paths + alternate Host header to edge, look for non-403; no mutation
impact: if ACL bypass found → admin portal/unauthorized access; currently locked, low
testability: PASSIVE
[PARKED] click.gamdom.com takeover: confidence 25<40; provider currently serves the host (not dangling), takeover conditional on future lapse.
[PARKED] dashboard.gamdom.com 403: confidence 30<40; fully ACL'd, no bypass found, locked-down admin host is inventory knowledge only.
[FINAL] survivors ranked: no new survivor above 40. Brand-wide origin-trust-pool cookie replay stays the [50] sole survivor (AUTH_HELPED, HUMAN-gated from prior cycles). This cycle's two finds (click third-party subdomain, dashboard 403) both park below threshold; no confirmed HIGH.
[NEXT] PROBE: re-check `click.gamdom.com` CNAME chain + root page periodically for a defensive monitor, and re-enumerate `dashboard.gamdom.com` paths/Host variants (read-only) for a 403 bypass — but the single highest-value un-executed action remains the AUTH_HELPED cookie-replay test: authenticate on gamdom80006.com, replay Cookie to gamdom.com & gamdom80007.com /client-api. Absent a session, all AUTH-class passive probes stay exhausted.
[LEARN] ACCEPTED recon @ click.gamdom.com: passive DNS + GET resolved in-scope subdomain to third-party SymplifyMail email provider (eu-iv-1.symplifymail.com 192.165.55.11) serving stock nginx default page — not currently dangling; subdomain-takeover watchlist item, not an active finding.
[LEARN] ACCEPTED recon @ dashboard.gamdom.com: passive GET/HEAD shows genuine scoped admin hostname on flagship Fastly Pool A, fully 403-locked at edge (Varnish Error 54113) across all probed paths; access-controlled admin vhost exists but no bypass — inventory knowledge only.
[LEARN] ACCEPTED recon @ help.gamdom.com: passive GET shows Intercom-hosted help center (x-intercom-version, /en/ 302) — standard third-party helpdesk SaaS, benign surface.
[RISK] gamdom: 50 — breadth scan of flagship/subdomain surface found no confirmed HIGH: dashboard.gamdom.com is fully 403-locked, click.gamdom.com points at a live third-party provider (not dangling), help is Intercom SaaS. The only open high-value class remains the brand-wide origin-trust-pool cookie replay ([50], HUMAN-gated authenticated session). Two low-confidence leads parked. No confirmed HIGH; no new positive exploit surface.
[HYP] click.gamdom.com third-party subdomain takeover/dangling CNAME
[HYP] dashboard.gamdom.com edge 403 is a misconfigured-but-routable admin vhost
## 2026-09-05 16:09:34 UTC [target] (model bigpickle)
reasoning: handshake issues SID with websocket upgrade; public status-page heartbeats already accepted recon; any admin RPC needs POST
evidence_needed: non-null getMonitorList/needSetup without admin token
verify_steps: only via POST socket.io RPC — not permitted (no-auth-bypass/mutate-against-live-data)
impact: deface official trust page / leak monitor IPs; low-med
testability: HUMAN_ONLY
[NEXT] HUMAN: authenticate on gamdom80006.com, replay exact session cookie header against gamdom80007.com/client-api (and gamdom4567.com/client-api); confirm cross-host acceptance to validate shared-trust cookie replay — then report, since all passive paths for the ATO class are now exhausted.
[LEARN] ACCEPTED recon @ gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle and stones the certificate of the cookie-replay hypothesis to TLS replay.
[RISK] gamdom: 58 — 7-8 mirror hostnames on one Fastly pool share one verified origin backend for a cookie-auth identity/wallet proxy; sole remaining ATO class is host-agnostic cookie acceptance, which requires an authenticated session to confirm (HUMAN gated). No confirmed HIGH; no new positive surface this cycle.
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at one backend behind every Gamdom domain including flagship
class: AUTH
asset: gamdom.com/client-api (covers io/eu/vip/win + 80006/80007/4567)
confidence: 50
reasoning: all 8 probed hosts return byte-identical 400 signature on GET (proven this cycle); JS bundle proves transport is server-set same-origin cookie; CORS absent (proven) closes client-side replay, leaving protocol-level cookie acceptance as the only open question
evidence_needed: session cookie minted on any one host accepted by /client-api on another (response class differs from invalid-cookie 400/401 baseline)
verify_steps: AUTH_HELPED — authenticate on gamdom80006.com, replay exact Cookie header to gamdom.com and gamdom80007.com/client-api, diff response; passive fallback is gated-POST byte-diff of error verbosity with identical invalid cookie
impact: single cookie theft or single mirror compromise grants account access across the entire Gamdom fleet incl. flagship; medium-high (amplification of any XSS/leak anywhere in the fleet)
testability: AUTH_HELPED
[HYP] gamdom80004.com alias widens trust surface
class: MISCONFIG
asset: gamdom80004.com (302→gamdom80007.com)
confidence: 20
reasoning: PROVEN this cycle it is redirect-only for both / and /client-api — cannot set or serve session cookies, only forwards browsers; residual risk only if A-record remapped to a live backend before the official status page is updated
evidence_needed: 80004 serving 400 signature or Set-Cookie directly (currently false)
verify_steps: GET /client-api on 80004 periodically; GET gamdommirrors.com status page for an 8th monitor
impact: none today; latent unmonitored landing host; low
testability: PASSIVE
[HYP] Kuma admin leak via status-page internals
class: MISCONFIG
asset: gamdommirrors.com /socket.io + /api/status-page/gamdom-domains
confidence: 35
reasoning: REST publicGroupList now empty; heartbeats/monitor config only reachable via socket.io RPC which is POST-only
evidence_needed: tokenless getMonitorList/needSetup via socket
verify_steps: only via POST socket.io RPC — not permitted (no-auth-bypass/mutate-against-live-data)
impact: deface official trust page/leak monitor IPs; low-med
testability: HUMAN_ONLY
[PARKED] gamdom80004 hidden-host: confidence 20; disproven as API-serving host this cycle (302-only).
[PARKED] Kuma admin leak: confidence 35<40; verification requires prohibited POST RPC; REST view now empty.
[PARKED] Prior [55] cross-mirror replay as standalone: CORS absence (no ACAO, preflight 400) removes the attacker-page vector; reframed as fleet-wide origin trust pool requiring an existing cookie.
[FINAL] survivors ranked: [50] brand-wide origin trust pool cookie replay (AUTH_HELPED, now spans gamdom.com) — sole survivor; both lower leads park.
[NEXT] HUMAN: authenticate on gamdom80006.com, replay the exact session Cookie header to gamdom.com/client-api AND gamdom80007.com/client-api; log the response vs an invalid-cookie baseline to prove host-blind validation across the fleet — then report; absent a session, all remaining passive probes for the AUTH class are exhausted.
[LEARN] ACCEPTED recon @ gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend signature spans the entire brand, not just mirrors; raises business value of the trust-pool hypothesis.
[LEARN] ACCEPTED recon @ gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory that implied an 8th API-serving mirror.
[LEARN] ACCEPTED recon @ gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credentialed replay is blocked, closing the client-side branch of the replay vector.
[RISK] gamdom: 52 — one verified backend serves POST-only cookie-auth /client-api for all 8 brand hosts incl. flagship gamdom.com, so any single stolen/leaked session cookie is fleet-wide; however no standalone reachable flaw: client-side replay is CORS-blocked, 80004 is redirect-only, and host-blind cookie acceptance remains unverified and HUMAN-gated. No confirmed HIGH.
[NEW] BREADTH-SCAN flagship/subdomain surface (previously under-probed): dashboard.gamdom.com resolves on flagship Fastly Pool A but is 403-locked at edge everywhere (/ /login /api /graphql /health /static → all 403, Varnish Error 54113 origin-restrict) — genuine scoped admin hostname, access-controlled, no bypass.
[NEW] click.gamdom.com → CNAME eu-proxy-1.symplifymail.com → eu-iv-1.symplifymail.com (192.165.55.11, third-party SymplifyMail email provider) serving stock nginx default page (200, X-Robots noindex, Last-Modified 2025-04) — in-scope subdomain pointed at external provider; currently serves content so NOT dangling, but subdomain-takeover WATCHLIST.
[NEW] help.gamdom.com → Intercom-hosted help center (x-intercom-version, /en/ 302, Intercom CSP) — standard third-party helpdesk SaaS, benign.
[PRIO] dashboard.gamdom.com,5.3,flagship-admin-host (attack:4 business:6 tech:6 gate:3 cloud:6 fresh:9)
[PRIO] click.gamdom.com,4.9,3rd-party-subdomain-takeover (attack:5 business:5 tech:4 gate:4 cloud:6 fresh:8)
[HYP] click.gamdom.com third-party subdomain is a takeover/dangling-CNAME candidate
class: OTHER
asset: click.gamdom.com (CNAME eu-proxy-1.symplifymail.com)
confidence: 25
reasoning: in-scope brand subdomain CNAMEs to external SymplifyMail email provider IP 192.165.55.11; provider currently serves stock nginx default page (200) so reference is NOT dangling today; takeover only if provider hostname/account lapses
evidence_needed: provider hostname returning NXDOMAIN/404 ownerless content, or CNAME repoint to attacker-controlled; confirm whether symplifymail host is brand-owned vs shared
verify_steps: periodic PASV: GET click.gamdom.com/ + re-resolve CNAME chain; check symplifymail host ownership/DNS
impact: full control of *.gamdom.com subdomain if the third-party reference lapses (phishing, OAuth credential harvesting under trusted host); medium if it transitions
testability: PASSIVE
[HYP] dashboard.gamdom.com edge 403 is a misconfigured admin host exposing denied-but-routable admin DNS
class: MISCONFIG
asset: dashboard.gamdom.com
confidence: 30
reasoning: genuine scoped hostname on flagship Fastly pool (151.101.x.52) but origin/edge denies all paths with Varnish Error 54113; no bypass found via common paths; indicates an admin/dashboard vhost exists upstream but is ACL'd
evidence_needed: any path/verb/Host returning non-403 (bypass) on the dashboard vhost; or backend internal endpoints
verify_steps: PASSIVE/HEAD only — enumerate paths + alternate Host header to edge, look for non-403; no mutation
impact: if ACL bypass found → admin portal/unauthorized access; currently locked, low
testability: PASSIVE
[PARKED] click.gamdom.com takeover: confidence 25<40; provider currently serves the host (not dangling), takeover conditional on future lapse.
[PARKED] dashboard.gamdom.com 403: confidence 30<40; fully ACL'd, no bypass found, locked-down admin host is inventory knowledge only.
[FINAL] survivors ranked: no new survivor above 40. Brand-wide origin-trust-pool cookie replay stays the [50] sole survivor (AUTH_HELPED, HUMAN-gated from prior cycles). This cycle's two finds (click third-party subdomain, dashboard 403) both park below threshold; no confirmed HIGH.
[NEXT] PROBE: re-check `click.gamdom.com` CNAME chain + root page periodically for a defensive monitor, and re-enumerate `dashboard.gamdom.com` paths/Host variants (read-only) for a 403 bypass — but the single highest-value un-executed action remains the AUTH_HELPED cookie-replay test: authenticate on gamdom80006.com, replay Cookie to gamdom.com & gamdom80007.com /client-api. Absent a session, all AUTH-class passive probes stay exhausted.
[LEARN] ACCEPTED recon @ click.gamdom.com: passive DNS + GET resolved in-scope subdomain to third-party SymplifyMail email provider (eu-iv-1.symplifymail.com 192.165.55.11) serving stock nginx default page — not currently dangling; subdomain-takeover watchlist item, not an active finding.
[LEARN] ACCEPTED recon @ dashboard.gamdom.com: passive GET/HEAD shows genuine scoped admin hostname on flagship Fastly Pool A, fully 403-locked at edge (Varnish Error 54113) across all probed paths; access-controlled admin vhost exists but no bypass — inventory knowledge only.
[LEARN] ACCEPTED recon @ help.gamdom.com: passive GET shows Intercom-hosted help center (x-intercom-version, /en/ 302) — standard third-party helpdesk SaaS, benign surface.
[RISK] gamdom: 50 — breadth scan of flagship/subdomain surface found no confirmed HIGH: dashboard.gamdom.com is fully 403-locked, click.gamdom.com points at a live third-party provider (not dangling), help is Intercom SaaS. The only open high-value class remains the brand-wide origin-trust-pool cookie replay ([50], HUMAN-gated authenticated session). Two low-confidence leads parked. No confirmed HIGH; no new positive exploit surface.
[HYP] click.gamdom.com third-party subdomain takeover/dangling CNAME
[HYP] dashboard.gamdom.com edge 403 is a misconfigured-but-routable admin vhost
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[PARKED] gamdom80007.com session replay: requires authenticated session first (AUTH_HELPED), cannot verify passively
[PARKED] gamdommirrors.com socket.io admin/mutation events exposed without auth: confidence 38 < 40 threshold; mutation against monitoring infra = REJECTED class risk per scope (no clear path to Gamdom-owned asset compromise)
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 55, AUTH_HELPED testability, medium-high impact)
[FINAL] 2. Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (confidence 50, AUTH_HELPED testability, high impact)
[FINAL] 3. /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment (confidence 48, AUTH_HELPED testability, critical-if-proven impact)
[NEXT] PROBE: GET (read-only) `https://gamdom80006.com/build/client.41b06529227c4b8b6a1d.js` — already fetched; now passively compare `Set-Cookie` headers on `POST /client-api` (400) vs `GET /` (200) across gamdom80006.com, gamdom80007.com, gamdom4567.com to extract cookie Domain/Path/SameSite attributes without authentication (<=1 rps, no mutation)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Domains" status page) and gamdom4567.com as its CNAME origin root
[LEARN] ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamdom domains — legitimate passive recon resolving true operating mirrors
[LEARN] ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com is the origin backend serving /client-api for all 8000x mirrors
[LEARN] ACCEPTED recon @ gamdom80006.com/build/client.41b06529227c4b8b6a1d.js: passive fetch of the app's own request-layer bundle (597 KB) proved auth transport is server-set same-origin cookie — no bearer/localStorage token — grounding the cross-mirror replay vector in evidence
[LEARN] ACCEPTED recon @ gamdommirrors.com/socket.io: GET-only EIO=4 polling handshake issues SID with websocket upgrade; no admin surface reachable without POST RPC, consistent with prior PARKED verdict
[LEARN] ACCEPTED recon @ gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle and stones the certificate of the cookie-replay hypothesis to TLS replay
[LEARN] ACCEPTED recon @ gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend signature spans the entire brand, not just mirrors; raises business value of the trust-pool hypothesis
[LEARN] ACCEPTED recon @ gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory that implied an 8th API-serving mirror
[LEARN] ACCEPTED recon @ gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credentialed replay is blocked, closing the client-side branch of the replay vector
[RISK] gamdom: 65 reason: Confirmed 7 mirror domains + 1 unlisted alias (gamdom80004.com) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary now verified (gamdom4567.com = backend for 8000x + gamdom80004); brand-wide trust pool extended to flagship gamdom.com via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
[NEW] gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend spans entire brand, not just mirrors
[NEW] gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory implying 8th API-serving mirror
[NEW] gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credentialed replay blocked
[CHANGED] gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle
[PRIO] gamdom4567.com/client-api,9.3,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=10
[PRIO] gamdom80006.com/client-api,8.9,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=10
[PRIO] gamdom80007.com/client-api,8.9,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=10
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom80004.com/client-api,8.2,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=8
[PRIO] gamdommirrors.com/api/status-page/gamdom-domains,6.5,attack_surface=7 business_value=6 tech_exposure=6 gate_ease=10 cloud_surface=5 freshness=8
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80006.com/client-api (shared origin gamdom4567.com, Pool B: 80004/80006/80007/4567)
confidence: 55
reasoning: Byte-identical SPA + shared origin backend across all mirrors; /client-api GET 400 with no ACAO on both mirrors and origin (no CORS vector); auth transport proven as server-set same-origin cookie via bundle analysis; replay feasibility depends on whether cookie is host-agnostic at shared backend
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80007/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80007; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80007/client-api
impact: Cross-domain account takeover across all 8 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship
class: AUTH
asset: gamdom.com/client-api (Pool A) and gamdom4567.com/client-api (Pool B)
confidence: 50
reasoning: gamdom.com/io/eu/vip/win/client-api returns byte-identical GET 400 body as Pool B mirrors; shared origin backend signature spans entire brand; if session cookie Domain attribute is .gamdom.com or host-agnostic at backend, flagship domain joins replay surface
evidence_needed: Set-Cookie Domain attribute from auth flow on gamdom.com vs mirrors; whether cookie from gamdom.com accepted by gamdom4567.com/client-api
verify_steps: (passive) compare Set-Cookie headers on auth endpoints across gamdom.com (Pool A) and gamdom80006.com (Pool B); (AUTH_HELPED) replay flagship session cookie against Pool B /client-api
impact: Cross-pool ATO across flagship + all 7 mirrors + alias; high
testability: AUTH_HELPED
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom4567.com/client-api (and all 7 mirrors + gamdom.com)
confidence: 48
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[PARKED] gamdom80007.com session replay: requires authenticated session first (AUTH_HELPED), cannot verify passively
[PARKED] gamdommirrors.com socket.io admin/mutation events exposed without auth: confidence 38 < 40 threshold; mutation against monitoring infra = REJECTED class risk per scope (no clear path to Gamdom-owned asset compromise)
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 55, AUTH_HELPED testability, medium-high impact)
[FINAL] 2. Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (confidence 50, AUTH_HELPED testability, high impact)
[FINAL] 3. /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment (confidence 48, AUTH_HELPED testability, critical-if-proven impact)
[NEXT] PROBE: GET (read-only) `https://gamdom80006.com/build/client.41b06529227c4b8b6a1d.js` — already fetched; now passively compare `Set-Cookie` headers on `POST /client-api` (400) vs `GET /` (200) across gamdom80006.com, gamdom80007.com, gamdom4567.com to extract cookie Domain/Path/SameSite attributes without authentication (<=1 rps, no mutation)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Domains" status page) and gamdom4567.com as its CNAME origin root
[LEARN] ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamdom domains — legitimate passive recon resolving true operating mirrors
[LEARN] ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com is the origin backend serving /client-api for all 8000x mirrors
[LEARN] ACCEPTED recon @ gamdom80006.com/build/client.41b06529227c4b8b6a1d.js: passive fetch of the app's own request-layer bundle (597 KB) proved auth transport is server-set same-origin cookie — no bearer/localStorage token — grounding the cross-mirror replay vector in evidence
[LEARN] ACCEPTED recon @ gamdommirrors.com/socket.io: GET-only EIO=4 polling handshake issues SID with websocket upgrade; no admin surface reachable without POST RPC, consistent with prior PARKED verdict
[LEARN] ACCEPTED recon @ gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle and stones the certificate of the cookie-replay hypothesis to TLS replay
[LEARN] ACCEPTED recon @ gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend signature spans the entire brand, not just mirrors; raises business value of the trust-pool hypothesis
[LEARN] ACCEPTED recon @ gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory that implied an 8th API-serving mirror
[LEARN] ACCEPTED recon @ gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credentialed replay is blocked, closing the client-side branch of the replay vector
[RISK] gamdom: 65 reason: Confirmed 7 mirror domains + 1 unlisted alias (gamdom80004.com) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary now verified (gamdom4567.com = backend for 8000x + gamdom80004); brand-wide trust pool extended to flagship gamdom.com via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80006.com/client-api (shared origin gamdom4567.com, Pool B: 80004/80006/80007/4567)
confidence: 50
reasoning: byte-identical SPA + shared origin backend across all mirrors; auth transport proven as server-set same-origin cookie via bundle analysis; CORS absent on /client-api (no ACAO) closes attacker-page vector; host-blind cookie acceptance remains the sole unverified link in the chain
evidence_needed: Set-Cookie Domain/Path/SameSite attributes from login/register pages across gamdom80006.com, gamdom80007.com, and gamdom.com — determines if cookie scope is per-host, per-subdomain, or backend-wide; or session replay confirmation
verify_steps: PASSIVE: GET https://gamdom80006.com/ and https://gamdom80007.com/ and https://gamdom.com/ logging full Set-Cookie headers (if any) — compare Domain attribute; also check /login and /register paths for session-setting behavior (1 req each)
impact: single cookie theft grants account access across entire Gamdom fleet incl. flagship; medium-high
testability: PASSIVE
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom4567.com/client-api (and all 7 mirrors + gamdom.com)
confidence: 45
reasoning: /client-api is POST-only catch-all proxy returning identical 400 on GET/OPTIONS; bundle shows api factory calling methods by action name; if proxy forwards subpath/payload to upstream identity/wallet service, mass-assignment or SSRF-on-proxy risks emerge; but verification requires POST with authorized session (REJECTED for blind probing)
evidence_needed: POST with crafted body returning upstream internal error/status differing by injected subpath; or bundle resolving action->endpoint table via authorized session
verify_steps: PASSIVE: enumerate GET/OPTIONS on /client-api subpaths (already done); AUTH_HELPED: POST with session to test action routing
impact: fund theft or ATO if wallet/deposit endpoints reachable; critical — but unproven
testability: AUTH_HELPED
[HYP] Set-Cookie attribute extraction reveals cookie scope across fleet
class: AUTH
asset: gamdom80006.com and gamdom.com (root + /login pages)
confidence: 42
reasoning: prior cycles proved auth is server-set same-origin cookie but never extracted actual Set-Cookie headers from any response (only GET / returning 200 SPA). If login/register pages set session cookies with Domain=.gamdom.com attribute, fleet-wide replay is proven passively. If Domain=per-host, scope is narrower.
evidence_needed: full Set-Cookie header from GET https://gamdom80006.com/ or /login or /register across Pool A and Pool B hosts
verify_steps: PASSIVE: HEAD+GET https://gamdom80006.com/login and https://gamdom.com/login (1 req each) — log Set-Cookie headers; compare Domain attribute
impact: determines scope of session cookie (per-host vs subdomain-wide) — either confirms or kills the fleet-replay hypothesis without authentication
testability: PASSIVE
[PARKED] /client-api SSRF/mass-assignment: confidence 45 but verify_steps requires AUTH_HELPED live POST; no passive-only verification path exists — REJECTED auth-bypass learning blocks this. Parked.
[PARKED] Cross-mirror cookie replay: confidence 50, but ACTUAL testability is PASSIVE now that I've reframed the HYP toward cookie attribute extraction. Keep but reframe the evidence_needed to Set-Cookie headers.
[FINAL] survivors ranked:
[NEXT] PROBE: `curl -sS -D- -o /dev/null https://gamdom80006.com/login https://gamdom.com/login https://gamdom80007.com/login https://gamdom.com/register` (<=4 sequential GET requests, 1 rps, read-only) — extract Set-Cookie headers with Domain/Path/SameSite attributes to determine cookie scope across Pool A vs Pool B; also try GET https://gamdom.com/ and https://gamdom80006.com/ with full header dump to catch any session cookies set on initial page load
[LEARN] ACCEPTED recon @ gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend signature spans entire brand
[LEARN] ACCEPTED recon @ gamdom80004.com/client-api: 302 → gamdom80007.com/client-api — redirect alias only, not API-serving
[LEARN] ACCEPTED recon @ gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no ACAO — browser cross-origin credentialed replay blocked
[RISK] gamdom: 50 — sole surviving hypothesis (brand-wide origin trust pool cookie replay, [50]) remains HUMAN-gated; all passive AUTH-class probes exhausted without a session; dashboard.gamdom.com 403-locked; click.gamdom.com third-party subdomain not dangling; CORS blocks client-side replay. No confirmed HIGH. Passive cookie-scope extraction is the last read-only probe available.
## 2026-09-05 18:23:33 UTC [target] (model bigpickle)
[HYP] Session cookie Domain/scope is per-host (host-only), not fleet-wide
class: AUTH
asset: gamdom.com/client-api (shared origin backend across all domains)
confidence: 40
reasoning: (1) Every GET across Pool A (gamdom.com) and Pool B (80006/80007) sets ONLY `gd-lang=en-gb`, host-only, no Domain attr, identical name/attrs — proving server cookie-issuance policy on GET is per-host; (2) /login, /register are 404 (SPA-routed) so no server session cookie on those paths; (3) session cookie server-issued only via authenticated /client-api POST; (4) if the same per-host policy applied to session cookie, fleet-replay is dead; if the session cookie were backend-issued with subdomain-Domain (.gamdom.com), replay survives. Unknown which, requires auth.
evidence_needed: Set-Cookie header from an authenticated /client-api POST on either pool — requires valid session (AUTH_HELPED)
verify_steps: PASSIVE: exhausted (all GET/HEAD/Opt-in surfaces probed — no session cookie on any unauthenticated response; host-only policy on the only observable cookie). AUTH_HELPED: POST /client-api with valid credentials on Pool B, log Set-Cookie Domain attr; then replay on Pool A.
impact: determines whether single-session theft crosses the whole brand (critical) or is host-isolated (medium-low)
testability: AUTH_HELPED
[HYP] gd-lang cookie (host-only, no SameSite/no HttpOnly) is a self-XSS/session-cement primitive but NOT an auth cookie
class: AUTH
asset: gamdom.com (all hosts)
confidence: 60
reasoning: gd-lang is the only observable cookie, set with no SameSite (allows cross-site sends) and no HttpOnly; but it is a `gd-lang` language pref, clearly not the auth session cookie. Its cross-site-readable nature is only relevant if it held auth value — it does not. Not a reportable vuln on its own (default cookie flags on non-sensitive data = REJECTED class). Kill.
evidence_needed: none (non-sensitive data cookie = rejected class)
verify_steps: none
impact: none (rejected class)
testability: N/A
[HYP] /client-api catch-all proxy SSRF/mass-assignment
class: OTHER
asset: gamdom4567.com/client-api
confidence: 45
reasoning: POST-only catch-all proxy; bundle shows action-name routing; upstream forwarding to wallet/auth possible; but verification requires authenticated live POST (REJECTED for blind probing), and no passive endpoint-map extractable from bundle (action strings obfuscated).
evidence_needed: authenticated POST returning upstream-differentiated status by injected action/subpath
verify_steps: AUTH_HELPED only
impact: fund theft/ATO if reachable — critical if proven
testability: AUTH_HELPED
## 2026-09-05 20:47:09 UTC [target] (model bigpickle)
[HYP] Cross-host session replay across the now-13-host brand trust pool (Pool A + Pool B + 4 regional TLDs)
class: AUTH
asset: gamdom.eu/client-api (and gamdom.io, gamdom.vip, gamdom.win, gamdom80006.com, gamdom.com)
confidence: 60
reasoning: all 13 hosts share one POST-only /client-api backend (byte-identical 400 signature); auth transport proven server-set same-origin cookie (bundle: credentials same-origin, zero bearer/localStorage); every host sets only host-only `gd-lang` on GET (no Domain) with URL-identical policy verified on gamdom.eu/gamdom.win; CORS absent blocks browser replay; the session cookie's Domain attribute is the sole unverified link
evidence_needed: Set-Cookie Domain/Path/SameSite from an authenticated /client-api POST on any brand host (Pool A or B)
verify_steps: AUTH_HELPED: POST /client-api with valid session on gamdom80006.com, log Domain attr; replay same cookie on gamdom.eu and gamdom.com
impact: single session theft = account access across flagship + 4 regional TLDs + 7 mirrors (funds, withdrawal, KYC PII); critical
testability: AUTH_HELPED
[HYP] Staff-rated /client-api actions reachable via name-based action routing (staffRefillConfig present client-side)
class: AUTH
asset: gamdom.com/client-api catch-all proxy
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) and a verifyProxies gate = staff refill tooling exists; /client-api routes by action name (bundle API factory); if privilege is client-asserted (flags) rather than server-scoped, a forged action could hit refill/wallet ops; blind proof requires authenticated POST (REJECTED class)
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names
verify_steps: AUTH_HELPED only; no passive path (action strings obfuscated, no endpoint map in bundle)
impact: coin/wallet refill or staff-operation abuse; critical if proven
testability: AUTH_HELPED
[HYP] Session cookie Domain is subdomain-wide (.gamdom.com) vs host-only — determines replay scope
class: AUTH
asset: gamdom.com/client-api
confidence: 40
reasoning: gd-lang is host-only on every brand host (verified across Pool A and B and now regional TLDs) but is non-sensitive; session cookie issued only via authenticated POST; if session follows same host-only policy replay dies, if backend issues .gamdom.com replay crosses all 13 hosts
evidence_needed: Domain attr of authenticated session cookie
verify_steps: passive exhausted (no session cookie on any unauthenticated response); AUTH_HELPED only
impact: bounds critical-vs-medium of fleet ATO; gating evidence only
testability: AUTH_HELPED
[NEXT] PROBE: `curl -sS -m 12 "https://gamdommirrors.com/status/gamdom-domains"` then re-fetch `client.41b06529227c4b8b6a1d.js` diff only if hash changed — single passive GET to confirm whether eu/io/vip/win are on the official monitor list (parallels prior ACCEPTED inventory method for gamdom80007); else treat bundle link-list as the official listing.
[LEARN] ACCEPTED inventory @ gamdom.eu/gamdom.io/gamdom.vip/gamdom.win: passive mining of the flagship SPA's own link list yielded 4 live official regional TLDs on Pool A (151.101.x.52), each serving full SPA + byte-identical `/client-api` 400 `Invalid request, only POST` — same-family brand domains widening the trust pool by 4 hosts
[LEARN] ACCEPTED recon @ login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com: all NXDOMAIN (matches api/auth.gamdom.com 000) — the inventory's auth/admin-flavored subdomains are dead DNS; live scoped surface is now fully enumerated
[LEARN] ACCEPTED recon @ gamdom.eu/gamdom.win: root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — server cookie-issuance policy uniform per-host across the entire 13-host fleet
[RISK] gamdom: 62 — confirmed 13 live TLS hosts sharing a single POST-only /client-api backend with per-host server-set session cookies; single-session theft would cross flagship, 4 regional TLDs, and 7 mirrors (funds + KYC). No data touched; ATO remains human-gated behind an authenticated Set-Cookie replay test; all passive avenues exhausted.
