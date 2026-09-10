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
## 2026-09-05 22:44:36 UTC [target] (model bigpickle)
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
[HYP] Rotating official mirror enumeration via brand SEO hub yields provisioned /client-api hosts
class: AUTH
asset: gamdom90471.com/client-api (CNAME→gamdom4567.com)
confidence: 55
reasoning: 90471 CNAME→gamdom4567.com identical to live 80006/80007 (both serve byte-identical /client-api 400 on 4567 origin); self-referenced 18× in official HomePage bundle; SEO hub declares "Aktif" yet edge returns fallback TLS cert on current node — implies Fastly host-cert not yet propagated/per-edge or host currently rotating; CT shows the domain has real DigiCert/Certainly certs going back 2025-10, so a capable node/edge will serve it.
evidence_needed: render /client-api on a Fastly edge with valid host SNI cert (try other pool A/edge IPs, or wait for rotation) → confirm byte-identical 400 `Invalid request, only POST`; presence on official monitor list once rotated.
verify_steps: PASSIVE: `curl -D` on `https://gamdom90471.com/client-api` on more edge IPs for the 4567 anycast (151.101.3.67/68/131/195 .72) plus re-Check once the SEO hub flips to a new numbered name; diff body against `/client-api` on gamdom80007.com.
impact: widens the 13-host brand trust pool by a rotating alias that will carry the same shared session-cookie backend — additive to cross-host replay ATO; mostly inventory value.
testability: PASSIVE
[HYP] Brand SEO hub (gamdom-girisi.com) → open-redirect / phishing-adjacent chain
class: OTHER
asset: gamdom-girisi.com (Cloudflare, tr)
confidence: 35
reasoning: hub is Cloudflare-hosted (not Fastly origin), links only to gamdom90471.com + gamdomgiris.link which are NOT on the 7-monitor status page; if gamdom90471.com gets registered/abandoned, an attacker with same CNAME-control could serve/phish; but the hub is presumably Gamdom-controlled (linked from official bundle) and 90471 is provisioned on the real origin, so takeover risk is speculative now.
evidence_needed: any unvalidated `?next=`/`?url=`/`?redirect=` parameter on the hub or 90471 that redirects off-brand; CTRL over gamdom90471.com domain slipping to a squatter.
verify_steps: PASSIVE grep of hub HTML/bundle for redirect params (done — none found bare); enumerate get params on gamdom-girisi.com/ and gamdom90471.com for 3xx off-brand.
impact: phishing/brand-jacking of Turkish Gamdom users; medium
testability: PASSIVE
[HYP] Untracked SEO mirror host as an extra entry in the fleet-wide session-replay trust pool
class: AUTH
asset: gamdom90471.com/client-api (when live)
confidence: 45
reasoning: 90471 CNAME→4567 origin = same backend that issues host-only session cookies; when it becomes live it will accept the same /client-api POSTs and, per the shared backend, accept the same session cookie Domain-scope as the other 13; it adds one more cross-host replay target.
evidence_needed: authenticated session Set-Cookie + replay on 90471 once live; proving replay on the established 13 already gates this (AUTH_HELPED).
verify_steps: AUTH_HELPED only (POST /client-api with valid session on 80006, replay on 90471) — entirely blocked without a session; mirrors lead-1 gating evidence.
impact: one more node in critical fleet ATO blast radius; no independent value
testability: AUTH_HELPED
[NEXT] PROBE: passive re-watch the SEO hub — `curl -sS -m 12 "https://gamdom-girisi.com/"` + grep for the current `gamdom[0-9]+\.com` target and for any new 3xx redirect params; if the flip lands on a NEW numbered name, resolve its CNAME and re-test `https://<new>/client-api` for the 400 signature. (Single read-only GET on rotation; keeps inventory current for the 14-host trust pool.)
[LEARN] ACCEPTED inventory @ gamdom90471.com: CNAME→gamdom4567.com (verified origin, same as 80006/80007), Fastly Pool B (151.101.67.72), self-referenced 18× in the official flagship HomePage bundle via the brand's own Turkish SEO hub gamdom-girisi.com which tags it "Aktif"; CT shows real DigiCert/Certainly host certs since 2025-10; currently only the fallback.tls.fastly.net SNI cert is served → provisioned-but-not-yet-live on current edge node = 8th mirror alias / 14th hostname in the brand trust pool (off the 7-monitor status page).
[LEARN] ACCEPTED recon @ gamdom-girisi.com: official brand SEO/redirect hub (Cloudflare, Turkish, 61 KB) funneling to gamdom90471.com and discord/telegram; linked from the flagship HomePage.js — a legitimate discovery seed for the rotating mirror fleet, not a threat.
[LEARN] ACCEPTED recon @ gamdomgiris.link: third-party Cloudflare landing alongside the SEO hub; no Fastly origin /client-api signature → NOT in-scope brand origin (watchlist only).
[LEARN] ACCEPTED recon @ gamdommirrors.com/status/gamdom-domains: official Uptime status page lists exactly 7 monitors = com/eu/io/vip/win/80006/80007 → independently ratifies the 4 regional TLDs as official and confirms gamdom90471.com is not yet on the monitor list (rotating alias before listing).
[RISK] gamdom: 63 — confirmed 13 live TLS hosts + 1 provisioned (90471) sharing a single POST-only /client-api backend (verified origin 4567) with per-host server-set session cookies; single-session theft would cross flagship + 4 regional TLDs + 7 live mirrors + the rotating alias (funds, withdrawal, KYC). No data touched; ATO remains human-gated behind one authenticated Set-Cookie replay test; all passive avenues exhausted.
## 2026-09-06 00:18:39 UTC [target] (model bigpickle)
[HYP] Cross-host session replay across the 14-host brand trust pool — single shared nginx/Starlette origin proven fleet-wide via /health
class: AUTH
asset: gamdom.com/client-api (shared backend: Pool A + Pool B + 4567 + provisioned 90471)
confidence: 60
reasoning: /client-api GET+OPTIONS 400 `Invalid request, only POST` (content-length 26) byte-identical on Pool A+B; /health now byte-identical (200 `ok`, same Starlette ETag, server nginx) across com/80006/80007/eu/io/4567 in the same probe cycle; bundle proves auth transport = server-set same-origin cookie (zero bearer/localStorage); every host sets only host-only `gd-lang`; no ACAO → browser cross-origin replay dead; only unverified link = Domain/Path/SameSite attr of the authenticated session cookie.
evidence_needed: Set-Cookie attributes from an authenticated /client-api POST on any brand host, then replay result on a different brand host.
verify_steps: AUTH_HELPED: POST /client-api with valid session on gamdom80006.com (Pool B), log Set-Cookie Domain/Path/SameSite; replay the cookie verbatim on gamdom.eu and gamdom.com (Pool A), compare session-acceptance.
impact: single-session theft → account control across flagship + 4 regional TLDs + 7 mirrors + rotating alias (funds, withdrawal, KYC PII); critical.
testability: AUTH_HELPED
[HYP] Rotating mirror alias joins the shared backend once host cert propagates
class: AUTH
asset: gamdom90471.com/client-api (CNAME→gamdom4567.com)
confidence: 55
reasoning: 90471 CNAME→4567 (verified origin), self-referenced 18× in official HomePage bundle, SEO hub tags "Aktif", real DigiCert/Certainly certs in CT since 2025-10; yet all 4 Pool B edges still serve only fallback.tls.fastly.net this cycle → provisioned-not-live, off the 7-monitor status page; /health on 4567 confirms it is live as origin backend today.
evidence_needed: /client-api renders byte-identical 400 on an edge serving a valid 90471 host SNI cert; appears on status-page monitor list.
verify_steps: PASSIVE: `curl --resolve gamdom90471.com:443:<edge> https://gamdom90471.com/client-api` on 151.101.{3,67,131,195}.72 each cycle + re-watch gamdom-girisi.com and gamdommirrors.com/status/gamdom-domains for list changes; byte-diff against gamdom80007.com/client-api.
impact: one more node in critical fleet ATO blast radius; inventory value.
testability: PASSIVE
[HYP] Staff-rated /client-api actions reachable by name-based action routing, privilege client-asserted
class: AUTH
asset: gamdom.com/client-api catch-all proxy (Starlette origin)
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate = refill tooling exists client-side; /client-api routes by action name (bundle API factory); Starlette origin now confirmed; if privilege is flag-asserted rather than server-scoped, a forged action name targets refill/wallet ops; blind proof requires authenticated POST (REJECTED class).
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; action strings obfuscated, no endpoint map in bundle, no passive path.
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
## 2026-09-06 04:48:53 UTC [target] (model bigpickle)
[HYP] Cross-host session replay across the 14-host brand trust pool — single shared nginx/Starlette origin proven fleet-wide (/health 200 byte-identical)
class: AUTH
asset: gamdom.com/client-api (Pool A + Pool B + 4567 + provisioned 90471)
confidence: 60
reasoning: /client-api GET/OPTIONS 400 (26B) byte-identical across all pools; bundle proves auth transport = server-set same-origin cookie, zero bearer/localStorage; each host sets only host-only `gd-lang`; no ACAO closes browser cross-origin replay; only unverified link = Domain/Path/SameSite of the authenticated session cookie.
evidence_needed: Set-Cookie attributes from an authenticated /client-api exchange on one brand host, then reuse on a different brand host.
verify_steps: AUTH_HELPED — capture Set-Cookie (Domain/Path/SameSite) from an authenticated POST on gamdom80006.com, replay verbatim on gamdom.eu + gamdom.com, compare session acceptance.
impact: single session theft → account control across flagship + 4 regional TLDs + 7 mirrors + rotating alias (funds, withdrawal, KYC PII); critical.
testability: AUTH_HELPED
[HYP] Rotating mirror alias (90471) joins the shared backend once the host cert propagates
class: AUTH
asset: gamdom90471.com/client-api (CNAME→gamdom4567.com)
confidence: 55
reasoning: CNAME→verified origin 4567 (same as live 80006/80007); self-referenced 18× in official HomePage bundle; SEO hub tags "Aktif"; real DigiCert/Certainly certs in CT since 2025-10; yet this cycle all Pool B edges still serve only fallback.tls.fastly.net → provisioned-not-live, off the 7-monitor status page.
evidence_needed: /client-api renders byte-identical 400 on an edge serving a valid 90471 host SNI cert; domain appears on gamdommirrors.com monitor list.
verify_steps: PASSIVE — `curl --resolve gamdom90471.com:443:151.101.67.72 https://gamdom90471.com/client-api` each cycle; diff vs gamdom80007.com/client-api; re-watch gamdom-girisi.com + status page for a new numbered name.
impact: one more node in the fleet ATO blast radius; inventory value.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action-name routing
class: AUTH
asset: gamdom.com/client-api catch-all proxy
confidence: 40
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate; /client-api routes by action name; but this cycle's path-scan shows the catch-all 400s every GET subpath and origin 4567 exposes no /docs|/openapi.json → action map formally unenumerable; privilege model untestable without authenticated POST.
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; no passive path remains (endpoint map closed).
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
[NEXT] PROBE: rotation watch (single read-only pass): `curl -sS -m 12 "https://gamdom-girisi.com/"` and grep `gamdom[0-9]+\.com`; `curl -sS -m 12 "https://gamdommirrors.com/status/gamdom-domains"` and grep monitor hostnames; `curl -sS -m 8 --resolve gamdom90471.com:443:151.101.67.72 "https://gamdom90471.com/client-api"` — diff numbered target vs 90471 and body vs gamdom80007.com/client-api.
[RISK] gamdom: 63 — unchanged: confirmed 13 live TLS hosts + 1 provisioned (90471) sharing a single POST-only `/client-api` backend (verified origin 4567) with per-host server-set session cookies; single-session theft crosses flagship + 4 regional TLDs + 7 mirrors + rotating alias (funds, withdrawal, KYC). No data touched; ATO remains human-gated behind one authenticated Set-Cookie replay test; fleet schema-leak angle formally closed this cycle; all passive avenues exhausted.
## 2026-09-06 09:11:01 UTC [target] (model bigpickle)
[HYP] Cross-host session replay across the 14-host brand trust pool — single shared nginx/Starlette origin now proven fleet-wide via byte-identical /health ETag
class: AUTH
asset: gamdom.com/client-api (Pool A + Pool B + 4567)
confidence: 62
reasoning: /client-api GET/OPTIONS 400 (26B) byte-identical across pools; /health 200 content-length 2 `ok` + identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" + server nginx across com/eu/80006/80007/4567 in a single cycle; bundle proves auth transport = server-set same-origin cookie (zero bearer/localStorage); each host sets only host-only `gd-lang`; no ACAO closes browser cross-origin replay; only unverified link = Domain/Path/SameSite of the authenticated session cookie.
evidence_needed: Set-Cookie attributes from an authenticated /client-api POST on one brand host, then reuse on a different brand host.
verify_steps: AUTH_HELPED — capture Set-Cookie (Domain/Path/SameSite) via authenticated POST on gamdom80006.com, replay verbatim on gamdom.eu + gamdom.com, compare session acceptance.
impact: single session theft → account control across flagship + 4 regional TLDs + 7 mirrors + rotating alias (funds, withdrawal, KYC PII); critical.
testability: AUTH_HELPED
[HYP] Rotating mirror alias (90471) joins the shared backend once the host cert propagates
class: AUTH
asset: gamdom90471.com/client-api (CNAME→gamdom4567.com)
confidence: 55
reasoning: CNAME→verified origin 4567 (same as live 80006/80007); self-referenced 18× in flagship HomePage.js; SEO hub tags "Aktive"; real DigiCert/Certainly certs in CT since 2025-10; yet this cycle all 4 Pool B edges still return HTTP 421 on host SNI → fallback.tls.fastly.net only; status-page JSON still has no 90471 monitor.
evidence_needed: /client-api renders byte-identical 400 (26B) on an edge serving a valid 90471 host cert; monitor appears on status-page JSON.
verify_steps: PASSIVE — `curl --resolve gamdom90471.com:443:{151.101.3,67,131,195}.72 https://gamdom90471.com/client-api` each cycle (expect 000/421 until live); byte-diff against gamdom80007.com/client-api; watch status-page JSON for a new monitor entry.
impact: one more node in fleet ATO blast radius; inventory value.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action-name routing
class: AUTH
asset: gamdom.com/client-api catch-all proxy
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate; /client-api routes by action name; /health ETag confirms Starlette origin on both pools; catch-all 400s every GET subpath so action map formally unenumerable; privilege model untestable without authenticated POST.
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; no passive path remains.
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
[NEXT] PROBE: passive adjacent-numbered mirror inventory (read-only DNS, no HTTP): `dig +short gamdom80005.com; dig +short gamdom80008.com; dig +short gamdom80003.com; dig +short gamdom90472.com` — flag any that resolve to Fastly CNAME/151.101.x.xx as new provisioned aliases (280-char cost, 4 lookups, ≤1rps); else confirm none beside the known 80004/80006/80007/90471.
[RISK] gamdom: 63 — unchanged. Confirmed 13 live TLS hosts + 1 provisioned (90471) sharing one POST-only `/client-api` identity/wallet proxy (origin 4567) with per-host server-set session cookies; single-session theft crosses flagship + 4 regional TLDs + 7 mirrors + rotating alias (funds, withdrawal, KYC). No data touched; ATO remains human-gated behind one authenticated Set-Cookie replay test; all passive avenues exhausted; rotation watch shows no fleet topology change this cycle.
## 2026-09-06 12:57:39 UTC [target] (model bigpickle)
[PRIO] gamdom.com/client-api (Pool A+P+B),9.8,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=1 cloud_surface=7 freshness=9
[PRIO] gamdom80003.com/client-api (Pool B),9.5,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=1 cloud_surface=7 freshness=10
[PRIO] gamdom90472.com/client-api (Pool B),6.0,attack_surface=8 business_value=10 tech_exposure=9 gate_ease=1 cloud_surface=7 freshness=10
[HYP] Cross-host session replay across the 16-host brand trust pool — single shared nginx/Starlette origin proven fleet-wide via byte-identical /health ETag + /client-api across 9 live mirrors
class: AUTH
asset: gamdom.com/client-api (Pool A) + gamdom80003/80006/80007.com (Pool B) + gamdom4567.com + gamdom.eu/io/vip/win (Pool A) = 15 live hostnames + 1 provisioned (90472)
confidence: 62
reasoning: /health 200 body `ok` + identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" + server nginx confirmed on gamdom80003.com (ALL 4 Pool B edges) identical to com/eu/80006/80007/4567; /client-api 400 (26B) byte-identical across all pools; bundle proves auth transport = server-set same-origin cookie (zero bearer/localStorage); each host sets only host-only gd-lang; no ACAO on OPTIONS/Origin preflight closes browser cross-origin replay; only unverified link = Domain/Path/SameSite of the authenticated session cookie.
evidence_needed: Set-Cookie attributes from an authenticated /client-api POST on one brand host, then reuse on a different brand host.
verify_steps: AUTH_HELPED — capture Set-Cookie (Domain/Path/SameSite) via authenticated POST on gamdom80006.com, replay verbatim on gamdom.eu + gamdom.com + gamdom80003.com, compare session acceptance.
impact: single session theft → account control across flagship + 4 regional TLDs + 9 mirrors + 2 provisioned aliases (funds, withdrawal, KYC PII); critical.
testability: AUTH_HELPED
[HYP] Rotating mirror aliases (90471 + 90472) join the shared backend once host certs propagate
class: AUTH
asset: gamdom90471.com + gamdom90472.com/client-api (CNAME→gamdom4567.com)
confidence: 55
reasoning: Both CNAME→verified origin 4567 (same as live 80003/80006/80007); gamdom90471.com self-referenced 18× in official HomePage.js, SEO hub tags "Aktif"; real DigiCert/Certainly certs in CT since 2025-10; gamdom90472.com same CNAME+Pool B DNS pattern; yet this cycle both return HTTP 000 on all 4 Pool B edges; status-page JSON still has no monitor for either.
evidence_needed: /client-api renders byte-identical 400 (26B) on an edge serving a valid host cert for either domain; monitor appears on status-page JSON.
verify_steps: PASSIVE — `curl --resolve {gamdom90471,gamdom90472}.com:443:{151.101.3,67,131,195}.72 https://$host/client-api` each cycle (expect 000 until live); byte-diff against gamdom80007.com/client-api; watch status-page JSON for new monitor entries.
impact: 2 more nodes in fleet ATO blast radius; inventory value.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action-name routing
class: AUTH
asset: gamdom.com/client-api catch-all proxy
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate; /client-api routes by action name; /health ETag confirms Starlette origin on both pools; catch-all 400s every GET subpath so action map formally unenumerable; privilege model untestable without authenticated POST.
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; no passive path remains.
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
[NEXT] PROBE: passive fleet expansion scan (read-only DNS): `dig +short gamdom80009.com; dig +short gamdom80010.com; dig +short gamdom80011.com; dig +short gamdom90473.com; dig +short gamdom90474.com` — flag any resolving to gamdom4567.com CNAME or 151.101.x.72 Fastly Pool B IPs as new provisioned aliases (280-char cost, 5 lookups, ≤1rps); also re-probe 90471/90472 on all edges for cert propagation.
[LEARN] ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" + /client-api 400 (26B) byte-identical — confirmed 9th live mirror, 15th hostname in brand trust pool; off the 7-monitor status page.
[LEARN] ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool; off the 7-monitor status page.
[LEARN] REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure.
[LEARN] REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure.
[RISK] gamdom: 63 — unchanged. 16 hostnames (14 live + 2 provisioned) confirmed sharing one POST-only /client-api identity/wallet proxy (verified origin gamdom4567.com) with per-host server-set session cookies; fleet now includes gamdom80003.com (9th live mirror, byte-identical /health ETag) and gamdom90472.com (provisioned alias); single-session theft crosses flagship + 4 regional TLDs + 9 mirrors + 2 rotating aliases (funds, withdrawal, KYC PII). No data touched; ATO remains human-gated behind one authenticated Set-Cookie replay test; all passive avenues exhausted; fleet topology +2 hostnames this cycle.
## 2026-09-06 15:59:52 UTC [target] (model bigpickle)
[HYP] Cross-host session replay across the now-17-host brand trust pool — single shared nginx/Starlette origin proven fleet-wide via byte-identical /health ETag + /client-api
class: AUTH
asset: gamdom.com/client-api (Pool A) + gamdom80003/80006/80007.com (Pool B) + gamdom4567.com + gamdom.eu/io/vip/win (Pool A) = 15 live hostnames + 2 provisioned (90472/90473)
confidence: 62
reasoning: /health 200 body `ok` + identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" + server nginx re-confirmed on gamdom80003.com Pool B this cycle, identical to com/eu/80006/80007/4567; /client-api 400 (26B) byte-identical across all pools; bundle proves auth transport = server-set same-origin cookie (zero bearer/localStorage); each host sets only host-only gd-lang; no ACAO on OPTIONS/Origin preflight closes browser cross-origin replay; only unverified link = Domain/Path/SameSite of the authenticated session cookie.
evidence_needed: Set-Cookie attributes from an authenticated /client-api POST on one brand host, then reuse on a different brand host.
verify_steps: AUTH_HELPED — capture Set-Cookie (Domain/Path/SameSite) via authenticated POST on gamdom80006.com, replay verbatim on gamdom.eu + gamdom.com + gamdom80003.com, compare session acceptance.
impact: single session theft → account control across flagship + 4 regional TLDs + 9 mirrors + 2 provisioned aliases (funds, withdrawal, KYC PII); critical.
testability: AUTH_HELPED
[HYP] Rotating mirror aliases (90471 + 90472 + 90473) transition from provisioned → live and join the shared backend
class: AUTH
asset: gamdom90471.com + gamdom90472.com + gamdom90473.com/client-api (CNAME→gamdom4567.com)
confidence: 60
reasoning: All three CNAME→verified origin 4567 (same as live 80003/80006/80007); gamdom90471.com self-referenced 18× in official HomePage.js, SEO hub tags "Aktif"; this cycle all three upgraded from HTTP 000 → Fastly 421 (edge now routes host, TLS cert not yet in host config) — a concrete provisioning milestone distinct from last cycle; status-page JSON still shows no monitor for any.
evidence_needed: any edge serving a valid host SNI cert for 90471/90472/90473 → /client-api renders byte-identical 400 (26B); monitor appears on status-page JSON.
verify_steps: PASSIVE — `curl --resolve {gamdom90471,gamdom90472,gamdom90473}.com:443:{151.101.3,67,131,195}.72 https://$host/client-api` each cycle (expect 421 until cert propagates); byte-diff against gamdom80007.com/client-api; watch status-page JSON for new monitor entries.
impact: 3 more nodes in fleet ATO blast radius; inventory value.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action-name routing
class: AUTH
asset: gamdom.com/client-api catch-all proxy
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate; /client-api routes by action name; /health ETag confirms Starlette origin on both pools; catch-all 400s every GET subpath so action map formally unenumerable; privilege model untestable without authenticated POST.
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; no passive path remains.
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
[NEXT] PROBE: rotation/cert-propagation watch (single read-only pass): `curl -sSk --resolve {gamdom90471,gamdom90472,gamdom90473}.com:443:151.101.67.72 https://$host/client-api` (expect 421; if 400 `Invalid request, only POST` → byte-diff vs gamdom80007.com/client-api to confirm live mirror), plus `dig +short gamdom9047{4,5}.com` + `gamdom800{04,08}.com` for the next numbered alias, and `curl -sS -m 12 https://gamdom-girisi.com/` grep `gamdom[0-9]+\.com` + `curl -sSk -m 12 https://gamdommirrors.com/status/gamdom-domains` grep for a new monitor. ≤6 lookups, ≤1rps.
[RISK] gamdom: 63 — 17 hostnames (15 live + 2 provisioned-but-edge-routed) confirmed sharing one POST-only `/client-api` identity/wallet proxy (verified origin gamdom4567.com) with per-host server-set session cookies; single-session theft crosses flagship + 4 regional TLDs + 9 mirrors + 3 rotating aliases (funds, withdrawal, KYC PII). No data touched; ATO remains human-gated behind one authenticated Set-Cookie replay test; all passive avenues exhausted; this cycle: +1 hostname (90473) and +2 aliases upgraded 000→421 toward live (90471/90472), but 3 candidates rejected out-of-scope on DNS. No confirmed HIGH vulnerability.
## 2026-09-06 18:12:48 UTC [target] (model bigpickle)
[HYP] Cross-host session replay across 18-host brand trust pool — single shared nginx/Starlette origin proven fleet-wide via byte-identical /health ETag + /client-api
class: AUTH
asset: gamdom.com/client-api (Pool A) + gamdom80003/80006/80007.com (Pool B) + gamdom4567.com + gamdom.eu/io/vip/win (Pool A) = 15 live hostnames + 4 provisioned (90471/90472/90473/90475)
confidence: 62
reasoning: /health 200 body `ok` + identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" re-confirmed on gamdom80003.com Pool B this cycle; /client-api 400 (26B) byte-identical across all pools; bundle proves auth transport = server-set same-origin cookie (zero bearer/localStorage); each host sets only host-only gd-lang; no ACAO on OPTIONS/Origin preflight blocks browser cross-origin replay; only unverified link = Domain/Path/SameSite of the authenticated session cookie.
evidence_needed: Set-Cookie attributes from an authenticated /client-api POST on one brand host, then reuse on a different brand host.
verify_steps: AUTH_HELPED — capture Set-Cookie (Domain/Path/SameSite) via authenticated POST on gamdom80006.com, replay verbatim on gamdom.eu + gamdom.com + gamdom80003.com, compare session acceptance.
impact: single session theft → account control across flagship + 4 regional TLDs + 9 mirrors + 4 provisioned aliases (funds, withdrawal, KYC PII); critical.
testability: AUTH_HELPED
[HYP] Rotating mirror aliases (90471 + 90472 + 90473 + 90475) transition from provisioned → live and join the shared backend
class: AUTH
asset: gamdom90471.com + gamdom90472.com + gamdom90473.com + gamdom90475.com/client-api (CNAME→gamdom4567.com)
confidence: 58
reasoning: All four CNAME→verified origin 4567 (same as live 80003/80006/80007); gamdom90471.com self-referenced 18× in official HomePage.js, SEO hub tags "Aktif"; this cycle all four remain HTTP 421 on all 4 Pool B edges — Fastly routes host but TLS cert not yet in host config; status-page JSON still shows no monitor for any; gamdom90475 is a NEW 5th alias this cycle (same provisioning pattern).
evidence_needed: any edge serving a valid host SNI cert for any of the four → /client-api renders byte-identical 400 (26B); monitor appears on status-page JSON.
verify_steps: PASSIVE — `curl --resolve {gamdom90471,gamdom90472,gamdom90473,gamdom90475}.com:443:{151.101.3,67,131,195}.72 https://$host/client-api` each cycle (expect 421 until cert propagates); byte-diff against gamdom80007.com/client-api; watch status-page JSON for new monitor entries.
impact: 4 more nodes in fleet ATO blast radius; inventory value.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action-name routing
class: AUTH
asset: gamdom.com/client-api catch-all proxy
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate; /client-api routes by action name; /health ETag confirms Starlette origin on both pools; catch-all 400s every GET subpath so action map formally unenumerable; privilege model untestable without authenticated POST.
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; no passive path remains.
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
[NEXT] PROBE: passive cert-propagation + new-alias watch (read-only, ≤1rps):
[RISK] gamdom: 64 — 19 hostnames (15 live + 4 provisioned-but-edge-routed) confirmed sharing one POST-only `/client-api` identity/wallet proxy (verified origin gamdom4567.com) with per-host server-set session cookies; fleet now includes gamdom90475.com (5th rotating alias, NEW this cycle); single-session theft crosses flagship + 4 regional TLDs + 9 mirrors + 4 rotating aliases (funds, withdrawal, KYC PII). No data touched; ATO remains human-gated behind one authenticated Set-Cookie replay test; +1 hostname this cycle (90475); no confirmed HIGH vulnerability.
## 2026-09-06 20:35:30 UTC [target] (model bigpickle)
[HYP] Cross-host session replay across 19-host gamdom4567 trust pool — single shared nginx/Starlette origin proven fleet-wide via byte-identical /health ETag + /client-api
class: AUTH
asset: gamdom.com/client-api (Pool A) + gamdom80003/80006/80007.com (Pool B) + gamdom4567.com + gamdom.eu/io/vip/win (Pool A) + provisioned aliases 90471/90472/90473/90475/90480/90482
confidence: 62
reasoning: /health ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" + /client-api 400 (26B) byte-identical across all pools previously; THIS cycle +2 new provisioned aliases (gamdom90480.com, gamdom90482.com) both CNAME→gamdom4567.com, Pool B, HTTP 421 on client-api (edge routes, host cert pending) — same provisioning milestone as 90471/72/73/75; bundle proves auth = server-set same-origin cookie; no ACAO on preflight closes browser replay; only unverified link = Domain/Path/SameSite of the authenticated session cookie.
evidence_needed: Set-Cookie attributes from an authenticated /client-api POST on one brand host, then reuse verbatim on a different brand host.
verify_steps: AUTH_HELPED — capture Set-Cookie via authenticated POST on gamdom80006.com, replay on gamdom.eu + gamdom.com + gamdom80003.com, compare acceptance.
impact: single session theft → account control across flagship + 4 regional TLDs + mirrors + 6 provisioned aliases (funds, withdrawal, KYC PII); critical.
testability: AUTH_HELPED
[HYP] Public mirror alias gamdom90488.com wired to Basic-auth-gated internal nginx on second brand TLD teamgamdom.com
class: MISCONFIG
asset: gamdom90488.com CNAME→shreeram-dynamic-test.teamgamdom.com (Fastly Pool A 151.101.x.52)
confidence: 55
reasoning: gamdom90488.com CNAME = shreeram-dynamic-test.teamgamdom.com (first-party Certainly cert, same CA as mirror fleet; Route53 NS; flags every probe with nginx HTTP Basic 401 realm="secret" including /health /api /admin /.git/HEAD /static /robots.txt — fixture-wide gate); DNS PUTS the public numbered alias onto the internal test backend; alias SNI currently only serves origin cert (TLS mismatch on gamdom90488.com SNI at Pool A edge) = provisioning-in-progress; name "shreeram-dynamic-test" = employee/engineering test instance. teamgamdom.com also hosts staging.teamgamdom.com serving first-party "Maintenance - Gamdom.com" page referencing gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com (public objects, ListObjects closed).
evidence_needed: any path on shreeram-dynamic-test/90488 served WITHOUT the Basic-auth gate, or the alias's own host cert going live so a visitor reachable; enumerating S3 bucket listing (currently denied).
verify_steps: PASSIVE — periodic `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/` (401 baseline; any 200/302 = gate change); `curl --resolve gamdom90488.com:443:151.101.67.52 https://gamdom90488.com/client-api` (421/TLS vs 401 = alias live); `curl -sS https://gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com/` (403 = still closed).
impact: if gate stripped/path-scoped → public exposure of internal test env (PII/test funds); if alias goes live → users phishable to internal host via brand alias; medium.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action-name routing
class: AUTH
asset: gamdom.com/client-api catch-all proxy
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate; /client-api routes by action name; /health ETag confirms Starlette origin on both pools; catch-all 400s every GET subpath so action map formally unenumerable; privilege model untestable without authenticated POST.
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; no passive path remains.
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
[NEXT] PROBE: passive teamgamdom + alias watch (read-only ≤1rps): `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/` + `https://staging.teamgamdom.com/` (baseline 401/503; any change = gate/service flip), `curl --resolve gamdom90480.com:443:151.101.67.72 https://gamdom90480.com/client-api` + gamdom90482 (expect 421 until host cert live), `curl -sS https://gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com/` (403 closed), and `dig gamdom90490.com CNAME` + `dig gamdom90{5..7}xx.com CNAME` narrow window for next numbered alias.
[RISK] gamdom: 65 — NEW second in-scope brand TLD teamgamdom.com discovered (Certainly cert = same CA as fleet, Route53, Fastly Pool A): hosts staging (live 503 Gamdom maintenance page, prod S3 bucket ref) + shreeram-dynamic-test (nginx Basic realm="secret" fixture-wide); public alias gamdom90488.com CNAME→the Basic-auth internal host; gamdom90580/90582 join as 5th/6th provisioned 9047x-family aliases → 21-host fleet across 2 TLD realms. No data touched; no confirmed HIGH vuln; top hypothesis (cross-host cookie replay, 62) still human-gated behind one authenticated Set-Cookie replay; +19 lookups, +~20 GETs all <1rps passive.
[PRIO] shreeram-dynamic-test.teamgamdom.com, 7.9, tech_exposure(nginx Basic auth internal test) + business_value(staff/test PII) + freshness(undiscovered)
[PRIO] teamgamdom.com-staging, 7.3, attack_surface(staging env) fresh + business_value(first-party prod-adjacent)
[PRIO] gamdom90480.com, 6.8, freshness(rotating alias) tech_exposure(client-api proxy) gate_ease(provisioned)
[PRIO] gamdom90488.com, 6.6, gate_ease(alias→internal) freshness
[PRIO] gamdom90482.com, 6.4, freshness(rotating alias) tech_exposure(client-api)
[HYP] Cross-host session replay across 19-host gamdom4567 trust pool — single shared nginx/Starlette origin proven fleet-wide via byte-identical /health ETag + /client-api
class: AUTH
asset: gamdom.com/client-api (Pool A) + gamdom80003/80006/80007.com (Pool B) + gamdom4567.com + gamdom.eu/io/vip/win (Pool A) + provisioned 90471/90472/90473/90475/90480/90482
confidence: 62
reasoning: /health weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" + /client-api 400 (26B) byte-identical across all pools; bundle proves auth = server-set same-origin cookie, zero bearer/localStorage; no ACAO on OPTIONS/Origin preflight blocks browser cross-origin replay; THIS cycle +2 provisioned aliases (90480, 90482, CNAME→4567, 421) join the pool; only unverified link = Domain/Path/SameSite of the authenticated session cookie.
evidence_needed: Set-Cookie attributes from an authenticated /client-api POST on one brand host, then reuse verbatim on a different brand host.
verify_steps: AUTH_HELPED — capture Set-Cookie via authenticated POST on gamdom80006.com, replay on gamdom.eu + gamdom.com + gamdom80003.com, compare session acceptance.
impact: single session theft → account control across flagship + 4 regional TLDs + mirrors + 6 rotating aliases (funds, withdrawal, KYC PII); critical.
testability: AUTH_HELPED
[HYP] Public mirror alias gamdom90488.com wired to Basic-auth-gated internal nginx on 2nd brand TLD teamgamdom.com
class: MISCONFIG
asset: gamdom90488.com CNAME→shreeram-dynamic-test.teamgamdom.com (Fastly Pool A 151.101.x.52)
confidence: 55
reasoning: gamdom90488.com CNAME = shreeram-dynamic-test.teamgamdom.com (first-party Certainly cert same CA as fleet; Route53; nginx HTTP Basic 401 realm="secret" on EVERY probed path — / /health /api /admin /.git/HEAD /static /robots.txt — fixture-wide gate); DNS pins the public numbered alias to the internal engineering test backend ("shreeram-dynamic-test"); alias SNI currently without own cert (TLS mismatch on gamdom90488.com at edge) = provisioning-in-progress; sibling staging.teamgamdom.com serves real "Maintenance - Gamdom.com" page.
evidence_needed: any shreeram-dynamic-test/90488 path served WITHOUT the Basic gate (200/302 vs 401 baseline), or alias host cert going live, or S3 listing opening.
verify_steps: PASSIVE — `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/` (401 baseline; any change = gate flip); `curl --resolve gamdom90488.com:443:151.101.67.52 https://gamdom90488.com/client-api` (TLS-NOMATCH vs 401 = alias live); `curl -sS https://gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com/` (403 = closed).
impact: gate-strip/path-scope error → internal test env (staff data) exposed via a public brand alias; if alias goes live → victim auths to internal host believing it real gamdom; medium.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action-name routing
class: AUTH
asset: gamdom.com/client-api catch-all proxy
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate; /client-api routes by action name; /health ETag confirms Starlette origin both pools; catch-all 400s every GET subpath so action map formally unenumerable; privilege model untestable without authenticated POST.
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; no passive path remains.
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
[NEXT] PROBE: passive 2nd-realm + rotation watch (read-only ≤1rps): `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/` and `https://staging.teamgamdom.com/` (baselines 401/503; any 200 = gate/service flip), `curl --resolve gamdom90480.com:443:151.101.67.72 https://gamdom90480.com/client-api` + gamdom90482.com (421 → 400 `Invalid request, only POST` = live mirror), `curl -sS https://gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com/` (403 closed), `dig gamdom90490.com CNAME` + `dig gamdom90483.com CNAME`.
[LEARN] ACCEPTED inventory @ teamgamdom.com: 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert same CA as mirror fleet) hosting staging.teamgamdom.com (live "Maintenance - Gamdom.com" 503 page) + shreeram-dynamic-test.teamgamdom.com (nginx Basic realm="secret" fixture-wide 401) — new origin realm distinct from gamdom4567 Starlette pool.
[LEARN] ACCEPTED recon @ gamdom90488.com: public numbered alias CNAME→shreeram-dynamic-test.teamgamdom.com pins a brand mirror onto the internal Basic-auth-gated nginx test backend; alias edge SNI not yet live.
[LEARN] ACCEPTED inventory @ gamdom90480.com/gamdom90482.com: both CNAME→gamdom4567.com, Pool B, client-api 421 — 5th/6th provisioned 9047x-family alias.
[LEARN] ACCEPTED recon @ gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com: staging page assets public-read (200), ListObjects/root denied (403) — closed hosting, inventory only.
[LEARN] REJECTED out-of-scope @ gamdom90474/90476/90477/90478/90479/90481/90483/90484/90485/90486.com: resolve to 192.64.119.x/162.255.119.x (non-Fastly) — not brand infrastructure.
[RISK] gamdom: 65 — fleet now 21 hostnames across 2 TLD realms (gamdom4567 Starlette trust pool + teamgamdom.com nginx/Go realm); NEW this cycle: whole brand TLD teamgamdom.com (staging live + Basic-auth internal test behind public 90488 alias) and 2 fresh provisioned aliases (90480, 90482). No data touched; no confirmed HIGH; top in-scope chain (cross-host cookie replay, 62) remains human-gated behind one authenticated Set-Cookie replay test; teamgamdom Basic-auth gate verified fixture-wide (closed control) — flagged as the single untested control, watch for gate/cert flip.
## 2026-09-06 22:23:26 UTC [target] (model bigpickle)
[NEXT] PROBE: alias-deployment watch (read-only ≤1rps): `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (TLS-NOMATCH→200/401 = alias cert live = internal host reachable via public brand name), `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 https://gamdom90488.com/client-api -o /dev/null -w "%{http_code}"` (421→400/401 = live), `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/` (gate watch), `curl -sS -o /dev/null -w "%{http_code}" https://gamdom80003.com/health` (pool guard).
[LEARN] ACCEPTED recon @ api/admin/auth/app/dev/test/sso/login/mail/cdn/core/support.teamgamdom.com: Fastly Pool A → Go `404 page not found` (19B) + `x-dbg-vcl2: bong_ke` — DNS-wildcard artifacts, only staging + shreeram real vhosts; teamgamdom breadth closed.
[LEARN] ACCEPTED recon @ teamgamdom.com wildcard: Fastly VCL/backend `bong_ke` fingerprint; third origin realm (Go) distinct from Starlette pool + nginx Basic.
[LEARN] ACCEPTED watch @ gamdom90480/90482/90488 + 9047x: all still 421/TLS-NOMATCH — no alias went live.
[LEARN] ACCEPTED watch @ staging.teamgamdom.com: 503 across all probed paths — maintenance gate fixture-wide.
[LEARN] ACCEPTED watch @ gamdommirrors.com status page: still exactly 7 monitors.
[RISK] gamdom: 65 — teamgamdom wildcard surface proven dead; all watched controls stable (no gate/cert flip); top chain (cookie replay, 62) AUTH_HELPED; single live passive thread = 90488/international-host MISCONFIG watch for cert propagation.
[HYP] Cross-host session replay across gamdom4567 trust pool — single shared nginx/Starlette origin fleet-wide
class: AUTH
asset: gamdom.com/client-api (Pool A) + gamdom80003/80006/80007 (Pool B) + gamdom4567.com + gamdom.eu/io/vip/win + provisioned aliases
confidence: 62
reasoning: /health ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" + /client-api 400 (26B) byte-identical across all pools; bundle proves auth = server-set same-origin cookie, no bearer/localStorage; no ACAO blocks browser cross-origin replay; this cycle 9047x + 90480/90482/90488 re-verified 421 provisioned (pool growing, none live yet).
evidence_needed: Set-Cookie attributes from one authenticated /client-api POST, then verbatim reuse on a different brand host.
verify_steps: AUTH_HELPED — capture Set-Cookie via authenticated POST on gamdom80006.com; replay on gamdom.eu + gamdom.com + gamdom80003.com; compare session acceptance.
impact: session theft → account control across flagship + 4 regional TLDs + mirrors + 6 aliases (funds, withdrawal, KYC PII); critical.
testability: AUTH_HELPED
[HYP] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on 2nd brand TLD teamgamdom.com
class: MISCONFIG
asset: gamdom90488.com CNAME→shreeram-dynamic-test.teamgamdom.com (Pool A)
confidence: 55
reasoning: gamdom90488.com CNAME = shreeram-dynamic-test.teamgamdom.com (first-party Certainly cert, Route53, nginx Basic realm="secret" fixture-wide on every probed path); DNS pins the public numbered alias onto the internal engineering test backend ("shreeram-dynamic-test"); alias SNI re-verified not live this cycle (TLS-NOMATCH at Pool A edge → 421 with -k) = provisioning-in-progress; sibling staging.teamgamdom.com is a separate Go/nginx vhost serving 503 maintenance everywhere.
evidence_needed: any shreeram-dynamic-test/90488 path served WITHOUT the Basic gate (200/302 vs 401), or alias host cert going live, or S3 listing opening.
verify_steps: PASSIVE — `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/` (baseline 401; change = gate flip); `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 https://gamdom90488.com/client-api -o /dev/null -w "%{http_code}"` (421→400/401 = alias live); `curl -sS https://gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com/` (403 = closed); first header-check https://gamdom90488.com/ without -k (200 vs TLS-NOMATCH = cert deployed).
impact: gate-strip/path-scope error → internal test env via public brand alias; alias live → users auth to internal host trusting the brand DNS; medium.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action-name routing
class: AUTH
asset: gamdom.com/client-api catch-all proxy
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate; /client-api routes by action name; Starlette origin both pools; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST.
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; no passive path remains.
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
[NEXT] PROBE: passive deployment-watch (read-only ≤1rps): `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (TLS-NOMATCH→200/401 = alias cert deployed = internal host reachable via public brand name), `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (421→400/401 = live), `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/` (401 gate; change = flip), `curl -sS -o /dev/null -w "%{http_code}" https://gamdom80003.com/health` (200 = pool stable).
[RISK] gamdom: 65 — 21-host fleet across 2 TLD realms / 3 origin types (Starlette gamdom4567 pool, Go `bong_ke` wildcard, nginx Basic shreeram). New this cycle: teamgamdom wildcard subdomain surface proven dead (Go 404 everywhere, no routes); no gate/cert flip on any watched control; staging confirmed locked across all paths. No data touched; top in-scope chain (cross-host cookie replay, 62) remains human-gated behind one authenticated Set-Cookie replay; MISCONFIG watch on 90488→internal-host (55) is the only live passive thread — priority is SNI/cert propagation.
## 2026-09-07 00:06:11 UTC [target] (model bigpickle)
[HYP] Cross-host session replay across gamdom4567 trust pool — single shared nginx/Starlette origin fleet-wide
class: AUTH
asset: gamdom.com/client-api (Pool A) + gamdom80003/80006/80007 (Pool B) + gamdom4567.com + 4 regional TLDs + 9047x/9048x provisioned aliases
confidence: 62
reasoning: /health weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" + /client-api 400 (26B) byte-identical across both pools; bundle proves auth = server-set same-origin cookie (no bearer/localStorage); no ACAO blocks browser cross-origin replay; this cycle re-verified 90473/90475 join the provisioning pool at 421 (pool growing, none live).
evidence_needed: Set-Cookie attributes from one authenticated /client-api POST, then verbatim reuse on a different brand host.
verify_steps: AUTH_HELPED — capture Set-Cookie via authenticated POST on gamdom80006.com; replay on gamdom.eu + gamdom.com + gamdom80003.com; compare session acceptance.
impact: session theft → account/wallet control across flagship + regional TLDs + mirrors + aliases (funds, withdrawal, KYC PII); critical.
testability: AUTH_HELPED
[HYP] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on 2nd brand TLD teamgamdom.com
class: MISCONFIG
asset: gamdom90488.com CNAME→shreeram-dynamic-test.teamgamdom.com (Pool A)
confidence: 55
reasoning: gamdom90488.com CNAME = shreeram-dynamic-test.teamgamdom.com (first-party Certainly cert, Route53; nginx Basic realm="secret" fixture-wide on every probed path); alias edge SNI re-verified still not live this cycle (cert check 000, --resolve /client-api 421) = provisioning-in-progress; no 9047x alias went live this cycle either (90473/90475 also 421).
evidence_needed: any shreeram-dynamic-test/90488 path served WITHOUT the Basic gate (200/302 vs 401), or alias host cert going live (200 vs TLS-NOMATCH), or S3 listing opening.
verify_steps: PASSIVE — `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/` (401 baseline; change = gate flip); `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (421→400/401 = alias live); `curl -sS -o /dev/null -w "%{http_code}" https://gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com/` (403 = closed).
impact: gate-strip/path-scope error → internal test env via public brand alias; alias live → users auth to internal host trusting brand DNS; medium.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action-name routing
class: AUTH
asset: gamdom.com/client-api catch-all proxy
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate; /client-api routes by action name; Starlette origin both pools; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST.
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; no passive path remains.
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
[RISK] gamdom: 65 — fleet still 3 realms (gamdom4567 Starlette pool incl. +2 new provisioned aliases 90473/90475; teamgamdom Go/nginx realm; S3 closed). No gate/cert/S3 flip on any watched control and no alias went live; all new aliases remain 421-provisioned (no new exploitable surface). Top in-scope chain (cross-host cookie replay, 62) unchanged and human-gated; single live passive thread remains the 90488→internal-host SNI/gate watch (55). No data touched.
## 2026-09-07 04:51:14 UTC [target] (model bigpickle)
[PRIO] gamdom.com/client-api (13 live + 10 provisioned hosts),6.4,attack_surface+business_value
[PRIO] shreeram-dynamic-test.teamgamdom.com,5.3,tech_exposure+cloud_surface
[PRIO] gamdom90488.com alias,3.9,gate_ease+freshness
[HYP] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on 2nd brand TLD teamgamdom.com
class: MISCONFIG
asset: gamdom90488.com CNAME→shreeram-dynamic-test.teamgamdom.com (Pool A)
confidence: 52
reasoning: gamdom90488.com CNAME = shreeram-dynamic-test.teamgamdom.com (first-party Certainly cert, Route53; nginx Basic realm="secret"). This cycle gate re-verified fixture-wide: / .git/config /env /server-status /status /_metrics /actuator all 401 (172B) — no gate gap. gamdom90488.com root still fails cert verify (000), `-k --resolve` /client-api 421 = alias still provisioned-not-live; SEO hub still lists only 90471 (17×). Remaining risk is purely when alias cert goes live, not gate bypass.
evidence_needed: alias host cert going live (200 vs TLS-NOMATCH), or any shreeram path served 200/302 vs 401, or S3 listing opening.
verify_steps: PASSIVE — `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (TLS-NOMATCH→200 = cert live); `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (421→400/401 = live); spot-check shreeram paths for 401 only; S3 root 403.
impact: alias live → users auth to internal host trusting brand DNS; gate-scope error → internal test env via public brand name; medium.
testability: PASSIVE
[HYP] Cross-host session replay across gamdom4567 trust pool — single shared nginx/Starlette origin fleet-wide
class: AUTH
asset: gamdom.com/client-api (Pool A) + gamdom80003/80006/80007 (Pool B) + gamdom4567.com + 4 regional TLDs
confidence: 62
reasoning: /health weak-ETag + /client-api 400 (26B) byte-identical across both pools (re-verified this cycle via 80003 ETag); bundle proves auth = server-set same-origin cookie (no bearer/localStorage); no ACAO blocks browser cross-origin replay. Fleet stable this cycle (90480/90488 remain 421-provisioned).
evidence_needed: Set-Cookie attributes from one authenticated /client-api POST, then verbatim reuse on a different brand host.
verify_steps: AUTH_HELPED — capture Set-Cookie via authenticated POST on gamdom80006.com; replay on gamdom.eu + gamdom.com + gamdom80003.com; compare session acceptance.
impact: session theft → account/wallet control across flagship + regional TLDs + mirrors (funds, withdrawal, KYC PII); critical.
testability: AUTH_HELPED
[HYP] Client-asserted privilege in /client-api action-name routing
class: AUTH
asset: gamdom.com/client-api catch-all proxy
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate; /client-api routes by action name; Starlette origin both pools; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST. No new passive evidence this cycle.
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; no passive path remains.
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
[PARKED] socket.io/admin surface @ gamdommirrors.com: GET-only handshake issues SID, no admin surface without POST RPC — parked; consistent.
[PARKED] subdomain takeover @ click.gamdom.com: resolved to SymplifyMail stock nginx, not dangling — watchlist only, no verify path.
[FINAL] survivors ranked: 1) [62] cross-host cookie replay (AUTH_HELPED, human-gated), 2) [52] 90488→internal-host alias cert watch (only live passive thread), 3) [45] client-asserted privilege (AUTH_HELPED).
[NEXT] PROBE: passive deployment-watch (read-only ≤1rps): `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (TLS-NOMATCH→200/401 = alias cert deployed), `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (421→400/401 = live), `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/env` (401 baseline; change = gate flip), `curl -sS -o /dev/null -w "%{http_code}" https://gamdom80003.com/health` (200 ETag-identical = pool stable), and re-grep gamdom-girisi.com for any new numbered alias.
[LEARN] ACCEPTED recon @ shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate fixture-wide — /.git/config /env /server-status /status /_metrics /actuator all 401 (172B) identical; no gate gap or debug leak on internal test backend; watch reduced to cert-propagation-only.
[LEARN] ACCEPTED watch @ gamdom-girisi.com: SEO hub unchanged, still 17× gamdom90471.com only, no new alias advertised; provisioned fleet (90480/90482/90488/90473/90475) still unpublished → provisioning-in-progress confirmed for another cycle.
[RISK] gamdom: 65 — 21-host fleet across 2 TLD realms / 3 origin types fully stable this cycle: no alias cert deployed (90488/90480 still TLS-NOMATCH/421), Starlette pool healthy with unchanged shared weak-ETag, teamgamdom Backend gate solid across 11 paths, status page and SEO hub unchanged. Top chain (cross-host cookie replay, 62) remains human-gated behind one authenticated Set-Cookie replay; no new exploitable surface emerged; all probed hosts closed/stable. No data touched.
## 2026-09-07 09:57:04 UTC [target] (model bigpickle)
[HYP] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on 2nd brand TLD teamgamdom.com
class: MISCONFIG
asset: gamdom90488.com CNAME→shreeram-dynamic-test.teamgamdom.com (Pool A)
confidence: 52
reasoning: gamdom90488.com CNAME = shreeram-dynamic-test.teamgamdom.com (first-party Certainly cert, Route53). This cycle gate re-verified fixture-wide: /env /.git/config /server-status /status /_metrics /actuator all 401 (unchanged), no gap. Alias still fails cert verify (000), -k --resolve /client-api 421 = still provisioned-not-live; SEO hub still 17× 90471 only.
evidence_needed: alias host cert going live (200 vs TLS-NOMATCH), or any shreeram path 200/302 vs 401, or S3 listing opening.
verify_steps: PASSIVE — `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (TLS-NOMATCH→200 = cert live); `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (421→400/401 = live); spot-check shreeram paths for 401.
impact: alias live → users auth to internal host trusting brand DNS; gate-scope error → internal test env via public brand name; medium.
testability: PASSIVE
[HYP] Cross-host session replay across gamdom4567 trust pool — single shared nginx/Starlette origin fleet-wide
class: AUTH
asset: gamdom.com/client-api (Pool A) + gamdom80003/80006/80007 (Pool B) + gamdom4567.com + 4 regional TLDs
confidence: 62
reasoning: /health weak-ETag + /client-api 400 (md5 7e3a161d) byte-identical across both pools (re-verified this cycle); bundle proves auth = server-set same-origin cookie; no ACAO blocks browser cross-origin replay. Fleet stable (90472/90488/90473/90475 all 421).
evidence_needed: Set-Cookie from one authenticated /client-api POST, verbatim reuse on different brand host.
verify_steps: AUTH_HELPED — capture Set-Cookie via authenticated POST on gamdom80006.com; replay on gamdom.eu + gamdom.com + gamdom80003.com; compare session acceptance.
impact: session theft → account/wallet control across flagship + regional TLDs + mirrors (funds, withdrawal, KYC PII); critical.
testability: AUTH_HELPED
[HYP] Client-asserted privilege in /client-api action-name routing
class: AUTH
asset: gamdom.com/client-api catch-all proxy
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate; /client-api routes by action name; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST. No new passive evidence.
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; no passive path remains.
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
[NEXT] PROBE: passive deployment-watch (read-only ≤1rps): `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (TLS-NOMATCH→200/401 = alias cert deployed), `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (421→400/401 = live), `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/env` (401 baseline; change = gate flip), `curl -sS -o /dev/null -w "%{http_code}" https://gamdom80003.com/health` (200 ETag-identical = pool stable), re-grep gamdom-girisi.com for new numbered alias.
[LEARN] ACCEPTED watch @ 90472/90488/90473/90475 aliases: all still 421/TLS-NOMATCH — no alias went live; provisioning-in-progress continues.
[LEARN] ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com + teamgamdom Go realm: gate holds 401 fixture-wide (7 paths), api.teamgamdom.com 404 default — no new surface.
[LEARN] ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors, SEO hub still 17× 90471 only.
[LEARN] ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable.
[RISK] gamdom: 65 — 21-host fleet across 2 TLD realms / 3 origin types fully stable this cycle: no alias cert deployed (90488/90472/90473/90475 still 421/TLS-NOMATCH), Starlette pool healthy with unchanged shared weak-ETag, teamgamdom Backend gate solid across 7 paths + Go realm 404 default, status page and SEO hub unchanged. Top chain (cross-host cookie replay, 62) remains human-gated behind one authenticated Set-Cookie replay; no new exploitable surface emerged; all probed hosts closed/stable. No data touched.
## 2026-09-07 15:32:00 UTC [target] (model bigpickle)
[PRIO] gamdom4567.com trust pool, 7.65, AUTH+business_value
[PRIO] gamdom90488.com alias, 6.05, MISCONFIG+gate_ease
[PRIO] dashboard.gamdom.com, 4.55, AUTH+admin_surface
[HYP] Cross-host session replay across gamdom4567 trust pool — single shared nginx/Starlette origin fleet-wide
class: AUTH
asset: gamdom.com/client-api (Pool A) + gamdom80003/80006/80007 (Pool B) + gamdom4567.com + 4 regional TLDs
confidence: 62
reasoning: /health weak-ETag + /client-api 400 (26B) byte-identical across both pools; bundle proves auth = server-set same-origin cookie (no bearer/localStorage); no ACAO blocks browser cross-origin replay; fleet stable this cycle.
evidence_needed: Set-Cookie attributes from one authenticated /client-api POST, then verbatim reuse on a different brand host.
verify_steps: AUTH_HELPED — capture Set-Cookie via authenticated POST on gamdom80006.com; replay on gamdom.eu + gamdom.com + gamdom80003.com; compare session acceptance.
impact: session theft → account/wallet control across flagship + regional TLDs + mirrors (funds, withdrawal, KYC PII); critical.
testability: AUTH_HELPED
[HYP] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on 2nd brand TLD teamgamdom.com
class: MISCONFIG
asset: gamdom90488.com CNAME→shreeram-dynamic-test.teamgamdom.com
confidence: 52
reasoning: CNAME confirmed, gate holds fixture-wide 401 across 7 paths, alias still TLS-NOMATCH (cert not deployed), SEO hub still 17× 90471 only — provisioned-but-not-live unchanged.
evidence_needed: alias host cert going live (200 vs TLS-NOMATCH), or any shreeram path 200/302 vs 401.
verify_steps: PASSIVE — `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (TLS-NOMATCH→200 = cert live); `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (421→400/401 = live).
impact: alias live → users auth to internal host trusting brand DNS; gate-scope error → internal test env via public brand name; medium.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action-name routing
class: AUTH
asset: gamdom.com/client-api catch-all proxy
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate; /client-api routes by action name; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST. No new passive evidence.
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; no passive path remains.
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
[PARKED] socket.io/admin surface @ gamdommirrors.com: GET-only EIO=4 polling handshake issues SID with websocket upgrade; no admin surface reachable without POST RPC — parked; consistent.
[PARKED] subdomain takeover @ click.gamdom.com: resolved to SymplifyMail stock nginx, not dangling — watchlist only, no verify path.
[FINAL] survivors ranked:
[NEXT] PROBE: passive deployment-watch (read-only ≤1rps): `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (TLS-NOMATCH→200/401 = alias cert deployed), `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (421→400/401 = live), `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/env` (401 baseline; change = gate flip), `curl -sS -o /dev/null -w "%{http_code}" https://gamdom80003.com/health` (200 ETag-identical = pool stable), and re-grep gamdom-girisi.com for any new numbered alias.
[LEARN] ACCEPTED watch @ gamdom90488.com + shreeram gate: both unchanged — alias cert not deployed, gate holds 401 fixture-wide. Provisioning-in-progress continues for another cycle.
[LEARN] ACCEPTED watch @ gamdom-girisi.com: SEO hub unchanged, still 17× 90471 only; no new alias advertised, provisioned fleet (90480/90482/90488/90473/90475) still unpublished.
[LEARN] ACCEPTED watch @ Starlette pool: /client-api 400 + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable.
[LEARN] ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors.
[RISK] gamdom: 65 — 21-host fleet across 2 TLD realms / 3 origin types fully stable this cycle: no alias cert deployed (90488 still TLS-NOMATCH/421), Starlette pool healthy with unchanged shared weak-ETag, teamgamdom Basic gate solid, status page + SEO hub unchanged. Top chain (cross-host cookie replay, 62) remains human-gated behind one authenticated Set-Cookie replay; no new exploitable surface emerged; all probed hosts closed/stable. No data touched.
## 2026-09-07 19:24:55 UTC [target] (model bigpickle)
[HYP] Cross-host session replay across gamdom4567 trust pool — single shared nginx/Starlette origin fleet-wide
class: AUTH
asset: gamdom.com/client-api (Pool A) + gamdom80003/80006/80007 (Pool B) + gamdom4567.com + 4 regional TLDs
confidence: 62
reasoning: /health weak-ETag + /client-api 400 (md5 7e3a161d) byte-identical across both pools this cycle; bundle proves auth = server-set same-origin cookie (no bearer/localStorage); no ACAO blocks browser cross-origin replay; no new passive evidence.
evidence_needed: Set-Cookie attributes from one authenticated /client-api POST, then verbatim reuse on a different brand host.
verify_steps: AUTH_HELPED — capture Set-Cookie via authenticated POST on gamdom80006.com; replay on gamdom.eu + gamdom.com + gamdom80003.com; compare session acceptance.
impact: session theft → account/wallet control across flagship + regional TLDs + mirrors (funds, withdrawal, KYC PII); critical.
testability: AUTH_HELPED
[HYP] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on 2nd brand TLD teamgamdom.com
class: MISCONFIG
asset: gamdom90488.com CNAME→shreeram-dynamic-test.teamgamdom.com
confidence: 52
reasoning: CNAME confirmed; gate holds 401 fixture-wide; alias still 421/000 (cert not deployed); SEO hub still 17× 90471 only — provisioned-but-not-live unchanged for 3rd cycle.
evidence_needed: alias host cert going live (200 vs 421/000), or any shreeram path 200/302 vs 401.
verify_steps: PASSIVE — `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/`; `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api`.
impact: alias live → users auth to internal host trusting brand DNS; gate-scope error → internal test env via public brand name; medium.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action-name routing
class: AUTH
asset: gamdom.com/client-api catch-all proxy
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate; /client-api routes by action name; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST. No new passive evidence.
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; no passive path remains.
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
## 2026-09-07 22:17:21 UTC [target] (model bigpickle)
[PRIO] gamdom90488.com,6.1,gate_ease+freshness (highest-movement vector; CNAME pinned to internal Basic gate; only cert deployment away from live test)
[PRIO] gamdom.com/client-api,5.8,business_value+gate_ease (identity/wallet proxy, 400-catchall, staff action names unenumerable passively)
[PRIO] gamdom90472/90473/90475,5.4,attack_surface+freshness (provisioned-not-live family; any one flipping live expands trust pool)
[HYP] Cross-host session replay across gamdom4567 trust pool — single shared nginx/Starlette origin fleet-wide
class: AUTH
asset: gamdom.com/client-api (Pool A) + gamdom80003/80006/80007 (Pool B) + gamdom4567.com + 4 regional TLDs
confidence: 62
reasoning: /health weak-ETag + /client-api 400 (md5 7e3a161d) byte-identical across both pools this cycle; bundle proves auth = server-set same-origin cookie (no bearer/localStorage); no ACAO blocks browser cross-origin replay; no new passive evidence.
evidence_needed: Set-Cookie attributes from one authenticated /client-api POST, then verbatim reuse on a different brand host.
verify_steps: AUTH_HELPED — capture Set-Cookie via authenticated POST on gamdom80006.com; replay on gamdom.eu + gamdom.com + gamdom80003.com; compare session acceptance.
impact: session theft → account/wallet control across flagship + regional TLDs + mirrors (funds, withdrawal, KYC PII); critical.
testability: AUTH_HELPED
[HYP] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on 2nd brand TLD teamgamdom.com
class: MISCONFIG
asset: gamdom90488.com CNAME→shreeram-dynamic-test.teamgamdom.com
confidence: 53
reasoning: CNAME confirmed; gate holds 401 fixture-wide; alias still 000/421 (cert not deployed 4th cycle); SEO hub still 17× 90471 only — provisioned-but-not-live unchanged.
evidence_needed: alias host cert going live (200 vs 421/000), or any shreeram path 200/302 vs 401.
verify_steps: PASSIVE — `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/`; `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api`.
impact: alias live → users auth to internal host trusting brand DNS; gate-scope error → internal test env via public brand name; medium.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action-name routing
class: AUTH
asset: gamdom.com/client-api catch-all proxy
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate; /client-api routes by action name; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST. No new passive evidence.
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; no passive path remains.
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
[PARKED] socket.io/admin surface @ gamdommirrors.com: GET-only EIO=4 SID handshake, no POST-RPC admin surface; consistent.
[PARKED] subdomain takeover @ click.gamdom.com: SymplifyMail stock nginx, not dangling; watchlist only.
[FINAL] survivors ranked: (1) cross-host cookie replay [62], (2) 90488 live-alias misconfig [53], (3) staff action-name privilege [45].
[NEXT] PROBE: passive deployment-watch (read-only ≤1rps): `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (000→200/401 = alias cert deployed), `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (421→400/401 = live), `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/env` (401 baseline; change = gate flip), `curl -sS -o /dev/null -w "%{http_code}" https://gamdom80003.com/health` (200 ETag-identical = pool stable), and re-grep gamdom-girisi.com for any new numbered alias.
[LEARN] ACCEPTED watch @ gamdom90488.com + shreeram gate: both unchanged — alias cert not deployed (000/421), gate holds 401 fixture-wide; provisioning-in-progress 4th cycle.
[LEARN] ACCEPTED watch @ gamdom-girisi.com: SEO hub unchanged, still 17× 90471 only; provisioned fleet (90472/90473/90475/90480/90482/90488) still unpublished.
[LEARN] ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical Pool A + Pool B — shared origin confirmed stable.
[LEARN] ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors (com/eu/io/vip/win/80006/80007).
[RISK] gamdom: 65 — 21-host fleet across 2 TLD realms / 3 origin types fully stable 4th cycle: no alias cert deployed (90488 still 000/421, 90472/90473/90475 TLS-NOMATCH), Starlette pool healthy with unchanged shared weak-ETag, teamgamdom Basic gate solid, status page + SEO hub unchanged. Top chain (cross-host cookie replay, 62) remains human-gated behind one authenticated Set-Cookie replay; no new exploitable surface emerged; all probed hosts closed/stable. No data touched.
## 2026-09-08 00:28:25 UTC [target] (model bigpickle)
## 2026-09-08 05:16:55 UTC [target] (model bigpickle)
[NEW] gamdom90488.com TLS-NOMATCH persists (cert not deployed); forced-resolve to Pool A IP returns 421 on /client-api  
[NEW] gamdom90472.com TLS-NOMATCH persists (cert not deployed); DNS resolves to Pool B but all edges HTTP 000  
[NEW] gamdom90480.com / gamdom90482.com TLS-NOMATCH persists; forced-resolve to Pool B IP returns 421 on /client-api  
[NEW] gamdom80003.com confirmed live (9th mirror): /health 200 weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical; /client-api 400 byte-identical  
[CHANGED] gamdommirrors.com/status/gamdom-domains API still exactly 7 monitors (com/eu/io/vip/win/80006/80007) — provisioned fleet (90471/90472/80003/80004/90480/90482/90488) still off monitor list  
[CHANGED] gamdom-girisi.com SEO hub unchanged — only gamdom90471.com referenced (17×)  
[CHANGED] shreeram-dynamic-test.teamgamdom.com Basic realm="secret" gate holds 401 fixture-wide (7 paths tested)  
[CHANGED] Starlette pool signature stable: /client-api 400 body md5 7e3a161d + /health weak-ETag byte-identical across Pool A (gamdom.com) + Pool B (80006/80007/80003) — shared origin confirmed  
[CHANGED] teamgamdom.com Go realm (api.teamgamdom.com) returns 404 default; VCL fingerprint `bong_ke` confirmed distinct from Starlette pool
[PRIO] gamdom80006.com/client-api,8.5,attack_surface=10,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=9  
[PRIO] gamdom.com/client-api,8.2,attack_surface=9,business_value=10,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=8  
[PRIO] gamdom4567.com/client-api,8.0,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=5,cloud_surface=8,freshness=9  
[PRIO] gamdom90488.com,5.5,attack_surface=6,business_value=6,tech_exposure=7,gate_ease=3,cloud_surface=6,freshness=7  
[PRIO] shreeram-dynamic-test.teamgamdom.com,4.0,attack_surface=5,business_value=4,tech_exposure=6,gate_ease=2,cloud_surface=5,freshness=6  
[PRIO] gamdom90471.com,6.8,attack_surface=7,business_value=7,tech_exposure=7,gate_ease=5,cloud_surface=7,freshness=8  
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO  
class: AUTH  
asset: gamdom80006.com/client-api  
confidence: 62  
reasoning: Single shared backend (gamdom4567.com) proven via byte-identical /client-api headers + weak-ETag on /health across 9 live Pool B mirrors; auth transport confirmed as server-set same-origin cookie (credentials: same-origin, no Bearer/localStorage) from passive bundle analysis; cookie Domain attribute is host-only (no Domain=) per gd-lang cookie observation; if backend validates cookie host-agnostically, session from any mirror works on all others  
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80007/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope from auth flow Set-Cookie headers  
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80007 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80007/client-api and 90471/client-api  
impact: Cross-domain account takeover across all 17 mirror hostnames; medium-high  
testability: AUTH_HELPED  
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship  
class: AUTH  
asset: gamdom.com/client-api  
confidence: 57  
reasoning: gamdom.com/io/eu/vip/win/client-api returns byte-identical GET 400 body as Pool B mirrors; shared origin backend signature spans entire 17-host brand; if session cookie Domain attribute is .gamdom.com or host-agnostic at backend, flagship domain + 4 regional TLDs join replay surface  
evidence_needed: Set-Cookie Domain attribute from auth flow on gamdom.com vs mirrors; whether cookie from gamdom.com accepted by gamdom4567.com/client-api  
verify_steps: (passive) compare Set-Cookie headers on auth endpoints across gamdom.com (Pool A) and gamdom80006.com (Pool B); (AUTH_HELPED) replay flagship session cookie against Pool B /client-api  
impact: Cross-pool ATO across flagship + 4 regional TLDs + all 9 mirrors + 3 aliases; high  
testability: AUTH_HELPED  
[HYP] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on 2nd brand TLD teamgamdom.com  
class: MISCONFIG  
asset: gamdom90488.com  
confidence: 52  
reasoning: gamdom90488.com CNAME = shreeram-dynamic-test.teamgamdom.com (first-party Certainly cert, Route53, Fastly Pool A); nginx Basic realm="secret" gate fixture-wide 401 across 7 paths (/, /.git/config, /env, /server-status, /status, /_metrics, /actuator); alias edge SNI not yet live (TLS-NOMATCH/000); SEO hub still lists only gamdom90471.com (17×); risk purely when alias cert goes live, not gate bypass  
evidence_needed: alias host cert going live (TLS-NOMATCH → 200/401), or any shreeram path served 200/302 vs 401, or S3 listing opening  
verify_steps: PASSIVE — `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (TLS-NOMATCH→200 = cert live); `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (421→400/401 = live); spot-check shreeram paths for 401 only; S3 root 403  
impact: alias live → users auth to internal host trusting brand DNS; gate-scope error → internal test env via public brand name; medium  
testability: PASSIVE  
[PARKED] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on 2nd brand TLD teamgamdom.com: confidence 52 but no live cert deployment after 4+ cycles; purely speculative until TLS-NOMATCH resolves; no active bypass vector  
[FINAL] 1) Cross-mirror auth cookie replay via shared /client-api origin yields ATO (62)  
[FINAL] 2) Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (57)
[NEXT] PROBE: passive auth flow cookie inspection — `curl -sS -I https://gamdom80006.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80007.com and gamdom.com; ≤1 rps, HEAD only
[LEARN] ACCEPTED watch @ 90472/90488/90473/90475 aliases: all still 421/TLS-NOMATCH — no alias went live; provisioning-in-progress continues  
[LEARN] ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com + teamgamdom Go realm: gate holds 401 fixture-wide (7 paths), api.teamgamdom.com 404 default — no new surface  
[LEARN] ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors, SEO hub still 17× 90471 only  
[LEARN] ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable  
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing  
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)  
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing  
[RISK] gamdom: 70 reason: Confirmed 17-host fleet (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
evidence_needed: alias host cert going live (200 vs 421/000), or any shreeram path 200/302 vs 401.
verify_steps: PASSIVE — `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/`; `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api`.
impact: alias live → users auth to internal host trusting brand DNS; gate-scope error → internal test env via public brand name; medium.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action-name routing
class: AUTH
asset: gamdom.com/client-api catch-all proxy
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate; /client-api routes by action name; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST. No new passive evidence.
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; no passive path remains.
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
[PARKED] socket.io/admin surface @ gamdommirrors.com: GET-only EIO=4 SID handshake, no POST-RPC admin surface; consistent.
[PARKED] subdomain takeover @ click.gamdom.com: SymplifyMail stock nginx, not dangling; watchlist only.
[FINAL] survivors ranked: (1) cross-host cookie replay [62], (2) 90488 live-alias misconfig [53], (3) staff action-name privilege [45].
[NEXT] PROBE: passive deployment-watch (read-only ≤1rps): `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (000→200/401 = alias cert deployed), `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (421→400/401 = live), `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/env` (401 baseline; change = gate flip), `curl -sS -o /dev/null -w "%{http_code}" https://gamdom80003.com/health` (200 ETag-identical = pool stable), and re-grep gamdom-girisi.com for any new numbered alias.
[LEARN] ACCEPTED watch @ gamdom90488.com + shreeram gate: both unchanged — alias cert not deployed (000/421), gate holds 401 fixture-wide; provisioning-in-progress 4th cycle.
[LEARN] ACCEPTED watch @ gamdom-girisi.com: SEO hub unchanged, still 17× 90471 only; provisioned fleet (90472/90473/90475/90480/90482/90488) still unpublished.
[LEARN] ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical Pool A + Pool B — shared origin confirmed stable.
[LEARN] ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors (com/eu/io/vip/win/80006/80007).
[RISK] gamdom: 65 — 21-host fleet across 2 TLD realms / 3 origin types fully stable 4th cycle: no alias cert deployed (90488 still 000/421, 90472/90473/90475 TLS-NOMATCH), Starlette pool healthy with unchanged shared weak-ETag, teamgamdom Basic gate solid, status page + SEO hub unchanged. Top chain (cross-host cookie replay, 62) remains human-gated behind one authenticated Set-Cookie replay; no new exploitable surface emerged; all probed hosts closed/stable. No data touched.
[HYP] Cross-host AND cross-brand cookie replay on single identity/wallet origin
class: AUTH
asset: gamdom.com/client-api (shared gamdom4567 origin: gamdom.com/eu/io/one/vip/win, fatbets.com, 8000x mirrors, 4567)
confidence: 66
reasoning: Bundle-config mining + passive probes proved fatbets.com and gamdom.one serve byte-identical app bundle + `/client-api` 400 (md5 7e3a161d) + `/health` weak-ETag identical + `gd-lang` host-only cookie → one Starlette backend spans 2 brands / 19+ hosts; auth transport = server-set same-origin cookie (no bearer/localStorage). Fresh hosts' `/auth/login`+`/graphql` → 404 with only host-only `gd-lang` (no Domain attr at pre-auth edge).
evidence_needed: one authenticated Set-Cookie (attrs) replayed verbatim on fatbets.com/gamdom.one/gamdom.eu/gamdom.com/80006 — backend acceptance = replay proven.
verify_steps: AUTH_HELPED — own test accounts: POST /client-api on gamdom80006.com, capture Set-Cookie, replay on fatbets.com + gamdom.one + gamdom.com, compare responses. Passive path exhausted pre-auth.
impact: wallet/identity session theft across two brands + 19 hostnames (funds, withdrawal, KYC); critical if backend validates host-agnostic.
testability: AUTH_HELPED
[HYP] Client-asserted privilege in /client-api action routing (dual staffRefillConfig shipped)
class: AUTH
asset: gamdom.com/client-api
confidence: 48
reasoning: bundle ships `staffRefillConfig` twice (30M + 75M coins/h) + moderator non-admin tip cap, calls `/client-api` via proxy keyed by action name `e.name→t+"/"+name`; catch-all 400s all GET subpaths → action map unenumerable; privilege model unverifiable without authenticated POST.
evidence_needed: authenticated POST of staff action names → upstream-differentiated response vs non-staff names.
verify_steps: AUTH_HELPED only — POST /client-api with bundle-mined staff names on normal vs staff-tier test accounts.
impact: staff coin/wallet refill or moderator-tip abuse; critical if privilege is client-asserted.
testability: AUTH_HELPED
[HYP] Provisioned alias 90488 still cert-gated (inactive misconfig)
class: MISCONFIG
asset: gamdom90488.com CNAME→shreeram-dynamic-test.teamgamdom.com
confidence: 52
reasoning: 5th cycle alias cert not deployed (root TLS-fail 000, forced-resolve 421), shreeram Basic realm="secret" gate 401 fixture-wide, SEO hub 17× 90471 only.
evidence_needed: alias cert live (200/401 vs 000/421) or gate-scope regression to 200/302.
verify_steps: PASSIVE — `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/`; `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 https://gamdom90488.com/client-api`.
impact: brand DNS → internal Basic-gated test backend if cert lands with gate-scope error; medium.
testability: PASSIVE
[PARKED] gamdom90452→90488 alias watch: cert absence 5th cycle; unchanged, speculative only vs gate.
[PARKED] socket.io/admin @ gamdommirrors.com + click.gamdom.com takeover: prior verdicts hold, no new evidence.
[PARKED] approx knowledge: staff-bundle names — deprioritized; only actionable via authenticated POST (HYP2).
[FINAL] survivors ranked: (1) cross-host/cross-brand cookie replay [66], (2) staff action-name privilege [48], (3) 90488 live-alias misconfig [52 watch].
[NEXT] PROBE: passive parity+deploy watch — `curl -sS -o /dev/null -w "%{http_code}" https://fatbets.com/health` (200 + unchanged weak-ETag = origin stable across brands), re-fetch `https://gamdom.one/` index and capture `client.<hash>.js` (hash flip = deploy → re-mine BRANDED_SUBDOMAINS + `gamdom.*` encrypted-config keys + staffRefillConfig for new brands/TLDs), re-check `https://gamdommirrors.com/status/gamdom-domains` for >7 monitors. ≤1 rps, GET only.
[LEARN] ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identical app/bundle + `/client-api` md5 7e3a161d + `/health` weak-ETag + host-only gd-lang → shared identity/wallet origin serves 2 brands / 19+ hostnames.
[LEARN] ACCEPTED recon @ fatbets.com + gamdom.one: `/auth/login` + `/graphql` → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth; replay remains backend-level.
[LEARN] ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; `trMirrorDomain=gamdom80004.com`.
[LEARN] ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress.
[LEARN] ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable.
[RISK] gamdom: 66 — two NEW live in-scope hostnames (fatbets.com, gamdom.one) verified this cycle prove a single identity/wallet origin now spans 2 consumer brands across 19+ hostnames, raising impact of the top chain (cross-host/brand cookie replay, 66) which remains gated behind one authenticated Set-Cookie replay; all 6 provisioned aliases still not live (5th cycle), shreeram/Go gates solid, bundle unchanged; only read-only GET probes run, no data touched.
## 2026-09-08 09:47:28 UTC [target] (model bigpickle)
[HYP] Live-alias deploy on 90488 (provisioning-in-progress gate-scope risk)
class: MISCONFIG
asset: gamdom90488.com CNAME→shreeram-dynamic-test.teamgamdom.com
confidence: 50
reasoning: 6th consecutive cycle alias cert not deployed (root TLS-fail 000, forced-resolve 421); shreeram Basic realm="secret" gate holds 401 fixture-wide; SEO hub 17× 90471 only. No change from 5th cycle.
evidence_needed: alias cert live (200/401 vs 000/421) or shreeram gate-scope regression to 200/302.
verify_steps: PASSIVE — `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/`; `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 https://gamdom90488.com/client-api`.
impact: brand DNS → internal Basic-gated nginx test backend if cert lands with gate-scope error; medium.
testability: PASSIVE
[HYP] Cross-brand cookie replay on single identity/wallet origin (fatbets.com + gamdom.one)
class: AUTH
asset: gamdom.com/client-api shared gamdom4567 origin (gamdom.com/eu/io/one/vip/win, fatbets.com, 8000x, 4567)
confidence: 66
reasoning: fatbets.com + gamdom.one serve byte-identical bundle `client.41b06529227c4b8b6a1d.js`, /health 200, /client-api 400, host-only gd-lang → one Starlette backend spans 2 brands / 19+ hosts; auth transport = server-set same-origin cookie (no bearer/localStorage). Pre-auth /auth/login+/graphql → 404 host-only gd-lang only.
evidence_needed: one authenticated Set-Cookie replayed verbatim on fatbets.com/gamdom.one/gamdom.com/emandom.eu/80006 — backend acceptance = replay proven.
verify_steps: AUTH_HELPED only — own test accounts: POST /client-api on gamdom80006.com, capture Set-Cookie, replay on fatbets.com + gamdom.one + gamdom.com, compare responses. Passive path exhausted pre-auth; bundle hash unchanged = no new passive surface.
impact: wallet/identity session theft across two brands + 19 hostnames (funds, withdrawal, KYC); critical if backend validates host-agnostic.
testability: AUTH_HELPED
[HYP] Client-asserted privilege in /client-api action routing (staffRefillConfig shipped)
class: AUTH
asset: gamdom.com/client-api
confidence: 48
reasoning: bundle ships dual staffRefillConfig (30M/75M coins/h) + moderator tip cap; /client-api proxies by action name `e.name→t+"/"+name`; catch-all 400s all GET subpaths → action map unenumerable. No new passive evidence (bundle hash unchanged).
evidence_needed: authenticated POST of staff action names → upstream-differentiated response vs non-staff names.
verify_steps: AUTH_HELPED only.
impact: staff coin/wallet refill or moderator-tip abuse; critical if privilege client-asserted.
testability: AUTH_HELPED
[NEXT] PROBE: passive deploy+parity watch — `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (000→200/401), `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90472.com/`, re-fetch `https://fatbets.com/` capture `client.<hash>.js` (hash flip = deploy → re-mine BRANDED_SUBDOMAINS + gamdom.* TLD keys + staffRefillConfig for new brands), re-check `https://gamdommirrors.com/status/gamdom-domains` for >7 monitors. ≤1 rps GET only.
[RISK] gamdom: 66 — 6th consecutive cycle with zero change: 6 provisioned aliases still not live, bundle hash byte-identical across 2 brands / 19+ hostnames (confirmed shared identity/wallet origin stable), shreeram/Go gates solid, status page + SEO hub unchanged. Top chain (cross-host/cross-brand cookie replay, 66) remains human-gated behind one authenticated Set-Cookie replay; no new exploitable surface emerged; all probed hosts closed/stable; only read-only GET probes run, no data touched.
## 2026-09-08 14:12:53 UTC [target] (model bigpickle)
[HYP] Live alias deployed on 90488 (provisioning gate-scope risk)
class: MISCONFIG
asset: gamdom90488.com CNAME→shreeram-dynamic-test.teamgamdom.com
confidence: 50
reasoning: 6th consecutive cycle alias cert not deployed (000/421); shreeram Basic realm="secret" gate 401 fixture-wide; SEO hub 17× 90471 only. Mirror rotation 80007→80008 proves fleet maintenance is active now, raising near-term deploy probability for pending aliases.
evidence_needed: alias cert live (200/401) or gate-scope regression
verify_steps: PASSIVE — `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/`; `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 https://gamdom90488.com/client-api`
impact: brand DNS → internal Basic-gated nginx backend if cert lands with gate-scope error; medium
testability: PASSIVE
[HYP] Cross-brand cookie replay on shared identity/wallet origin
class: AUTH
asset: gamdom.com/client-api shared origin (gamdom.com/eu/io/one/vip/win, fatbets.com, 80003/80006/80008, 4567)
confidence: 66
reasoning: fatbets.com + gamdom's 10 live mirrors serve byte-identical bundle + /client-api md5 7e3a161d + /health weak-ETag → one Starlette backend; auth transport = server-set same-origin cookie (no bearer/localStorage). 80008's rotation-in keeps the pool live but adds no pre-auth surface.
evidence_needed: one authenticated Set-Cookie replayed on another mirror hostname → backend acceptance
verify_steps: AUTH_HELPED only
impact: wallet/identity session theft across 2 brands + 19 hostnames; critical if host-agnostic
testability: AUTH_HELPED
[HYP] Client-asserted privilege in /client-api action routing (staffRefillConfig shipped)
class: AUTH
asset: gamdom.com/client-api
confidence: 48
reasoning: bundle ships dual staffRefillConfig (30M/75M) + moderator tip cap; catch-all 400s GET subpaths → action map unenumerable; bundle hash unchanged (no new config keys surfaced)
evidence_needed: authenticated POST of staff action names → differentiated upstream response
verify_steps: AUTH_HELPED only
impact: staff coin refill / tip abuse; critical if client-asserted
testability: AUTH_HELPED
[NEXT] PROBE: confirm 80001/80002 deliverability edge parity — `for e in 151.101.67.72 151.101.3.72 151.101.131.72 151.101.195.72; do curl -sSk -o /dev/null -w "%{http_code} " --resolve gamdom80001.com:443:$e https://gamdom80001.com/client-api; done` (all 421 = still provisioned; any 400/200 = new live mirror), re-check status page monitor set for >7.
[LEARN] ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); official status page monitor id:221 placed it as 7th listed domain REPLACING gamdom80007.com — mirror fleet rotation event, 10th live mirror / 20th hostname in trust pool.
[LEARN] ACCEPTED inventory @ gamdom80007.com: demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbered aliases by redirect, 80008 inherits its Pool B slot.
[LEARN] ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames, off the 7-monitor status page.
[RISK] gamdom: 68 — 6th cycle saw the FIRST live surface change in the mirror fleet: 80007 retire→redirect and 80008 (once out-of-scope) go live with byte-identical /client-api+health on the shared identity/wallet origin, plus two new provisioned aliases (80001/80002); this proves continuous fleet rotation around a single auth origin, keeping the top chain (cross-host cookie replay, 66) gated behind one authenticated Set-Cookie replay; all provisioned 9047x aliases + shreeram/Go gates still solid; only read-only GET probes run, no data touched.
## 2026-09-08 17:58:24 UTC [target] (model bigpickle)
[HYP] Cross-brand cookie replay on shared identity/wallet origin
class: AUTH
asset: gamdom.com/client-api shared gamdom4567 origin (gamdom.com/eu/io/one/vip/win, fatbets.com, 80003/80006/80008, 4567)
confidence: 66
reasoning: fatbets.com + gamdom.one serve byte-identical bundle + /client-api md5 7e3a161d + /health weak-ETag → one Starlette backend spans 2 brands / 20+ hostnames; auth transport = server-set same-origin cookie (no bearer/localStorage). 80008 ratifies the shared 7-monitor pool; adds no pre-auth surface.
evidence_needed: one authenticated Set-Cookie replayed verbatim on another mirror hostname → backend acceptance = replay proven.
verify_steps: AUTH_HELPED only — own test accounts: POST /client-api on gamdom80006.com, capture Set-Cookie, replay on fatbets.com + gamdom.one + gamdom.com, compare upstream responses.
impact: wallet/identity session theft across 2 brands + 20 hostnames (funds, withdrawal, KYC); critical if backend host-agnostic.
testability: AUTH_HELPED
[HYP] Provisioned alias goes live with Basic/edge-gate scope error (90488 → shreeram backend)
class: MISCONFIG
asset: gamdom90488.com CNAME→shreeram-dynamic-test.teamgamdom.com
confidence: 50
reasoning: 80007→80008 rotation proves active fleet maintenance; 6 provisioned aliases (90472/90473/90475/90480/90482/90488) still waiting cert. If a cert lands with gate-scope regression, brand DNS exposes internal Basic-gated nginx test backend.
evidence_needed: alias cert live (200/401) or gate-scope regression to 200/302.
verify_steps: PASSIVE — `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom90488.com/`; `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 https://gamdom90488.com/client-api`.
impact: brand DNS → internal Basic-gated nginx test backend if cert lands with gate-scope error; medium.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action routing (staffRefillConfig shipped)
class: AUTH
asset: gamdom.com/client-api
confidence: 48
reasoning: bundle ships dual staffRefillConfig (30M/75M coins/h) + moderator tip cap client-side; /client-api proxies by action name; catch-all 400s GET subpaths → action map unenumerable; bundle hash unchanged (no new config keys surfaced this cycle).
evidence_needed: authenticated POST of staff action names → differentiated upstream response vs non-staff names.
verify_steps: AUTH_HELPED only.
impact: staff coin/wallet refill or moderator-tip abuse; critical if privilege client-asserted.
testability: AUTH_HELPED
[NEXT] PROBE: deploy+parity watch on pending aliases + monitor-count confirm — `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (000/421→200/401 = live), `for e in 151.101.67.72 151.101.3.72 151.101.131.72 151.101.195.72; do curl -sSk -o /dev/null -w "%{http_code} " --resolve gamdom80001.com:443:$e https://gamdom80001.com/client-api; done` (any 400/200 = new live mirror), and re-`GET https://gamdommirrors.com/api/status-page/heartbeat/gamdom-domains` (monitor set >7 = another rotation).
[RISK] gamdom: 67 — The 80007→80008 monitor-set change ratifies an ALREADY-known rotation (no new exploitable surface); all 20+ fleet hostnames sit on one Starlette identity/wallet origin with a single authenticated Set-Cookie replay separating the top chain (cross-brand ATO, 66) from proof. 6 provisioned aliases still not live, gates (shreeram/Go) hold solid, bundle hash unchanged (no deploy). Only read-only GET probes run, no data touched; risk steady as fleet control-of-origin single point remains the core exposure.
## 2026-09-08 20:51:04 UTC [target] (model bigpickle)
[HYP] Provisioned alias cert lands with edge/backend gate-scope regression
class: MISCONFIG
asset: gamdom90488.com → shreeram-dynamic-test.teamgamdom.com (Basic realm="secret"); fleet 90471/90472/90473/90475/90480/90482 + 80001/80002
confidence: 52
reasoning: 80007→80008 rotation and 80001/80002 provisioned-host additions (now 421 on all edges) prove the mirror fleet is in active maintenance; 9 provisioned aliases await cert deployment; prior rotation landed 80008 byte-identical to the shared Starlette origin — if a Pending Alias cert lands on the shreeram Basic-gated nginx backend, or on gamdom4567 origin without proper pool binding, live brand DNS would expose an internal test/auth surface.
evidence_needed: a pending alias returns 200/400 (live) instead of 421, or a gate-scope regression on shreeram to non-401.
verify_steps: PASSIVE — `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (and 90471/90472/90473/90475/90480/90482/80001/80002); if live, `curl -sSk https://<alias>/client-api` and compare md5/ETag to 7e3a161d; `curl -sS https://shreeram-dynamic-test.teamgamdom.com/` for gate status.
impact: brand DNS resolves to an internal Basic-auth-gated nginx test/CI backend or misbound origin — unauthorized internal surface exposure; medium-high if gate-scope-error.
testability: PASSIVE
[HYP] Cross-brand cookie replay on shared identity/wallet origin
class: AUTH
asset: gamdom.com/client-api shared gamdom4567 Starlette origin (gamdom.com/eu/io/one/vip/win, fatbets.com, gamdom.one, mirrors 80003/80006/80008/4567)
confidence: 66
reasoning: fatbets.com + gamdom.one + 10 live Gamdom mirrors serve byte-identical bundle + /client-api md5 7e3a161d + /health weak-ETag → one Starlette identity/wallet backend across 2+ brands / 20 hostnames; auth transport = server-set same-origin cookie (no bearer/localStorage). 80008 ratified the 7-monitor pool but added no pre-auth surface.
evidence_needed: one authenticated Set-Cookie replayed verbatim on another mirror/brand hostname → backend acceptance = replay proven.
verify_steps: AUTH_HELPED only — own test accounts: POST /client-api on gamdom80006.com, capture Set-Cookie, replay on fatbets.com + gamdom.one + gamdom.com, compare upstream responses.
impact: wallet/identity session theft across 2 brands + 20 hostnames (funds, withdrawal, KYC); critical if backend host-agnostic.
testability: AUTH_HELPED
[HYP] Client-asserted privilege in /client-api action routing (staffRefillConfig shipped)
class: AUTH
asset: gamdom.com/client-api
confidence: 48
reasoning: bundle ships dual staffRefillConfig (30M/75M coins/h) + moderator tip cap client-side; /client-api proxies by action name; catch-all 400s GET subpaths → action map unenumerable; bundle hash unchanged (no new config keys surfaced).
evidence_needed: authenticated POST of staff action names → differentiated upstream response vs non-staff names.
verify_steps: AUTH_HELPED only.
impact: staff coin/wallet refill or moderator-tip abuse; critical if client-asserted.
testability: AUTH_HELPED
[NEXT] PROBE: redeploy-parity watch across all 9 pending aliases — `for a in 90471 90472 90473 90475 90480 90482 90488 80001 80002; do curl -sSk -o /dev/null -w "$a:%{http_code} " https://gamdom$a.com/client-api; done` (421→400/200 = newly live alias; verify md5 7e3a161d or gate-scope vs shreeram).
[LEARN] ACCEPTED watch @ 80001/80002 + 7 pending 9047x aliases: all 4 Pool B edges still 421/TLS-NOMATCH; no new alias went live; 80007 still 302→80008, 80008 live.
[RISK] gamdom: 67 — No new surface this cycle; 80008 rotation already absorbed (7 monitors stable: ids 2,3,5,6,13,217,221), all 9 provisioned aliases still 421 (6th cycle no deploy). 20+ hostnames share one Starlette identity/wallet origin; top chain (cross-brand cookie replay, 66) still gated behind one authenticated Set-Cookie replay, and the client-asserted staffRefill line (48) remains behind the same gate. Only read-only GET probes run; no data touched. Risk steady — single control-of-origin remains the core concentration.
## 2026-09-08 23:11:17 UTC [target] (model bigpickle)
[HYP] Cross-brand cookie replay on shared identity/wallet origin
class: AUTH
asset: gamdom.com/client-api shared gamdom4567 Starlette origin (com/eu/io/one/vip/win + fatbets.com + 80003/80006/80008/4567)
confidence: 66
reasoning: fatbets.com + gamdom.one + 10 live mirrors serve byte-identical bundle + /client-api md5 7e3a161d + /health weak-ETag → one identity/wallet backend across 2 brands/20 hostnames; auth transport is server-set same-origin cookie (bundle uses credentials same-origin, zero bearer/localStorage). Pre-auth observations unchanged: no Domain attr on gd-lang, browser cross-origin replay blocked by absent ACAO at edge (400). Flooring the chain on one authenticated Set-Cookie replay.
evidence_needed: authenticated Set-Cookie from one mirror replayed verbatim on another mirror/brand hostname → backend acceptance = replay proven.
verify_steps: AUTH_HELPED only — own test accounts: POST /client-api on gamdom80006.com, capture Set-Cookie, replay on fatbets.com + gamdom.one + gamdom.com, compare upstream responses.
impact: wallet/identity session theft across 2 brands + 20 hostnames (funds, withdrawal, KYC); critical if backend host-agnostic.
testability: AUTH_HELPED
[HYP] Provisioned alias cert lands with edge/backend gate-scope regression
class: MISCONFIG
asset: gamdom90488.com CNAME→shreeram-dynamic-test.teamgamdom.com; fleet 90471/90472/90473/90475/90480/90482/80001/80002
confidence: 52
reasoning: 80007→80008 rotation + 80001/80002 provisioning prove active fleet churn with certs landing out of monitor order; if the next cert lands on the shreeram Basic-gated backend (90488) or a Pool-B misbound vhost, brand DNS exposes an internal test/auth surface or a second origin realm.
evidence_needed: any pending alias returns 200/400 instead of 421, or shreeram gate-scope regression to non-401.
verify_steps: PASSIVE — `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (repeat for the other 8; 421→400/200 = live); if live, `curl -sSk https://<alias>/client-api | md5sum` compare to 7e3a161d; `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/`.
impact: brand DNS → internal Basic-auth-gated nginx test backend or misbound origin; medium-high if gate-scope-error.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action routing (staffRefillConfig shipped)
class: AUTH
asset: gamdom.com/client-api
confidence: 48
reasoning: flagship bundle ships dual staffRefillConfig (30M/75M coins/h) + moderator tip cap client-side; /client-api proxies by action name; catch-all 400 on GET subpaths → action map unenumerable; bundle hash unchanged (no new staff keys surfaced this cycle).
evidence_needed: authenticated POST of staff action names → differentiated upstream response vs non-staff names.
verify_steps: AUTH_HELPED only.
impact: staff coin/wallet refill or moderator-tip abuse; critical if privilege client-asserted.
testability: AUTH_HELPED
[NEXT] PROBE: deploy-parity watch (cycle 8) — `for a in 90471 90472 90473 90475 90480 90482 90488 80001 80002; do curl -sSk -o /dev/null -w "$a:%{http_code} " https://gamdom$a.com/client-api; done` (421→400/200 = newly live; confirm md5 7e3a161d or shreeram gate scope), then `curl -sS "https://gamdommirrors.com/api/status-page/gamdom-domains"` to re-confirm the 7-monitor set before/after any rotation.
[RISK] gamdom: 67 — No surface delta for the 7th cycle: 9 provisioned aliases stuck 421, 7-monitor set retired 80007→80008 already absorbed, Starlette pool byte-stable. Fleet churn evidence (80008 live, 80007 retired, 80001/80002 staged) keeps single-control-of-origin as the core concentration; 20+ hostnames across 2 brands funnel to one Starlette identity/wallet backend with the cross-brand cookie-replay chain (66) still gated behind one authenticated Set-Cookie replay I am not permitted to execute. Only read-only GET probes run at ≤1 rps; no data touched; risk steady.
## 2026-09-09 01:17:57 UTC [target] (model bigpickle)
[HYP] Cross-brand cookie replay on shared identity/wallet origin
class: AUTH
asset: gamdom.com/client-api shared gamdom4567 Starlette origin (com/eu/io/one/vip/win + fatbets.com + 80003/80006/80008/4567)
confidence: 66
reasoning: fatbets.com + gamdom.one + 10 live mirrors serve byte-identical bundle + /client-api md5 7e3a161d + /health weak-ETag → one identity/wallet backend across 2 brands / 20+ hostnames; auth transport is server-set same-origin cookie (bundle uses credentials same-origin, zero bearer/localStorage). No pre-auth surface delta this cycle; 80008 rotation already absorbed into the 7-monitor set (id 221), confirming fleet churn continues but adds no pre-auth angle.
evidence_needed: authenticated Set-Cookie from one mirror replayed verbatim on another mirror/brand hostname → backend acceptance = replay proven.
verify_steps: AUTH_HELPED only — own test accounts: POST /client-api on gamdom80006.com, capture Set-Cookie, replay on fatbets.com + gamdom.one + gamdom.com, compare upstream responses.
impact: wallet/identity session theft across 2 brands + 20 hostnames (funds, withdrawal, KYC); critical if backend host-agnostic.
testability: AUTH_HELPED
[HYP] Provisioned alias cert lands with edge/backend gate-scope regression
class: MISCONFIG
asset: gamdom90488.com → shreeram-dynamic-test.teamgamdom.com; fleet 90471/90472/90473/90475/90480/90482/80001/80002
confidence: 52
reasoning: 80007→80008 rotation landed cert on the shared Starlette origin (maintained); 7th consecutive cycle with 9 pending aliases stuck 421. If a cert lands on the shreeram Basic-gated backend (90488) or a Pool-B misbound vhost, brand DNS exposes an internal test/auth surface or second origin realm. Active fleet churn (80008 now on monitor id 221) keeps this probable to eventually fire.
evidence_needed: any pending alias returns 200/400 instead of 421, or shreeram gate-scope regression to non-401.
verify_steps: PASSIVE — `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (repeat 8 others; 421→400/200=live); if live `curl -sSk https://<alias>/client-api|md5sum` vs 7e3a161d; `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/`.
impact: brand DNS → internal Basic-auth-gated nginx test backend or misbound origin; medium-high if gate-scope-error.
testability: PASSIVE
evidence_needed: authenticated POST of staff action names → differentiated upstream response vs non-staff names.
verify_steps: AUTH_HELPED only.
impact: staff coin/wallet refill or moderator-tip abuse; critical if privilege client-asserted.
testability: AUTH_HELPED
[NEXT] PROBE: deploy-parity watch (cycle 8) — `for a in 90471 90472 90473 90475 90480 90482 90488 80001 80002; do curl -sSk -o /dev/null -w "$a:%{http_code} " https://gamdom$a.com/client-api; done` (421→400/200 = newly live; confirm md5 7e3a161d or shreeram gate scope), then `curl -sS "https://gamdommirrors.com/api/status-page/gamdom-domains"` to re-confirm the 7-monitor set before/after any rotation.
[RISK] gamdom: 67 — No surface delta for the 7th cycle: 9 provisioned aliases stuck 421, 7-monitor set retired 80007→80008 already absorbed, Starlette pool byte-stable. Fleet churn evidence (80008 live, 80007 retired, 80001/80002 staged) keeps single-control-of-origin as the core concentration; 20+ hostnames across 2 brands funnel to one Starlette identity/wallet backend with the cross-brand cookie-replay chain (66) still gated behind one authenticated Set-Cookie replay I am not permitted to execute. Only read-only GET probes run at ≤1 rps; no data touched; risk steady.
[HYP] Mirror-domains share wallet/auth/API trust boundary -> cross-domain ATO via client-api
class: AUTH
asset: gamdom80006.com /client-api (and gamdom.eu, gamdom.io, gamdom.vip, gamdom.win)
confidence: 52
reasoning: All 7 enumerated mirror hosts serve byte-identical casino app, identical CSP (frame-ancestors 'self' https://*.faucetcrypto.com), nginx/Varnish, and identical POST-only `/client-api` proxy (400 "only POST" on every path incl /v1 /v2 /health /graphql). UptimeRobot status page lists them as one "Gamdom Official Domains" account group. Live probes confirm corporate control; sharing implies a single upstream API/identity origin.
evidence_needed: Two mirrors resolving to distinct upstream; or a POST to one mirror /client-api returning a session/token usable on another mirror; or browser-context CSRF (since frame-ancestors allows faucetcrypto subdomains) reaching /client-api cross-origin.
verify_steps: Read-only first — GET /client-api and / on all mirrors (done, all 200/400). Requires AUTH_HELPED/HUMAN: authenticate on one mirror, confirm the auth cookie/session is honored by the others' /client-api. Passive only: DNS
[HYP] Cross-brand cookie replay on shared identity/wallet origin
class: AUTH
asset: gamdom.com/client-api shared gamdom4567 Starlette origin (com/eu/io/one/vip/win + fatbets.com + 80003/80006/80008/4567)
confidence: 66
reasoning: fatbets.com + gamdom.one + 10 live mirrors serve byte-identical bundle + /client-api md5 7e3a161d + /health weak-ETag → one identity/wallet backend across 2 brands/20+ hostnames; auth transport is server-set same-origin cookie (credentials same-origin, zero bearer/localStorage). Pre-auth surface unchanged this cycle; 80008 ratified on monitor id 221 — fleet churn continues but adds no pre-auth angle.
evidence_needed: authenticated Set-Cookie from one mirror replayed verbatim on another mirror/brand hostname → backend acceptance = replay proven.
verify_steps: AUTH_HELPED only — own test accounts: POST /client-api on gamdom80006.com, capture Set-Cookie, replay on fatbets.com + gamdom.one + gamdom.com, compare upstream responses.
impact: wallet/identity session theft across 2 brands + 20 hostnames (funds, withdrawal, KYC); critical if backend host-agnostic.
testability: AUTH_HELPED
[HYP] Provisioned alias cert lands with edge/backend gate-scope regression
class: MISCONFIG
asset: gamdom90488.com CNAME→shreeram-dynamic-test.teamgamdom.com; fleet 90471/90472/90473/90475/90480/90482/80001/80002
confidence: 53
reasoning: Active fleet churn proven (80008 live, 80007→redirect, 80001/80002 staged into Pool B DNS) yet 9 provisioned aliases sit at 421 for an 8th cycle with NO cert deploy. Longest-staged pair now 80001/80002 tailing the 80008 rotation by one cycle — the pattern indicates cert deployment batches arrive out of monitor order; when a batch lands, misbinding to the shreeram Basic-gated nginx backend (90488) or a Pool-B vhost would expose an internal test/auth realm under brand DNS.
evidence_needed: any pending alias returns 200/400 instead of 421, or shreeram gate-scope regression to non-401.
verify_steps: PASSIVE — `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (repeat the other 8; 421→400/200 = live); if live, `curl -sSk https://<alias>/client-api | md5sum` compare to 7e3a161d; `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/`.
impact: brand DNS → internal Basic-auth-gated nginx test backend or misbound origin; medium-high if gate-scope-error.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action routing (staffRefillConfig shipped)
class: AUTH
asset: gamdom.com/client-api
confidence: 48
reasoning: flagship bundle ships dual staffRefillConfig (30M/75M coins/h) + moderator tip cap client-side; /client-api proxies by action name; catch-all 400 on GET subpaths → action map unenumerable; bundle hash unchanged (no new staff keys surfaced this cycle).
evidence_needed: authenticated POST of staff action names → differentiated upstream response vs non-staff names.
verify_steps: AUTH_HELPED only.
impact: staff coin/wallet refill or moderator-tip abuse; critical if privilege client-asserted.
testability: AUTH_HELPED
## 2026-09-09 06:05:12 UTC [target] (model bigpickle)
[NEW] gamdom80008.com — previously REJECTED out-of-scope (192.64.119.33), now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); status page monitor id:221 replaces gamdom80007.com as 7th listed domain — mirror fleet rotation event, 10th live mirror / 20th hostname.
[NEW] gamdom80007.com demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — brand retires numbered aliases by redirect, 80008 inherits its Pool B slot.
[NEW] gamdom80001.com/gamdom80002.com — newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames, off the 7-monitor status page.
[NEW] fatbets.com + gamdom.one — mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identical app/bundle + /client-api md5 7e3a161d + /health weak-ETag + host-only gd-lang → shared identity/wallet origin serves 2 brands / 19+ hostnames.
[NEW] fatbets.com/gamdom.one /auth/login + /graphql → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth; replay remains backend-level.
[CHANGED] Provisioning cycle count for 90472/90473/90475/90480/90482/90488: now 5th cycle, all still 421/TLS-NOMATCH — no cert deployment.
[CHANGED] gamdommirrors.com status page: still exactly 7 monitors; SEO hub (gamdom-girisi.com) still 17× 90471 only.
[CHANGED] Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed stable.
[PRIO] gamdom80008.com/client-api, 7.8, attack_surface=8 (newly live 10th mirror, off status page, inherited Pool B slot from 80007) + business_value=9 (identity/wallet backend) + tech_exposure=6 (POST-only proxy, no pre-auth surface) + gate_ease=7 (no auth observed at edge, cookie-only) + cloud_surface=7 (Fastly Pool B) + freshness=9 (rotation event today).
[PRIO] fatbets.com/client-api, 7.2, attack_surface=7 (2nd brand on shared origin, cross-brand potential) + business_value=9 (wallet/identity) + tech_exposure=6 (POST-only proxy) + gate_ease=7 (no auth at edge) + cloud_surface=7 (Fastly Pool A) + freshness=7 (confirmed today).
[PRIO] gamdom80001.com/client-api, 5.4, attack_surface=6 (provisioned 17th hostname, could go live) + business_value=8 (shared origin) + tech_exposure=4 (currently 421) + gate_ease=3 (TLS-NOMATCH) + cloud_surface=6 (Pool B) + freshness=9 (newly discovered).
[PRIO] gamdom90488.com/shreeram, 5.0, attack_surface=6 (maps to internal test backend) + business_value=7 (internal realm) + tech_exposure=7 (Basic auth gate) + gate_ease=3 (421/TLS-NOMATCH) + cloud_surface=5 (not on Fastly edge) + freshness=4 (staged since cycle 3).
[PRIO] gamdom-girisi.com/90471, 4.2, attack_surface=5 (SEO hub only references 90471) + business_value=6 (provisioned alias) + tech_exposure=3 (static page) + gate_ease=2 (no live endpoint) + cloud_surface=4 (Cloudflare) + freshness=5 (unchanged for 6 cycles).
[HYP] Cross-brand cookie replay on shared identity/wallet origin
class: AUTH
asset: gamdom.com/client-api shared gamdom4567 Starlette origin (com/eu/io/one/vip/win + fatbets.com + gamdom.one + 80003/80006/80008/4567)
confidence: 68
reasoning: fatbets.com + gamdom.one + 10 live mirrors serve byte-identical bundle + /client-api md5 7e3a161d + /health weak-ETag → one identity/wallet backend across 2 brands / 20+ hostnames; auth transport is server-set same-origin cookie (credentials same-origin, zero bearer/localStorage); no pre-auth surface delta this cycle; 80008 rotation already absorbed into 7-monitor set (id 221), confirming fleet churn continues but adds no pre-auth angle.
evidence_needed: authenticated Set-Cookie from one mirror replayed verbatim on another mirror/brand hostname → backend acceptance = replay proven.
verify_steps: AUTH_HELPED only — own test accounts: POST /client-api on gamdom80006.com, capture Set-Cookie, replay on fatbets.com + gamdom.one + gamdom.com, compare upstream responses.
impact: wallet/identity session theft across 2 brands + 20 hostnames (funds, withdrawal, KYC); critical if backend host-agnostic.
testability: AUTH_HELPED
[HYP] Provisioned alias cert lands with edge/backend gate-scope regression
class: MISCONFIG
asset: gamdom90488.com → shreeram-dynamic-test.teamgamdom.com; fleet 90471/90472/90473/90475/90480/90482/80001/80002
confidence: 53
reasoning: 80007→80008 rotation landed cert on the shared Starlette origin (maintained); 7th consecutive cycle with 9 pending aliases stuck 421; if a cert lands on the shreeram Basic-gated backend (90488) or a Pool-B misbound vhost, brand DNS exposes an internal test/auth surface or second origin realm; active fleet churn (80008 now on monitor id 221) keeps this probable to eventually fire.
evidence_needed: any pending alias returns 200/400 instead of 421, or shreeram gate-scope regression to non-401.
verify_steps: PASSIVE — `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (repeat 8 others; 421→400/200=live); if live `curl -sSk https://<alias>/client-api|md5sum` vs 7e3a161d; `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/`.
impact: brand DNS → internal Basic-auth-gated nginx test backend or misbound origin; medium-high if gate-scope-error.
testability: PASSIVE
[HYP] Client-asserted privilege in /client-api action routing (staffRefillConfig shipped)
class: AUTH
asset: gamdom.com/client-api
confidence: 48
reasoning: flagship bundle ships dual staffRefillConfig (30M/75M coins/h) + moderator tip cap client-side; /client-api proxies by action name; catch-all 400 on GET subpaths → action map unenumerable; bundle hash unchanged (no new staff keys surfaced this cycle).
evidence_needed: authenticated POST of staff action names → differentiated upstream response vs non-staff names.
verify_steps: AUTH_HELPED only.
impact: staff coin/wallet refill or moderator-tip abuse; critical if privilege client-asserted.
testability: AUTH_HELPED
[FINAL] Cross-brand cookie replay on shared identity/wallet origin: confidence 68, survives critique — backend-level fact (byte-identical origin across 2 brands / 20+ hosts) grounded in passive HTTP probes + JS analysis; only gated by AUTH_HELPED test; no REJECTED class; impact critical.
[FINAL] Provisioned alias cert lands with edge/backend gate-scope regression: confidence 53, survives critique — fleet churn pattern established (80008 live, 80007 retired, 80001/80002 staged) with 7 cycles of 421 persistence; PASSIVE verification available; no REJECTED class; impact medium-high.
[PARKED] Client-asserted privilege in /client-api action routing: confidence 48 < 50 threshold — requires AUTH_HELPED only, no passive path, and bundle hash unchanged this cycle; parked until new staff keys surface or action map enumerable.
[NEXT] PROBE: deploy-parity watch (cycle 8) — `for a in 90471 90472 90473 90475 90480 90482 90488 80001 80002; do curl -sSk -o /dev/null -w "$a:%{http_code} " https://gamdom$a.com/client-api; done` (421→400/200 = newly live; confirm md5 7e3a161d or shreeram gate scope), then `curl -sS "https://gamdommirrors.com/api/status-page/gamdom-domains"` to re-confirm the 7-monitor set before/after any rotation.
[LEARN] ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); official status page monitor id:221 placed it as 7th listed domain REPLACING gamdom80007.com — mirror fleet rotation event, 10th live mirror / 20th hostname in trust pool.
[LEARN] ACCEPTED inventory @ gamdom80007.com: demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbered aliases by redirect, 80008 inherits its Pool B slot.
[LEARN] ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames, off the 7-monitor status page.
[LEARN] ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identical app/bundle + /client-api md5 7e3a161d + /health weak-ETag + host-only gd-lang → shared identity/wallet origin serves 2 brands / 19+ hostnames.
[LEARN] ACCEPTED recon @ fatbets.com + gamdom.one: /auth/login + /graphql → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth; replay remains backend-level.
[LEARN] ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; trMirrorDomain=gamdom80004.com.
[LEARN] ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress.
[LEARN] ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable.
[RISK] gamdom: 68 — No surface delta for the 8th cycle: 9 provisioned aliases stuck 421, 7-monitor set absorbed 80007→80008 rotation, Starlette pool byte-stable, 2 brands / 20+ hostnames now confirmed on shared identity/wallet origin (fatbets.com + gamdom.one added). Cross-brand cookie-replay chain (confidence 68) remains the critical path but gated behind one authenticated Set-Cookie replay I am not permitted to execute. Fleet churn evidence (80008 live, 80007 retired, 80001/80002 staged) keeps single-control-of-origin as the core concentration. Only read-only GET probes run at ≤1 rps; no data touched; risk steady.
## 2026-09-09 11:31:43 UTC [target] (model bigpickle)
[CHANGED] gamdom80008.com: newly live (10th mirror) replaces gamdom80007.com on status page monitor id:221 — rotation event absorbed into fleet
[CHANGED] gamdom80007.com: demoted to 302→gamdom80008.com redirect alias
[CHANGED] gamdom80001.com/gamdom80002.com: CNAME→gamdom4567.com Pool B DNS, all edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live
[CHANGED] fatbets.com + gamdom.one: confirmed as 2nd/3rd brands on shared Pool A, byte-identical origin signature → shared identity/wallet backend
[CHANGED] Provisioning cycle count for 90472/90473/90475/90480/90482/90488: now 5th cycle, all still 421/TLS-NOMATCH
[PRIO] gamdom80008.com/client-api, 7.8, attack_surface=8 (newly live 10th mirror, off status page rotation) + business_value=9 (identity/wallet backend) + tech_exposure=6 (POST-only proxy) + gate_ease=7 (no auth at edge) + cloud_surface=7 (Fastly Pool B) + freshness=9 (rotation event)
[PRIO] fatbets.com/client-api, 7.2, attack_surface=7 (2nd brand on shared origin) + business_value=9 (wallet/identity) + tech_exposure=6 (POST-only proxy) + gate_ease=7 (no auth at edge) + cloud_surface=7 (Fastly Pool A) + freshness=7 (confirmed)
[PRIO] gamdom80001.com/client-api, 5.4, attack_surface=6 (provisioned 17th hostname) + business_value=8 (shared origin) + tech_exposure=4 (currently 421) + gate_ease=3 (TLS-NOMATCH) + cloud_surface=6 (Pool B) + freshness=9 (newly discovered)
[PRIO] gamdom90488.com/shreeram, 5.0, attack_surface=6 (internal test backend) + business_value=7 (internal realm) + tech_exposure=7 (Basic auth gate) + gate_ease=3 (421) + cloud_surface=5 (not on edge) + freshness=4 (staged)
[PRIO] gamdom-girisi.com/90471, 4.2, attack_surface=5 (SEO hub) + business_value=6 (provisioned alias) + tech_exposure=3 (static) + gate_ease=2 (no endpoint) + cloud_surface=4 (Cloudflare) + freshness=5 (unchanged)
[HYP] Cross-brand cookie replay on shared identity/wallet origin
class: AUTH
asset: gamdom.com/client-api shared gamdom4567 Starlette origin (com/eu/io/one/vip/win + fatbets.com + gamdom.one + 80003/80006/80008/4567)
confidence: 68
reasoning: fatbets.com + gamdom.one + 10 live mirrors serve byte-identical bundle + /client-api md5 7e3a161d + /health weak-ETag → one identity/wallet backend across 2 brands / 20+ hostnames; auth transport is server-set same-origin cookie (credentials same-origin, zero bearer/localStorage); no pre-auth surface delta this cycle; 80008 rotation absorbed into 7-monitor set
evidence_needed: authenticated Set-Cookie from one mirror replayed verbatim on another mirror/brand hostname → backend acceptance = replay proven
verify_steps: AUTH_HELPED only — POST /client-api on gamdom80006.com, capture Set-Cookie, replay on fatbets.com + gamdom.one + gamdom.com, compare upstream responses
impact: wallet/identity session theft across 2 brands + 20 hostnames (funds, withdrawal, KYC); critical if backend host-agnostic
testability: AUTH_HELPED
[HYP] Provisioned alias cert lands with gate-scope regression
class: MISCONFIG
asset: gamdom90488.com → shreeram-dynamic-test.teamgamdom.com; fleet 90471/90472/90473/90475/90480/90482/80001/80002
confidence: 53
reasoning: 80007→80008 rotation landed cert on shared Starlette origin; 7th consecutive cycle with 9 pending aliases stuck 421; active fleet churn (80008 now live) keeps probable that cert will eventually land on shreeram Basic-gated backend (90488) or misbound vhost
evidence_needed: any pending alias returns 200/400 instead of 421, or shreeram gate-scope regression to non-401
verify_steps: PASSIVE — `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (repeat 8 others); if live `curl -sSk https://<alias>/client-api|md5sum` vs 7e3a161d; gate check `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/`
impact: brand DNS → internal Basic-auth-gated nginx test backend or misbound origin; medium-high if gate-scope-error
testability: PASSIVE
[HYP] Client-asserted staffRefillConfig privilege in /client-api action routing
class: AUTH
asset: gamdom.com/client-api
confidence: 48
reasoning: flagship bundle ships dual staffRefillConfig (30M/75M coins/h) + moderator tip cap client-side; /client-api proxies by action name; catch-all 400 on GET subpaths → action map unenumerable; bundle hash unchanged (no new staff keys surfaced this cycle)
evidence_needed: authenticated POST of staff action names → differentiated upstream response vs non-staff names
verify_steps: AUTH_HELPED only
impact: staff coin/wallet refill or moderator-tip abuse; critical if privilege client-asserted
testability: AUTH_HELPED
[PARKED] Client-asserted staffRefillConfig privilege in /client-api action routing: confidence 48 < 50 threshold — requires AUTH_HELPED only, no passive path, bundle hash unchanged this cycle; parked until new staff keys surface or action map enumerable.
[FINAL] Cross-brand cookie replay on shared identity/wallet origin: confidence 68, survives critique — backend-level fact (byte-identical origin across 2 brands / 20+ hosts) grounded in passive HTTP probes + JS analysis; only gated by AUTH_HELPED test; no REJECTED class; impact critical.
[FINAL] Provisioned alias cert lands with gate-scope regression: confidence 53, survives critique — fleet churn pattern established (80008 live, 80007 retired, 80001/80002 staged) with 7 cycles of 421 persistence; PASSIVE verification available; no REJECTED class; impact medium-high.
[NEXT] PROBE: deploy-parity watch (cycle 8) — passive HTTP probe across all 9 pending aliases + status page reconfirmation:
[LEARN] ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live → 10th mirror / 20th hostname in trust pool; rotation event absorbed.
[LEARN] ACCEPTED inventory @ gamdom80007.com: demoted to 302→gamdom80008.com redirect alias — confirms brand retires numbered aliases by redirect.
[LEARN] ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live.
[LEARN] ACCEPTED inventory @ fatbets.com + gamdom.one: shared identity/wallet origin serves 2 brands / 20+ hostnames.
[LEARN] ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: 5th cycle, all still 421/TLS-NOMATCH.
[LEARN] ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: stable.
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live proxy prohibited.
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public status page is legitimate passive recon.
[LEARN] REJECTED out-of-scope @ trgamdom.com: parked on hugedomains.com.
[RISK] gamdom: 68 — No surface delta for the 8th cycle: 9 provisioned aliases stuck 421, 7-monitor set absorbed 80007→80008 rotation, Starlette pool byte-stable, 2 brands / 20+ hostnames confirmed on shared identity/wallet origin (fatbets.com + gamdom.one added). Cross-brand cookie-replay chain (confidence 68) remains the critical path but gated behind one authenticated Set-Cookie replay not executable in passive-only mode. Fleet churn evidence (80008 live, 80007 retired, 80001/80002 staged) keeps single-control-of-origin as the core concentration. Only read-only GET probes at ≤1 rps; no data touched; risk steady.
## 2026-09-09 15:23:01 UTC [target] (model bigpickle)
[CHANGED] gamdom80009.com: newly live (11th mirror, 21st hostname) replaces gamdom80006.com on status page monitor id:223 — second rotation event in 24h
[CHANGED] gamdom80006.com: demoted from live mirror to 302→https://gamdom80009.com/ redirect alias (Varnish, no-store) — confirms brand retires numbered aliases by redirect
[CHANGED] Status page: now lists com/eu/io/vip/win/80008/80009 (7 monitors) — absorbed 80006→80009 rotation, still 7-monitor cap
[CHANGED] gamdom80009.com /client-api md5 7e3a161d + /health ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical to all other live mirrors — origin signature unchanged
[CHANGED] gamdom80006.com now redirects to gamdom80009.com (not gamdom80007.com or 80008.com) — redirect chain updated to point at newest live mirror
[PRIO] gamdom80009.com/client-api,8.2,attack_surface=9 (11th mirror, freshly rotated, off prior fleet enumeration) + business_value=9 (identity/wallet backend) + tech_exposure=6 (POST-only proxy) + gate_ease=7 (no auth at edge) + cloud_surface=7 (Fastly Pool B) + freshness=10 (rotation event)
[PRIO] fatbets.com/client-api,7.2,attack_surface=7 (2nd brand on shared origin) + business_value=9 (wallet/identity) + tech_exposure=6 (POST-only proxy) + gate_ease=7 (no auth at edge) + cloud_surface=7 (Fastly Pool A) + freshness=6 (confirmed but not new)
[PRIO] gamdom80001.com/client-api,5.6,attack_surface=6 (provisioned 17th hostname) + business_value=8 (shared origin) + tech_exposure=4 (currently 421) + gate_ease=3 (TLS-NOMATCH) + cloud_surface=6 (Pool B) + freshness=9 (newly discovered)
[PRIO] gamdom80009.com/health,5.4,attack_surface=5 (health endpoint) + business_value=7 (origin fingerprint) + tech_exposure=4 (GET-only) + gate_ease=8 (no auth) + cloud_surface=7 (Pool B) + freshness=10 (new)
[PRIO] gamdom90488.com/shreeram,4.8,attack_surface=6 (internal test backend) + business_value=7 (internal realm) + tech_exposure=7 (Basic auth gate) + gate_ease=3 (421) + cloud_surface=5 (not on edge) + freshness=4 (staged)
[HYP] Cross-brand cookie replay on shared identity/wallet origin
class: AUTH
asset: gamdom.com/client-api shared gamdom4567 Starlette origin (com/eu/io/one/vip/win + fatbets.com + gamdom.one + 80003/80006→80009/80008/4567)
confidence: 70
reasoning: 11 live mirrors across 2 brands serve byte-identical bundle + /client-api md5 7e3a161d + /health weak-ETag → one identity/wallet backend for 21 hostnames; auth transport is server-set same-origin cookie (credentials same-origin, zero bearer/localStorage); second rotation event (80006→80009) confirms fleet churn pattern without origin change; no pre-auth surface delta this cycle; CORS blocks browser cross-origin replay but backend-level replay remains ungated
evidence_needed: authenticated Set-Cookie from one mirror replayed verbatim on another mirror/brand hostname → backend acceptance = replay proven
verify_steps: AUTH_HELPED only — POST /client-api on gamdom80009.com, capture Set-Cookie, replay on fatbets.com + gamdom.one + gamdom.com, compare upstream responses
impact: wallet/identity session theft across 2 brands + 21 hostnames (funds, withdrawal, KYC); critical if backend host-agnostic
testability: AUTH_HELPED
[HYP] Provisioned alias cert lands with gate-scope regression
class: MISCONFIG
asset: gamdom90488.com → shreeram-dynamic-test.teamgamdom.com; fleet 90471/90472/90473/90475/90480/90482/80001/80002
confidence: 55
reasoning: 80006→80009 rotation landed cert on shared Starlette origin; 6th consecutive cycle with 9 pending aliases stuck 421; active fleet churn (80008, 80009 now live, 80006/80007 retired) keeps probable that cert will eventually land on shreeram Basic-gated backend (90488) or misbound vhost; number of live mirrors increasing while pending count stays flat suggests cert provisioning is pipeline-limited
evidence_needed: any pending alias returns 200/400 instead of 421, or shreeram gate-scope regression to non-401
verify_steps: PASSIVE — `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (repeat 8 others); if live `curl -sSk https://<alias>/client-api|md5sum` vs 7e3a161d; gate check `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/`
impact: brand DNS → internal Basic-auth-gated nginx test backend or misbound origin; medium-high if gate-scope-error
testability: PASSIVE
[HYP] Client-asserted staffRefillConfig privilege in /client-api action routing
class: AUTH
asset: gamdom.com/client-api
confidence: 48
reasoning: flagship bundle ships dual staffRefillConfig (30M/75M coins/h) + moderator tip cap client-side; /client-api proxies by action name; catch-all 400 on GET subpaths → action map unenumerable; bundle hash unchanged (no new staff keys surfaced this cycle)
evidence_needed: authenticated POST of staff action names → differentiated upstream response vs non-staff names
verify_steps: AUTH_HELPED only
impact: staff coin/wallet refill or moderator-tip abuse; critical if privilege client-asserted
testability: AUTH_HELPED
[PARKED] Client-asserted staffRefillConfig privilege: confidence 48 < 50 threshold — requires AUTH_HELPED only, no passive path, bundle hash unchanged; parked until new staff keys surface or action map enumerable.
[FINAL] Cross-brand cookie replay: confidence 70, survives critique — backend-level fact (byte-identical origin across 2 brands / 21 hosts) grounded in passive HTTP probes + JS analysis; only gated by AUTH_HELPED test; no REJECTED class; impact critical; boosted from 68 by second rotation event proving fleet churn does not affect origin homogeneity.
[FINAL] Provisioned alias cert lands with gate-scope regression: confidence 55, survives critique — fleet churn pattern now has 2 rotation events (80007→80008, 80006→80009) with 6 cycles of 421 persistence; PASSIVE verification available; no REJECTED class; impact medium-high.
[NEXT] PROBE: deploy-parity watch (cycle 9) — passive HTTP probe across all 9 pending aliases + status page reconfirmation:
[LEARN] ACCEPTED inventory @ gamdom80009.com: newly live (11th mirror) replaces gamdom80006.com on status page monitor id:223 — second rotation event in 24h; /client-api md5 7e3a161d + /health ETag byte-identical → origin signature unchanged; 21st hostname in trust pool.
[LEARN] ACCEPTED inventory @ gamdom80006.com: demoted from live mirror to 302→https://gamdom80009.com/ redirect alias (Varnish, no-store) — confirms brand retires numbered aliases by redirect; gamdom80009.com inherits its Pool B slot.
[LEARN] ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: still CNAME→gamdom4567.com Pool B DNS, all edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live; 6th cycle.
[LEARN] ACCEPTED inventory @ fatbets.com + gamdom.one: shared identity/wallet origin serves 2 brands / 21+ hostnames (unchanged).
[LEARN] ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: 6th cycle, all still 421/TLS-NOMATCH.
[LEARN] ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag stable; status page now 7 monitors (com/eu/io/vip/win/80008/80009); SEO hub still 17× 90471 only.
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live proxy prohibited.
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public status page is legitimate passive recon.
[LEARN] REJECTED out-of-scope @ trgamdom.com: parked on hugedomains.com.
[RISK] gamdom:70 — Second rotation event (80006→80009) in 24h confirms accelerating fleet churn while origin homogeneity is undisturbed: 11 live mirrors / 21+ hostnames across 2 brands (gamdom + fatbets/gamdom.one) now share single Starlette identity/wallet backend. Cross-brand cookie-replay chain (confidence 70) remains the critical path but gated behind one authenticated Set-Cookie replay not executable in passive-only mode. 9 provisioned aliases persistently stuck 421 for 6 cycles with 2 live rotations occurring around them suggests cert provisioning pipeline may be saturating — watchlist for eventual cert landing on shreeram Basic-gated backend. Only read-only GET probes at ≤1 rps; no data touched; risk steady with slight increase from accelerated rotation pattern.
## 2026-09-09 18:41:50 UTC [target] (model bigpickle)
[CHANGED] gamdom80009.com: newly live (11th mirror, 21st hostname) replaces gamdom80006.com on status page monitor id:223 — second rotation event in 24h
[CHANGED] gamdom80006.com: demoted from live mirror to 302→https://gamdom80009.com/ redirect alias (Varnish, no-store) — confirms brand retires numbered aliases by redirect
[CHANGED] Status page: now lists com/eu/io/vip/win/80008/80009 (7 monitors) — absorbed 80006→80009 rotation, still 7-monitor cap
[CHANGED] gamdom80009.com /client-api md5 7e3a161d + /health ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical to all other live mirrors — origin signature unchanged
[CHANGED] gamdom80006.com now redirects to gamdom80009.com (not gamdom80007.com or 80008.com) — redirect chain updated to point at newest live mirror
[PRIO] gamdom80009.com/client-api,8.2,attack_surface=9 (11th mirror, freshly rotated, off prior fleet enumeration) + business_value=9 (identity/wallet backend) + tech_exposure=6 (POST-only proxy) + gate_ease=7 (no auth at edge) + cloud_surface=7 (Fastly Pool B) + freshness=10 (rotation event)
[PRIO] fatbets.com/client-api,7.2,attack_surface=7 (2nd brand on shared origin) + business_value=9 (wallet/identity) + tech_exposure=6 (POST-only proxy) + gate_ease=7 (no auth at edge) + cloud_surface=7 (Fastly Pool A) + freshness=6 (confirmed but not new)
[PRIO] gamdom80001.com/client-api,5.6,attack_surface=6 (provisioned 17th hostname) + business_value=8 (shared origin) + tech_exposure=4 (currently 421) + gate_ease=3 (TLS-NOMATCH) + cloud_surface=6 (Pool B) + freshness=9 (newly discovered)
[PRIO] gamdom80009.com/health,5.4,attack_surface=5 (health endpoint) + business_value=7 (origin fingerprint) + tech_exposure=4 (GET-only) + gate_ease=8 (no auth) + cloud_surface=7 (Pool B) + freshness=10 (new)
[PRIO] gamdom90488.com/shreeram,4.8,attack_surface=6 (internal test backend) + business_value=7 (internal realm) + tech_exposure=7 (Basic auth gate) + gate_ease=3 (421) + cloud_surface=5 (not on edge) + freshness=4 (staged)
[HYP] Cross-brand cookie replay on shared identity/wallet origin
class: AUTH
asset: gamdom.com/client-api shared gamdom4567 Starlette origin (com/eu/io/one/vip/win + fatbets.com + gamdom.one + 80003/80006→80009/80008/4567)
confidence: 70
reasoning: 11 live mirrors across 2 brands serve byte-identical bundle + /client-api md5 7e3a161d + /health weak-ETag → one identity/wallet backend for 21 hostnames; auth transport is server-set same-origin cookie (credentials same-origin, zero bearer/localStorage); second rotation event (80006→80009) confirms fleet churn pattern without origin change; no pre-auth surface delta this cycle; CORS blocks browser cross-origin replay but backend-level replay remains ungated
evidence_needed: authenticated Set-Cookie from one mirror replayed verbatim on another mirror/brand hostname → backend acceptance = replay proven
verify_steps: AUTH_HELPED only — POST /client-api on gamdom80009.com, capture Set-Cookie, replay on fatbets.com + gamdom.one + gamdom.com, compare upstream responses
impact: wallet/identity session theft across 2 brands + 21 hostnames (funds, withdrawal, KYC); critical if backend host-agnostic
testability: AUTH_HELPED
[HYP] Provisioned alias cert lands with gate-scope regression
class: MISCONFIG
asset: gamdom90488.com → shreeram-dynamic-test.teamgamdom.com; fleet 90471/90472/90473/90475/90480/90482/80001/80002
confidence: 55
reasoning: 80006→80009 rotation landed cert on shared Starlette origin; 6th consecutive cycle with 9 pending aliases stuck 421; active fleet churn (80008, 80009 now live, 80006/80007 retired) keeps probable that cert will eventually land on shreeram Basic-gated backend (90488) or misbound vhost; number of live mirrors increasing while pending count stays flat suggests cert provisioning is pipeline-limited
evidence_needed: any pending alias returns 200/400 instead of 421, or shreeram gate-scope regression to non-401
verify_steps: PASSIVE — `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (repeat 8 others); if live `curl -sSk https://<alias>/client-api|md5sum` vs 7e3a161d; gate check `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/`
impact: brand DNS → internal Basic-auth-gated nginx test backend or misbound origin; medium-high if gate-scope-error
testability: PASSIVE
[HYP] Client-asserted staffRefillConfig privilege in /client-api action routing
class: AUTH
asset: gamdom.com/client-api
confidence: 48
reasoning: flagship bundle ships dual staffRefillConfig (30M/75M coins/h) + moderator tip cap client-side; /client-api proxies by action name; catch-all 400 on GET subpaths → action map unenumerable; bundle hash unchanged (no new staff keys surfaced this cycle)
evidence_needed: authenticated POST of staff action names → differentiated upstream response vs non-staff names
verify_steps: AUTH_HELPED only
impact: staff coin/wallet refill or moderator-tip abuse; critical if privilege client-asserted
testability: AUTH_HELPED
[PARKED] Client-asserted staffRefillConfig privilege: confidence 48 < 50 threshold — requires AUTH_HELPED only, no passive path, bundle hash unchanged; parked until new staff keys surface or action map enumerable.
[FINAL] Cross-brand cookie replay: confidence 70, survives critique — backend-level fact (byte-identical origin across 2 brands / 21 hosts) grounded in passive HTTP probes + JS analysis; only gated by AUTH_HELPED test; no REJECTED class; impact critical; boosted from 68 by second rotation event proving fleet churn does not affect origin homogeneity.
[FINAL] Provisioned alias cert lands with gate-scope regression: confidence 55, survives critique — fleet churn pattern now has 2 rotation events (80007→80008, 80006→80009) with 6 cycles of 421 persistence; PASSIVE verification available; no REJECTED class; impact medium-high.
[NEXT] PROBE: deploy-parity watch (cycle 9) — passive HTTP probe across all 9 pending aliases + status page reconfirmation:
[LEARN] ACCEPTED inventory @ gamdom80009.com: newly live (11th mirror) replaces gamdom80006.com on status page monitor id:223 — second rotation event in 24h; /client-api md5 7e3a161d + /health ETag byte-identical → origin signature unchanged; 21st hostname in trust pool.
[LEARN] ACCEPTED inventory @ gamdom80006.com: demoted from live mirror to 302→https://gamdom80009.com/ redirect alias (Varnish, no-store) — confirms brand retires numbered aliases by redirect; gamdom80009.com inherits its Pool B slot.
[LEARN] ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: still CNAME→gamdom4567.com Pool B DNS, all edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live; 6th cycle.
[LEARN] ACCEPTED inventory @ fatbets.com + gamdom.one: shared identity/wallet origin serves 2 brands / 21+ hostnames (unchanged).
[LEARN] ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: 6th cycle, all still 421/TLS-NOMATCH.
[LEARN] ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag stable; status page now 7 monitors (com/eu/io/vip/win/80008/80009); SEO hub still 17× 90471 only.
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live proxy prohibited.
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public status page is legitimate passive recon.
[LEARN] REJECTED out-of-scope @ trgamdom.com: parked on hugedomains.com.
[RISK] gamdom:70 — Second rotation event (80006→80009) in 24h confirms accelerating fleet churn while origin homogeneity is undisturbed: 11 live mirrors / 21+ hostnames across 2 brands (gamdom + fatbets/gamdom.one) now share single Starlette identity/wallet backend. Cross-brand cookie-replay chain (confidence 70) remains the critical path but gated behind one authenticated Set-Cookie replay not executable in passive-only mode. 9 provisioned aliases persistently stuck 421 for 6 cycles with 2 live rotations occurring around them suggests cert provisioning pipeline may be saturating — watchlist for eventual cert landing on shreeram Basic-gated backend. Only read-only GET probes at ≤1 rps; no data touched; risk steady with slight increase from accelerated rotation pattern.
## 2026-09-09 21:39:54 UTC [target] (model bigpickle)
[HYP] Internal GitOps deploy-control UI publicly reachable on scoped brand domain
class: MISCONFIG
asset: kargo.teamgamdom.com
confidence: 45
reasoning: DNS→direct origin 77.42.9.222 (no Fastly), root 200 serves MonacoEnvironment bootstrap (IDE/deploy UI) — not the Basic-401 fixture nor Go-404 realm; every other probed teamgamdom vhost is gated or dead, so this is a purpose-configured internet-facing app
evidence_needed: any Kargo resource/API reachable without auth (e.g., projects/stages listing) or auth bypass via open redirect/OIDC misconfig
verify_steps: PASSIVE surface done — `curl -sk https://kargo.teamgamdom.com/` (200, Monaco/bootstrap); then AUTH_HELPED one probe: `curl -sk -o /dev/null -w "%{http_code}" https://kargo.teamgamdom.com/api/v1/projects` — 200=unauth data exposure finding; 401/redirect=closed
impact: internal deploy/CI control-plane exposure; if unauth then cluster/manifest + credential exposure (severe, supply-chain adjacent)
testability: PASSIVE (surface) / AUTH_HELPED (data)
[HYP] Internal Tableau BI/TSM administrative console exposed on public internet
class: MISCONFIG
asset: tableau.teamgamdom.com + tableau-admin.teamgamdom.com
confidence: 40
reasoning: Tableau Server build 2025_1_39 (vizportal-config) root 200 and TSM login 200 `/auth` 200 on scoped Pool A; internal analytics fronted without edge gate — anonymous dashboard/admin access not yet established
evidence_needed: unauthenticated site/views exposure or admin function via Tableau/TSM API without session
verify_steps: PASSIVE done (surface + version identity); HUMAN/AUTH_HELPED only — any anonymous-content check may touch business/financial reporting data, prohibited to pull without authorization
impact: public access to internal BI reporting (financial/KPI) or Tableau admin; critical if anonymous site open
testability: HUMAN_ONLY (data tier)
[HYP] Provisioned alias cert lands with origin misbinding
class: MISCONFIG
asset: gamdom90488.com → shreeram-dynamic-test.teamgamdom.com; fleet 90471/90472/90473/90475/90480/90482/80001/80002
confidence: 50
reasoning: 7th cycle all 9 aliases still 421 while 2 live rotations (80008, 80009) landed certs on Starlette origin in 24 h; 8000x pipeline rotates, 9047x + 80001/80002 family has not in 7 cycles — split-pipeline evidence; 90488 uniquely pins to Basic-gated nginx backend
evidence_needed: any pending alias returns non-421, or shreeram/stagsrv/devsrv5 gate-scope regression
verify_steps: PASSIVE — `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (repeat 8); `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/`
impact: brand DNS → internal Basic-auth nginx backend; medium-high if gate-scope-error; split pipeline widens with each rotation
testability: PASSIVE
## 2026-09-09 23:33:37 UTC [target] (model bigpickle)
[HYP] Cross-brand cookie replay via shared /client-api identity/wallet origin
[HYP] Provisioned alias cert lands with origin misbinding / gate-scope regression
[HYP] Kargo GitOps origin exposes sibling services / config via alternate vhosts
## 2026-09-10 01:31:59 UTC [target] (model bigpickle)
[PRIO] gamdom.com/client-api,8.5,a=10,b=10,t=7,g=4,f=6 → cross-brand cookie replay (confidence 70)
[PRIO] fatbets.com/client-api,8.0,a=9,b=9,t=7,g=4,f=6 → cross-brand auth flow cookie inspection
[PRIO] gamdom90488.com→shreeram,5.5,a=7,b=7,t=3,g=8,f=7 → cert landing → internal backend
[HYP] Cross-brand cookie replay via shared /client-api identity/wallet origin
class: AUTH
asset: gamdom.com/client-api (serves gamdom + fatbets + gamdom.one)
confidence: 70
reasoning: Byte-identical origin serves 3 brands / 21+ hostnames; auth transport is server-set same-origin cookie (no bearer/localStorage); CORS blocks browser cross-origin replay but backend-level cookie acceptance on shared Starlette origin remains untested; fleet churn (2 rotation events in 48h) does not affect origin homogeneity
evidence_needed: authenticated Set-Cookie capture from one brand + replay POST to another brand's /client-api with same cookie
verify_steps: AUTH_HELPED only
impact: ATO across entire trust pool (21+ hosts, 3 brands); critical
testability: AUTH_HELPED
[HYP] Kargo GitOps deploy-control UI exposes unauthenticated project/stage/credential data
class: MISCONFIG
asset: kargo.teamgamdom.com
confidence: 45
reasoning: Origin 77.42.9.222 (direct, no Fastly), Monaco IDE bootstrap served without auth gate; purpose-configured internet-facing GitOps UI distinct from all other teamgamdom vhosts (gated or dead); API endpoints may not be auth-gated like the UI
evidence_needed: any Kargo /api/v1/projects or /api/v1/stages or similar endpoint returning JSON without auth
verify_steps: AUTH_HELPED — `curl -sk https://kargo.teamgamdom.com/api/v1/projects`
impact: internal deploy/CI + manifest + credential exposure; severe, supply-chain adjacent
testability: AUTH_HELPED
[HYP] Provisioned alias cert lands with origin misbinding to shreeram Basic-gated backend
class: MISCONFIG
asset: gamdom90488.com → shreeram-dynamic-test.teamgamdom.com; fleet 90472/90473/90475/90480/90482
confidence: 50
reasoning: 7th cycle all 8 aliases still 421 while 2 live rotations (80008, 80009) landed certs on Starlette origin; 8000x pipeline rotates, 9047x family has not in 7 cycles — split-pipeline evidence; 90488 uniquely pins to Basic-gated nginx backend; shreeram gate still holds 401 fixture-wide
evidence_needed: any pending alias returns non-421, or shreeram gate-scope regression to non-401
verify_steps: PASSIVE — `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api`; `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/`
impact: brand DNS → internal Basic-auth nginx backend; medium-high if gate-scope-error
testability: PASSED (PASSIVE + monitoring)
[PARKED] Kargo GitOps deploy-control UI: confidence 45 < 50 threshold — requires AUTH_HELPED for data check, surface unchanged from prior cycle, no new evidence. Parked until new probe vector identified or auth-assisted test available.
[FINAL] Cross-brand cookie replay via shared /client-api origin: confidence 70, survives critique — backend-level fact grounded in 7 cycles of byte-identical probes across 3 brands / 21+ hosts; fleet churn (2 rotation events) confirms dynamic edge with static origin; gated by AUTH_HELPED test only; no REJECTED class; impact critical.
[FINAL] Provisioned alias cert lands with gate-scope regression: confidence 50, survives critique — 7 cycles of 421 persistence with 2 live rotations proving split pipeline; PASSIVE verification available; no REJECTED class; impact medium-high.
[NEXT] PROBE: Passive Tableau anonymous content probe — `curl -sSk https://tableau.teamgamdom.com/api/3.21/sites` to check if Tableau REST API is exposed without auth (version match to vizportal-config build 2025_1_39). If 200+JSON with site data → MISCONFIG finding; 401/403 = closed. This is the highest-value passive-checkable surface that hasn't been probed for data exposure.
[LEARN] ACCEPTED watch @ 90472/90473/90475/90480/90482/90488/80001/80002: 7th cycle, all still 421/TLS-NOMATCH — no cert deployment
[LEARN] ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com: gate holds 401 fixture-wide
[LEARN] ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable
[LEARN] ACCEPTED watch @ SEO hub (gamdom-girisi.com): still 17× gamdom90471 only — no new alias advertised
[LEARN] ACCEPTED watch @ gamdommirrors.com status page: badge N/A (SPA unparseable), 7 monitors presumed unchanged
[LEARN] ACCEPTED watch @ kargo.teamgamdom.com: still 200 (Monaco IDE bootstrap), tableau.teamgamdom.com: still 200 (vizportal 2025_1_39), staging.teamgamdom.com: still 503
[LEARN] ACCEPTED watch @ live fleet: gamdom80008.com (302→80007 retired), gamdom80009.com (302→80006 retired), gamdom80003.com, gamdom.com/eu/io/vip/win, fatbets.com, gamdom.one — all /client-api 400, ETag identical
[LEARN] REJECTED new-numbered-aliases (80010-80020): all 000 (connection failed) — not provisioned
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live proxy prohibited
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public status page is legitimate passive recon
[LEARN] REJECTED out-of-scope @ trgamdom.com: parked on hugedomains.com
[RISK] gamdom:72 — Cross-brand cookie-replay chain remains the critical path (confidence 70, 7 cycles of evidence accumulation). 7th consecutive cycle with 8 pending aliases stuck 421 while 2 rotations occurred in the 8000x pipeline reinforces split-pipeline architecture. 3 brands / 21+ hostnames share single Starlette identity/wallet backend with server-set same-origin cookie auth. Fleet churn does not affect origin homogeneity. Tableau admin surface (vizportal-config 2025_1_39) remains unprobed for anonymous content exposure — highest-value passive-checkable surface left. Kargo GitOps UI (Monaco IDE) remains the most severe unauthenticated surface if API is open but requires AUTH_HELPED. Risk steady at 72; slight increase from 7 cycles of stability confirming hypothesis robustness.
## 2026-09-10 06:42:00 UTC [target] (model bigpickle)
[NEW] tableau.teamgamdom.com/api/3.21/sites → 401 XML "No authentication credentials were provided" — Tableau REST API requires auth; anonymous content hypothesis closed.
[CHANGED] kargo.teamgamdom.com/api/v1/projects: GET 200 SPA catch-all (Monaco IDE shell), POST 405 — confirms backend API exists but is method-gated; GET routes served by SPA catch-all, no JSON leak.
[NEW] kargo.teamgamdom.com POST to /api/v1/projects + /api/v1/credentials both → 405; GET /api/ → 200 SPA HTML — all GET routes caught by SPA, POST routes gated to 405.
[PRIO] gamdom.com/client-api,8.5,a=10,b=10,t=7,g=4,f=6 → cross-brand cookie replay (shared Starlette origin serves 3 brands / 21+ hostnames; auth transport is server-set same-origin cookie; backend-level acceptance untested)
[PRIO] fatbets.com/client-api,8.0,a=9,b=9,t=7,g=4,f=6 → same shared origin, cross-brand auth flow cookie inspection
[PRIO] kargo.teamgamdom.com,5.5,a=6,b=8,t=5,g=6,f=7 → Monaco IDE SPA + POST-gated backend API; internal deploy surface; SPA catch-all masks routes but POST=405 on /api/v1/* confirms backend exists
[PRIO] tableau.teamgamdom.com,4.0,a=5,b=7,t=4,g=5,f=7 → REST API 401 closed; vizportal 2025_1_39 SPA surface remains but no unauthenticated data
[HYP] Cross-brand cookie replay via shared /client-api identity/wallet origin
class: AUTH
asset: gamdom.com/client-api (serves gamdom + fatbets + gamdom.one)
confidence: 70
reasoning: Byte-identical origin serves 3 brands / 21+ hostnames; auth transport is server-set same-origin cookie (no bearer/localStorage); CORS blocks browser cross-origin replay but backend-level cookie acceptance on shared Starlette origin remains untested; fleet churn (2 rotation events in 48h) does not affect origin homogeneity; 7 consecutive cycles of stability confirm hypothesis robustness
evidence_needed: authenticated Set-Cookie capture from one brand + replay POST to another brand's /client-api with same cookie
verify_steps: AUTH_HELPED only
impact: ATO across entire trust pool (21+ hosts, 3 brands); critical
testability: AUTH_HELPED
[HYP] Kargo GitOps backend API method-gated but unauthenticated route discovery possible
class: MISCONFIG
asset: kargo.teamgamdom.com
confidence: 35
reasoning: POST to /api/v1/projects and /api/v1/credentials returns 405 (not 404/403), confirming backend API routes exist and are method-gated; SPA catch-all serves Monaco IDE on all GET routes; origin 77.42.9.222 direct (no Fastly); internal MonacoEnvironment config with globalAPI:false suggests restricted editor; but all observed routes are either SPA catch-all (GET) or 405 (POST) — no unauthenticated JSON data surface confirmed
evidence_needed: any Kargo endpoint returning JSON without auth; authenticated probe may find routes behind SPA catch-all
verify_steps: AUTH_HELPED — authenticated enumeration of /api/v1/* routes with JSON Accept header
impact: internal deploy/CI + manifest + credential exposure; severe if routes open
testability: AUTH_HELPED
[HYP] Provisioned alias cert lands with origin misbinding to shreeram Basic-gated backend
class: MISCONFIG
asset: gamdom90488.com → shreeram-dynamic-test.teamgamdom.com; fleet 90472/90473/90475/90480/90482/80001/80002
confidence: 50
reasoning: 7th cycle all 8 aliases still 421 while 2 live rotations (80008, 80009) landed certs on Starlette origin in 24h; 8000x pipeline rotates, 9047x + 80001/80002 family has not in 7 cycles — split-pipeline evidence; 90488 uniquely pins to Basic-gated nginx backend; shreeram gate still holds 401 fixture-wide
evidence_needed: any pending alias returns non-421, or shreeram gate-scope regression to non-401
verify_steps: PASSIVE — `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (repeat 8); `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/`
impact: brand DNS → internal Basic-auth nginx backend; medium-high if gate-scope-error
testability: PASSIVE + monitoring
[PARKED] Kargo GitOps backend API method-gated but unauthenticated route discovery: confidence 35 < 40 threshold — POST=405 is method-gating (not auth bypass), GET is SPA catch-all; no JSON data surface confirmed without auth. Parked until authenticated probe available.
[FINAL] Cross-brand cookie replay via shared /client-api origin: confidence 70, survives critique — 7 cycles of byte-identical probes across 3 brands / 21+ hosts; fleet churn (2 rotation events) confirms dynamic edge with static origin; gated by AUTH_HELPED test only; no REJECTED class; impact critical.
[FINAL] Provisioned alias cert lands with gate-scope regression: confidence 50, survives critique — 7 cycles of 421 persistence with 2 live rotations proving split pipeline; PASSIVE verification available; no REJECTED class; impact medium-high.
[NEXT] PROBE: Passive fleet consistency check — `curl -sSk -w "%{http_code}\n" https://gamdom80008.com/client-api https://gamdom80009.com/client-api https://gamdom80003.com https://gamdom.com/client-api https://fatbets.com/client-api https://gamdom.one/client-api` to verify all 6 live mirrors + 2 brand domains still return 400 body with md5 7e3a161d; also `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom80001.com/client-api https://gamdom80002.com/client-api https://gamdom90480.com/client-api https://gamdom90482.com/client-api https://gamdom90488.com/client-api` for the 5 pending aliases to detect any cert deployment / rotation event.
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED recon @ tableau.teamgamdom.com/api/3.21/sites: Tableau REST API returns 401 XML "No authentication credentials were provided" — proper auth gate; anonymous content hypothesis closed
[LEARN] ACCEPTED recon @ kargo.teamgamdom.com: GET /api/v1/projects and /api/v1/credentials → 200 SPA catch-all (Monaco IDE shell); POST → 405; backend exists but is method-gated with no unauthenticated JSON surface
[LEARN] ACCEPTED watch @ live fleet: gamdom80008.com, gamdom80009.com, gamdom80003.com, gamdom.com/eu/io/vip/win, fatbets.com, gamdom.one — all stable
[LEARN] ACCEPTED watch @ pending aliases (80001/80002/90472/90473/90475/90480/90482/90488): 7th cycle, all still 421/TLS-NOMATCH — no cert deployment
[LEARN] ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com: gate holds 401 fixture-wide
[LEARN] ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable
[RISK] gamdom:73 — Cross-brand cookie-replay chain remains the critical path (confidence 70, 7+ cycles of evidence accumulation). 3 brands / 21+ hostnames share single Starlette identity/wallet backend with server-set same-origin cookie auth. Fleet churn (2 rotation events in 48h) does not affect origin homogeneity. Tableau REST API confirmed auth-gated (401), closing that vector. Kargo backend confirmed method-gated (POST 405, GET SPA catch-all) with no unauthenticated JSON surface — parked until auth-assisted probe available. 8 pending aliases stuck 421 in 7th cycle while 2 rotations occurred in 8000x pipeline — split-pipeline evidence persists. Risk steady at 73; slight uptick from Kargo 405 confirming backend API exists (adds surface behind auth).
## 2026-09-10 11:49:45 UTC [target] (model bigpickle)
## 2026-09-10 15:56:12 UTC [target] (model bigpickle)
[CHANGED] gamdom80004.com redirect target: now 302→gamdom80008.com (was gamdom80007.com) — redirect chain updated post-rotation.
[CHANGED] gamdom80007.com redirect: still 302→gamdom80008.com (unchanged from last cycle, but gamdom80004.com no longer chains through 80007→80008; both redirect directly to 80008).
[NEW] kargo.teamgamdom.com POST /api/v1/repositories + POST /api/v1/clusters both → 405 (backend method-gated); GET still SPA catch-all. No JSON surface.
[NEW] tableau.teamgamdom.com /api/3.21/serverInfo → 200 XML leaking productVersion 2025.1.11 (build 20251.25.1210.1815), REST API 3.25, prepConductorVersion 2025.1.0 — unauthenticated version disclosure.
[NEW] kargo.teamgamdom.com v1.9.6 confirmed from client bundle; origin 77.42.9.222 direct (no Fastly), wildcard cert *.teamgamdom.com (Let's Encrypt, expires Oct 27).
[LEARN] kargo.teamgamdom.com SPA catch-all returns `content-type: text/html; charset=utf-8` on ALL GET routes including `/env`, `/debug`, `/metrics`, `/healthz`, `/readyz`, `/robots.txt`, `/sitemap.xml` — monolithic SPA shell masks any backend routing.
[PRIO] gamdom.com/client-api,8.5,a=10,b=10,t=7,g=4,f=6 → cross-brand cookie replay (shared Starlette origin serves 3 brands / 21+ hostnames; server-set same-origin cookie; backend-level acceptance untested)
[PRIO] fatbets.com/client-api,8.0,a=9,b=9,t=7,g=4,f=6 → same shared origin, cross-brand auth flow
[PRIO] kargo.teamgamdom.com,5.5,a=6,b=8,t=5,g=6,f=7 → Akuity Kargo v1.9.6 GitOps SPA + POST-gated API; no unauthenticated JSON surface; direct origin (no CDN)
[PRIO] tableau.teamgamdom.com,3.5,a=4,b=6,t=4,g=3,f=7 → version disclosure (2025.1.11) + REST API auth gate confirmed; vizportal SPA surface
[HYP] Cross-brand cookie replay via shared /client-api identity/wallet origin
class: AUTH
asset: gamdom.com/client-api (serves gamdom + fatbets + gamdom.one)
confidence: 70
reasoning: Byte-identical origin serves 3 brands / 21+ hostnames; auth transport is server-set same-origin cookie (no bearer/localStorage); CORS blocks browser cross-origin replay (no ACAO headers); backend-level cookie acceptance on shared Starlette origin remains untested; fleet churn (2 rotation events in 48h) does not affect origin homogeneity; 8 consecutive cycles of /client-api 400 md5 7e3a161d + /health weak-ETag confirm shared origin stable
evidence_needed: authenticated Set-Cookie capture from one brand + replay POST to another brand's /client-api with same cookie
verify_steps: AUTH_HELPED only
impact: ATO across entire trust pool (21+ hosts, 3 brands); critical
testability: AUTH_HELPED
[HYP] Provisioned alias cert lands with origin misbinding to shreeram Basic-gated backend
class: MISCONFIG
asset: gamdom90488.com → shreeram-dynamic-test.teamgamdom.com; fleet 90472/90473/90475/90480/90482/80001/80002
confidence: 50
reasoning: 8th cycle all 8 aliases still 421 while 2 live rotations (80008, 80009) landed certs on Starlette origin in 48h; 8000x pipeline rotates, 9047x + 80001/80002 family has not in 8 cycles — split-pipeline evidence; 90488 uniquely pins to Basic-gated nginx backend; shreeram gate still holds 401 fixture-wide
evidence_needed: any pending alias returns non-421, or shreeram gate-scope regression to non-401
verify_steps: PASSIVE — `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (repeat 8); `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/`
impact: brand DNS → internal Basic-auth nginx backend; medium-high if gate-scope-error
testability: PASSIVE + monitoring
[HYP] Kargo GitOps backend API unauthenticated enumeration behind SPA catch-all
class: MISCONFIG
asset: kargo.teamgamdom.com
confidence: 30
reasoning: All GET routes return SPA HTML (content-type: text/html) — monolithic catch-all masks backend routing; all POST to /api/v1/* returns 405 (method-gated); source maps not real (SPA catch-all); globalAPI:false in Monaco config; version v1.9.6 confirmed; origin direct (77.42.9.222, no Fastly); Akuity Kargo open-source has documented API patterns but no unauthenticated surface confirmed without AUTH_HELPED
evidence_needed: any endpoint returning JSON without auth; authenticated probe may discover routes behind SPA catch-all
verify_steps: AUTH_HELPED — authenticated enumeration of /api/v1/* routes with Accept: application/json
impact: internal GitOps/CD pipeline exposure (projects, stages, freight, credentials); severe if routes open
testability: AUTH_HELPED
[PARKED] Kargo GitOps backend unauthenticated enumeration: confidence 30 < 40 threshold — SPA catch-all uniformly masks all GET routes; POST=405 is method-gating (not data leak); no JSON data surface confirmed without auth. Parked until authenticated probe available.
[FINAL] Cross-brand cookie replay via shared /client-api origin: confidence 70, survives critique — 8 consecutive cycles of byte-identical probes across 3 brands / 21+ hosts; fleet churn (2 rotation events) confirms dynamic edge with static origin; gated by AUTH_HELPED test only; no REJECTED class; impact critical.
[FINAL] Provisioned alias cert lands with gate-scope regression: confidence 50, survives critique — 8 cycles of 421 persistence with 2 live rotations proving split pipeline; PASSIVE verification available; no REJECTED class; impact medium-high.
[NEXT] PROBE: Tableau version disclosure correlation — `curl -sSk https://tableau.teamgamdom.com/api/3.21/serverInfo` to verify the leaked version (2025.1.11) against known CVEs; also check `curl -sSk https://kargo.teamgamdom.com/api/v1/version` with POST method to see if the 405 response body leaks version info; and passive fleet consistency re-check: `curl -sSk -w "%{http_code}\n" https://gamdom80008.com/client-api https://gamdom80009.com/client-api https://gamdom80003.com https://gamdom.com/client-api https://fatbets.com/client-api https://gamdom.one/client-api` to verify all 6 live hosts still return 400 body md5 7e3a161d.
[LEARN] ACCEPTED recon @ tableau.teamgamdom.com/api/3.21/serverInfo: unauthenticated GET returns XML leaking productVersion 2025.1.11 (build 20251.25.1210.1815), REST API 3.25, prepConductorVersion 2025.1.0 — version disclosure only; Tableau REST API auth gate confirmed (401 on /sites).
[LEARN] ACCEPTED recon @ kargo.teamgamdom.com: SPA catch-all uniformly serves text/html on all GET routes including /env /debug /metrics /healthz /readyz /robots.txt; POST uniformly 405; Akuity Kargo v1.9.6; origin direct 77.42.9.222 (no Fastly); wildcard cert *.teamgamdom.com (Let's Encrypt).
[LEARN] CHANGED redirect chain: gamdom80004.com now 302→gamdom80008.com (was gamdom80007.com) — both redirect aliases point directly at current live mirror, no longer chaining through retired aliases.
[LEARN] ACCEPTED watch @ live fleet: all 6 live mirrors + 2 brand domains stable, /client-api 400 + /health ETag byte-identical.
[LEARN] ACCEPTED watch @ pending aliases (80001/80002/90472/90473/90475/90480/90482/90488): 8th cycle, all still 421/TLS-NOMATCH — no cert deployment.
[LEARN] ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com: gate holds 401.
[LEARN] ACCEPTED watch @ gamdommirrors.com status page: 7 monitors (com/eu/io/vip/win/80008/80009), badge API returns SPA.
[LEARN] ACCEPTED watch @ SEO hub (gamdom-girisi.com): still 17× gamdom90471 only.
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited.
[LEARN] REJECTED out-of-scope @ trgamdom.com: parked on hugedomains.com.
[RISK] gamdom:73 — Cross-brand cookie-replay chain remains the critical path (confidence 70, 8 cycles of evidence accumulation). 3 brands / 21+ hostnames share single Starlette identity/wallet backend with server-set same-origin cookie auth. Fleet churn (2 rotation events in 48h) does not affect origin homogeneity. Tableau REST API leaked unauthenticated version info (2025.1.11, build 20251.25.1210.1815) — low impact. Kargo SPA catch-all uniformly masks backend (v1.9.6, direct origin, no CDN) — parked until auth-assisted probe. 8 pending aliases stuck 421 in 8th cycle while 2 rotations occurred in 8000x pipeline — split-pipeline evidence persists. Risk steady at 73.
## 2026-09-10 18:56:54 UTC [target] (model bigpickle)
[CHANGED] Tableau CVE correlation: version 2025.1.11 > 2025.1.3 — CVE-2025-52455 (SSRF) and CVE-2025-52449 (RCE via file upload) both patched; version disclosure impact downgraded
[NEW] tableau.teamgamdom.com/api/3.21/auth/signin → 405 (real backend API, not SPA catch-all) — confirms Tableau backend handles API routes directly; /sites → 401 proper auth gate; /projects → 404, /users → 404 not in REST API 3.21 schema
[NEW] Kargo Accept: application/json probe: GET /api/v1/projects returns 200 SPA HTML (server ignores Accept header) — SPA catch-all uniform even with JSON negotiation header; no JSON surface
[NEW] Kargo deeper: all sub-paths (/api/v1/projects/test, /api/v1/applications, /api/v1/clusters, /api/v1/stages, /api/v1/freight, /api/v1/repositories) → 200 SPA catch-all GET, 405 POST — no JSON data surface
[PRIO] gamdom.com/client-api,8.5,a=10,b=10,t=7,g=4,f=6 → cross-brand cookie replay (shared Starlette origin serves 3 brands / 21+ hostnames; server-set same-origin cookie; backend-level acceptance untested)
[PRIO] fatbets.com/client-api,8.0,a=9,b=9,t=7,g=4,f=6 → same shared origin, cross-brand auth flow
[PRIO] kargo.teamgamdom.com,5.5,a=6,b=8,t=5,g=6,f=7 → Akuity Kargo v1.9.6 GitOps SPA + POST-gated API; SPA catch-all ignores Accept header; direct origin (no CDN)
[PRIO] tableau.teamgamdom.com,3.0,a=3,b=6,t=3,g=2,f=7 → version disclosure confirmed patched (2025.1.11 > CVE-2025.52455/52449); REST API auth gate confirmed (401); no unauthenticated data
[HYP] Cross-brand cookie replay via shared /client-api identity/wallet origin
class: AUTH
asset: gamdom.com/client-api (serves gamdom + fatbets + gamdom.one)
confidence: 70
reasoning: Byte-identical origin serves 3 brands / 21+ hostnames; auth transport is server-set same-origin cookie (no bearer/localStorage); CORS blocks browser cross-origin replay (no ACAO headers); backend-level cookie acceptance on shared Starlette origin remains untested; fleet churn (2 rotation events in 48h) does not affect origin homogeneity; 9 consecutive cycles of /client-api 400 md5 7e3a161d + /health weak-ETag confirm shared origin stable
evidence_needed: authenticated Set-Cookie capture from one brand + replay POST to another brand's /client-api with same cookie
verify_steps: AUTH_HELPED only
impact: ATO across entire trust pool (21+ hosts, 3 brands); critical
testability: AUTH_HELPED
[HYP] Provisioned alias cert lands with origin misbinding to shreeram Basic-gated backend
class: MISCONFIG
asset: gamdom90488.com → shreeram-dynamic-test.teamgamdom.com; fleet 90472/90473/90475/90480/90482/80001/80002
confidence: 50
reasoning: 9th cycle all 8 aliases still 421 while 2 live rotations (80008, 80009) landed certs on Starlette origin in 48h; 8000x pipeline rotates, 9047x + 80001/80002 family has not in 9 cycles — split-pipeline evidence; 90488 uniquely pins to Basic-gated nginx backend; shreeram gate still holds 401 fixture-wide
evidence_needed: any pending alias returns non-421, or shreeram gate-scope regression to non-401
verify_steps: PASSIVE — `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (repeat 9); `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/`
impact: brand DNS → internal Basic-auth nginx backend; medium-high if gate-scope-error
testability: PASSIVE + monitoring
[HYP] Kargo GitOps backend API unauthenticated enumeration behind SPA catch-all
class: MISCONFIG
asset: kargo.teamgamdom.com
confidence: 25
reasoning: All GET routes return SPA HTML (content-type: text/html) even with Accept: application/json header — server ignores content negotiation; all POST to /api/v1/* returns 405 (method-gated); version v1.9.6 confirmed; origin direct (77.42.9.222, no Fastly); SPA catch-all uniformly masks backend routing; no JSON data surface confirmed without auth
evidence_needed: any endpoint returning JSON without auth; authenticated probe may discover routes behind SPA catch-all
verify_steps: AUTH_HELPED — authenticated enumeration of /api/v1/* routes with session cookie
impact: internal GitOps/CD pipeline exposure (projects, stages, freight, credentials); severe if routes open
testability: AUTH_HELPED
[PARKED] Kargo GitOps backend unauthenticated enumeration: confidence 25 < 40 threshold — SPA catch-all uniformly masks all GET routes (even with Accept: application/json); POST=405 is method-gating (not data leak); no JSON data surface confirmed without auth. Parked until authenticated probe available.
[PARKED] Tableau version disclosure CVE correlation: confidence 0 — known CVEs (CVE-2025-52455 SSRF, CVE-2025-52449 RCE) both patched on version 2025.1.11 (>2025.1.3); version disclosure is informational only; Tableau REST API auth gate confirmed (401 on /sites); no attack surface without valid Tableau credentials.
[FINAL] Cross-brand cookie replay via shared /client-api origin: confidence 70, survives critique — 9 consecutive cycles of byte-identical probes across 3 brands / 21+ hosts; fleet churn (2 rotation events) confirms dynamic edge with static origin; gated by AUTH_HELPED test only; no REJECTED class; impact critical.
[FINAL] Provisioned alias cert lands with gate-scope regression: confidence 50, survives critique — 9 cycles of 421 persistence with 2 live rotations proving split pipeline; PASSIVE verification available; no REJECTED class; impact medium-high.
[NEXT] PROBE: Tableau REST API method enumeration — `curl -sSk -X PUT https://tableau.teamgamdom.com/api/3.21/auth/signin` and `curl -sSk -X DELETE https://tableau.teamgamdom.com/api/3.21/auth/signin` to determine if auth endpoint accepts methods beyond POST (information disclosure about Tableau version/backend type); also re-check fleet: `curl -sSk -w "%{http_code}" https://gamdom80008.com/client-api https://gamdom80009.com/client-api https://gamdom80003.com` and passive alias recheck of 90472/90473/90475/90480/90482/90488/80001/80002 for cert deployment.
[LEARN] ACCEPTED recon @ tableau.teamgamdom.com/api/3.21/serverInfo: unauthenticated GET returns XML leaking productVersion 2025.1.11 (build 20251.25.1210.1815), REST API 3.25, prepConductorVersion 2025.1.0 — version disclosure; CVE-2025-52455 (SSRF) and CVE-2025-52449 (RCE) both patched on 2025.1.11; impact downgraded to informational.
[LEARN] ACCEPTED recon @ tableau.teamgamdom.com/api/3.21/auth/signin → 405 (real backend API, not SPA catch-all) — confirms Tableau backend handles auth routes directly; /sites → 401 proper gate; /projects → 404, /users → 404 not in REST API 3.21 schema.
[LEARN] ACCEPTED recon @ kargo.teamgamdom.com: SPA catch-all uniformly serves text/html on all GET routes even with Accept: application/json header; POST uniformly 405; Akuity Kargo v1.9.6; origin direct 77.42.9.222 (no Fastly); wildcard cert *.teamgamdom.com (Let's Encrypt).
[LEARN] ACCEPTED fleet consistency: all 6 live hosts /client-api md5 7e3a161d — byte-identical, shared origin stable (9th cycle).
[LEARN] ACCEPTED watch @ pending aliases (80001/80002/90472/90473/90475/90480/90482/90488): 9th cycle, all still 421/TLS-NOMATCH — no cert deployment.
[RISK] gamdom:73 — Cross-brand cookie-replay chain remains the critical path (confidence 70, 9 cycles of evidence accumulation). 3 brands / 21+ hostnames share single Starlette identity/wallet backend with server-set same-origin cookie auth. Fleet churn (2 rotation events in 48h) does not affect origin homogeneity. Tableau version disclosure correlated against known CVEs — both patched (2025.1.11 > 2025.1.3); impact downgraded to informational. Kargo SPA catch-all uniformly masks backend (v1.9.6, direct origin, no CDN) — no JSON surface even with Accept header; parked until auth-assisted probe. 8 pending aliases stuck 421 in 9th cycle while 2 rotations occurred in 8000x pipeline — split-pipeline evidence persists. Risk steady at 73.
## 2026-09-10 21:25:51 UTC [target] (model bigpickle)
[PRIO] gamdom.com/client-api,8.5,a=10,b=10,t=7,g=4,f=6   # shared origin 3 brands / 21+ hosts, cookie-auth replay
[PRIO] fatbets.com/client-api,8.0,a=9,b=9,t=7,g=4,f=6     # same shared origin, cross-brand auth flow
[PRIO] 90488→shreeram (alias fleet),5.5,a=6,b=8,t=5,g=7,f=6 # split-pipeline misbinding watch
[PRIO] kargo.teamgamdom.com,5.5,a=6,b=8,t=5,g=6,f=7       # GitOps SPA, POST-gated, direct origin
[HYP] Cross-brand cookie replay via shared /client-api origin
class: AUTH
asset: gamdom.com/client-api (serves gamdom + fatbets + gamdom.one)
confidence: 70
reasoning: Byte-identical /client-api (md5 7e3a161d) across 7 hosts, 10th continuous cycle; single Starlette identity/wallet backend; server-set same-origin cookie auth (proven from client JS); CORS ACAO absent → browser replay blocked, backend-level acceptance untested; 2 fleet rotations (80008/80009) with static origin signature
evidence_needed: authenticated Set-Cookie from one brand + replay POST to another brand with same cookie
verify_steps: AUTH_HELPED only
impact: ATO across 3 brands / 21+ hostnames; critical
testability: AUTH_HELPED
[HYP] Provisioned alias cert lands with origin misbinding to shreeram Basic-gated backend
class: MISCONFIG
asset: gamdom90488.com → shreeram-dynamic-test.teamgamdom.com; fleet 90472/90473/90475/90480/90482/80001/80002
confidence: 50
reasoning: 10th cycle all 8 aliases 421 while 2 rotations (80008, 80009) landed certs in 48h — split 8000x vs 9047x pipeline persists; 90488 uniquely pins to Basic-gated nginx test backend; shreeram gate still 401 fixture-wide
evidence_needed: any pending alias returns non-421; or shreeram gate-scope regression to non-401
verify_steps: PASSIVE — `curl -sSk -o /dev/null -w \"%{http_code}\" https://gamdom90488.com/client-api` (repeat cycle) + `curl -sS -o /dev/null -w \"%{http_code}\" https://shreeram-dynamic-test.teamgamdom.com/`
impact: brand DNS → internal Basic-auth nginx backend; medium-high if gate-scope regression
testability: PASSIVE + monitoring
[HYP] Kargo GitOps API enumeration behind SPA catch-all
class: MISCONFIG
asset: kargo.teamgamdom.com
confidence: 25
reasoning: All GET → SPA text/html even with Accept: application/json; POST → 405 method-gate; v1.9.6 direct origin 77.42.9.222 (no Fastly); no JSON surface without auth
evidence_needed: any endpoint returning JSON unauthenticated; auth-assisted route discovery
verify_steps: AUTH_HELPED enumeration of /api/v1/* with session
impact: internal GitOps/CD exposure; severe if routes open
testability: AUTH_HELPED
[NEXT] PROBE: `curl -sSk https://gamdommirrors.com/api/status-page/gamdom-domains` (now-JSON Kuma endpoint) — track monitor-list + heartbeat config as authoritative fleet state each cycle, and re-run the 8-alias 421 watch (`curl -sSk -o /dev/null -w "%{http_code}" https://gamdom80001.com/client-api ... gamdom90488.com/client-api`) to catch the first pending alias going live, which is the falsification point for the split-pipeline misbinding hypothesis.
[RISK] gamdom: 73 — Cross-brand cookie replay (confidence 70) remains the only critical path, gated strictly by AUTH_HELPED; fleet + provisioning stable for 10th cycle with no new surface; newly-working Kuma JSON endpoint adds authoritative passive fleet state but no new risk. Kargo/Tableau surfaces closed (no unauthenticated data). No delta pushes risk either direction.
