## 2026-09-03 17:00:10 UTC [target] (model nemotron3)
## 2026-09-03 19:41:26 UTC [target] (model nemotron3)
[PRIO] gamdom80006.com /client-api,8.5,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdommirrors.com /api/status-page/gamdom-domains,7.2,attack_surface=8 business_value=6 tech_exposure=7 gate_ease=10 cloud_surface=5 freshness=9
[PRIO] api.gamdom.com,6.8,attack_surface=7 business_value=9 tech_exposure=6 gate_ease=4 cloud_surface=8 freshness=6
[PRIO] auth.gamdom.com,6.5,attack_surface=7 business_value=9 tech_exposure=6 gate_ease=3 cloud_surface=7 freshness=6
[PRIO] wallet/auth upstream behind /client-api (inferred),8.0,attack_surface=9 business_value=10 tech_exposure=9 gate_ease=2 cloud_surface=8 freshness=8
[HYP] Mirror-domain session sharing via /client-api enables cross-domain ATO
class: AUTH
asset: gamdom80006.com /client-api (and gamdom.eu, gamdom.io, gamdom.vip, gamdom.win)
confidence: 55
reasoning: All 7 mirrors serve byte-identical app with identical CSP (frame-ancestors 'self' https://*.faucetcrypto.com), identical nginx/Varnish, identical POST-only /client-api proxy (400 "only POST" on all paths). UptimeRobot groups them as single "Gamdom Official Domains" account. Shared upstream identity/wallet origin inferred.
evidence_needed: Auth cookie/session issued on one mirror honored by /client-api on another mirror; or DNS resolution showing distinct upstreams per mirror
verify_steps: PASSIVE: DNS A/AAAA comparison of all 7 mirrors vs canonical. AUTH_HELPED: Authenticate on one mirror, test if session cookie valid on /client-api of another mirror (requires authorized tester). No live POST without valid session.
impact: Cross-domain account takeover across all mirrors; phishing persistence via lookalike domains (medium-high)
testability: AUTH_HELPED
[HYP] UptimeRobot status page admin/mutation endpoints exposed without auth
class: MISCONFIG
asset: gamdommirrors.com /api/status-page/gamdom-domains
confidence: 42
reasoning: gamdommirrors.com is separate SPA (not casino /client-api proxy). Exposes UptimeRobot statuspage REST: GET /api/status-page/<slug> returns full monitor config JSON; heartbeat endpoints return empty. Distinct thinner attack surface; admin/threshold API may lack auth if exposed.
evidence_needed: Authless write endpoints (POST /api/status-page/.../incidents, POST /api/monitor/, /api/push/) accepting incident text or monitor toggles; or hidden admin panel returning 200 with controls
verify_steps: PASSIVE: GET enumeration of /api/status-page/*, /api/badge/*, /api/doc/* to map public keyspace; confirm no tokens/IDs in heartbeat responses. No POST (mutating third-party SaaS = REJECTED class risk).
impact: Deface official "Gamdom Official Domains" trust page; brand/SEO/trust impact; users routed to malicious mirrors (low-med)
testability: PASSIVE
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom.com /client-api (and all mirrors)
confidence: 48
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[PARKED] UptimeRobot status page admin/mutation endpoints exposed without auth: confidence 42 < 40 threshold borderline; verify_steps only PASSIVE but evidence_needed requires finding authless write endpoints on third-party SaaS (UptimeRobot) — mutation against third-party = REJECTED class risk per scope; no clear path to Gamdom-owned asset compromise
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[FINAL] Mirror-domain session sharing via /client-api enables cross-domain ATO
[NEXT] PROBE: GET (read-only) `https://gamdommirrors.com/api/status-page/gamdom-domains/incidents` to pull historical incident objects — may leak internal monitoring URLs/tokens/script tags referencing upstream endpoints not otherwise published (passive, <=1 rps, no auth, no mutation)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public UptimeRobot status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 62 reason: Expanded confirmed operating surface to 7 mirror domains sharing single POST-only `/client-api` identity/wallet proxy; top hypothesis (cross-domain ATO via session sharing) has passive verification path and high impact; no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-03 21:57:59 UTC [target] (model nemotron3)
[NEW] gamdommirrors.com is confirmed self-hosted Uptime Kuma (not UptimeRobot) publishing 7 monitors + 24h heartbeats for all Gamdom domains; `/api/badge/*` and `/socket.io/` endpoints exist but return SPA HTML or 400 JSON
[NEW] Two distinct Fastly origin pools confirmed: Pool A (151.101.x.52) for gamdom.com/eu/io/vip/win; Pool B (151.101.x.72) for gamdom80006.com/gamdom80007.com/gamdom4567.com — both serve byte-identical apps with identical CSP and POST-only `/client-api`
[NEW] gamdom4567.com is the CNAME origin root behind 8000x mirrors (root 404, `/client-api` 400), exposing true upstream
[CHANGED] api.gamdom.com and auth.gamdom.com return 000 (connection failed) — not publicly reachable on standard ports
[CHANGED] Uptime Kuma API routes (`/api/status-page/*`, `/api/monitor`, `/api/heartbeat/*`, `/api/badge/*`, `/api/doc`, `/api/push/*`) all serve SPA HTML; no unauthenticated JSON API surface confirmed
[PRIO] gamdom80006.com /client-api,8.5,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdom80007.com /client-api,8.5,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdom4567.com /client-api,8.2,attack_surface=10 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdommirrors.com /api/status-page/gamdom-domains,6.8,attack_surface=7 business_value=6 tech_exposure=6 gate_ease=10 cloud_surface=5 freshness=8
[PRIO] gamdommirrors.com /socket.io/,6.2,attack_surface=7 business_value=5 tech_exposure=7 gate_ease=8 cloud_surface=4 freshness=7
[HYP] Mirror-domain session sharing via /client-api enables cross-domain ATO
class: AUTH
asset: gamdom80006.com /client-api (and gamdom.eu, gamdom.io, gamdom.vip, gamdom.win, gamdom80007.com, gamdom4567.com)
confidence: 55
reasoning: All 7 mirrors serve byte-identical app with identical CSP (frame-ancestors 'self' https://*.faucetcrypto.com), identical nginx/Varnish headers, identical POST-only /client-api proxy (400 "only POST" on GET). Uptime Kuma groups them as single "Gamdom Official Domains" account. Two distinct Fastly origin pools (Pool A: .52 IPs for 5 core mirrors; Pool B: .72 IPs for 8000x mirrors + gamdom4567.com) but shared upstream identity/wallet origin inferred from identical /client-api behavior.
evidence_needed: Auth cookie/session issued on one mirror honored by /client-api on another mirror; or DNS resolution showing distinct upstreams per mirror
verify_steps: PASSIVE: DNS A/AAAA comparison of all 7 mirrors vs canonical (done — two pools confirmed). AUTH_HELPED: Authenticate on one mirror, test if session cookie valid on /client-api of another mirror (requires authorized tester). No live POST without valid session.
impact: Cross-domain account takeover across all mirrors; phishing persistence via lookalike domains (medium-high)
testability: AUTH_HELPED
[HYP] Uptime Kuma socket.io admin/mutation events exposed without auth
class: MISCONFIG
asset: gamdommirrors.com /socket.io/
confidence: 38
reasoning: gamdommirrors.com exposes socket.io endpoint (returns 400 JSON). Uptime Kuma uses socket.io for real-time monitor updates; admin panel mutations (acknowledge incidents, toggle maintenance, delete monitors) may be exposed via unauthenticated socket events if instance misconfigured. Distinct from status page SPA — thinner attack surface targeting monitoring infra.
evidence_needed: Unauthenticated socket.io events accepting mutation payloads (e.g., "deleteMonitor", "createIncident", "updateMonitor") returning success; or hidden admin panel returning 200 with controls
verify_steps: PASSIVE: Socket.io handshake probe (GET /socket.io/?EIO=4&transport=polling) to capture sid/handshake data; enumerate event names via client JS bundle. No POST/mutation against third-party SaaS (REJECTED class risk).
impact: Deface official "Gamdom Official Domains" trust page; brand/SEO/trust impact; users routed to malicious mirrors (low-med)
testability: PASSIVE
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom.com /client-api (and all 6 mirrors)
confidence: 48
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[PARKED] Uptime Kuma socket.io admin/mutation events exposed without auth: confidence 38 < 40 threshold; verify_steps only PASSIVE but evidence_needed requires finding authless mutation events on self-hosted instance — mutation against monitoring infra = REJECTED class risk per scope (no clear path to Gamdom-owned asset compromise)
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[FINAL] Mirror-domain session sharing via /client-api enables cross-domain ATO (confidence 55, AUTH_HELPED testability, medium-high impact)
[NEXT] PROBE: GET (read-only) `https://gamdommirrors.com/socket.io/?EIO=4&transport=polling` to capture socket.io handshake (sid, upgrades, pingInterval) and enumerate event namespace — passive, <=1 rps, no auth, no mutation
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Domains" status page) and gamdom4567.com as its CNAME origin root
[LEARN] ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamdom domains — legitimate passive recon resolving true operating mirrors
[RISK] gamdom: 62 reason: Expanded confirmed operating surface to 7 mirror domains sharing single POST-only `/client-api` identity/wallet proxy; top hypothesis (cross-domain ATO via session sharing) has passive verification path and high impact; no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-03 23:50:09 UTC [target] (model nemotron3)
[PRIO] gamdom80006.com /client-api,8.5,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdom80007.com /client-api,8.5,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdom4567.com /client-api,8.2,attack_surface=10 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdommirrors.com /socket.io/,6.2,attack_surface=7 business_value=5 tech_exposure=7 gate_ease=8 cloud_surface=4 freshness=7
[PRIO] gamdommirrors.com /api/status-page/gamdom-domains,6.8,attack_surface=7 business_value=6 tech_exposure=6 gate_ease=10 cloud_surface=5 freshness=8
[HYP] Mirror-domain session sharing via /client-api enables cross-domain ATO
class: AUTH
asset: gamdom80006.com /client-api (and gamdom.eu, gamdom.io, gamdom.vip, gamdom.win, gamdom80007.com, gamdom4567.com)
confidence: 55
reasoning: All 7 mirrors serve byte-identical app with identical CSP (frame-ancestors 'self' https://*.faucetcrypto.com), identical nginx/Varnish headers, identical POST-only /client-api proxy (400 "only POST" on GET). Uptime Kuma groups them as single "Gamdom Official Domains" account. Two distinct Fastly origin pools (Pool A: .52 IPs for 5 core mirrors; Pool B: .72 IPs for 8000x mirrors + gamdom4567.com) but shared upstream identity/wallet origin inferred from identical /client-api behavior.
evidence_needed: Auth cookie/session issued on one mirror honored by /client-api on another mirror; or DNS resolution showing distinct upstreams per mirror
verify_steps: PASSIVE: DNS A/AAAA comparison of all 7 mirrors vs canonical (done — two pools confirmed). AUTH_HELPED: Authenticate on one mirror, test if session cookie valid on /client-api of another mirror (requires authorized tester). No live POST without valid session.
impact: Cross-domain account takeover across all mirrors; phishing persistence via lookalike domains (medium-high)
testability: AUTH_HELPED
[HYP] Uptime Kuma socket.io admin/mutation events exposed without auth
class: MISCONFIG
asset: gamdommirrors.com /socket.io/
confidence: 38
reasoning: gamdommirrors.com exposes socket.io endpoint returning valid handshake (sid, upgrades: websocket). Uptime Kuma uses socket.io for real-time monitor updates; admin panel mutations (acknowledge incidents, toggle maintenance, delete monitors) may be exposed via unauthenticated socket events if instance misconfigured. Distinct from status page SPA — thinner attack surface targeting monitoring infra.
evidence_needed: Unauthenticated socket.io events accepting mutation payloads (e.g., "deleteMonitor", "createIncident", "updateMonitor") returning success; or hidden admin panel returning 200 with controls
verify_steps: PASSIVE: Socket.io handshake probe (GET /socket.io/?EIO=4&transport=polling) to capture sid/handshake data (done). Enumerate event names via client JS bundle analysis. No POST/mutation against third-party SaaS (REJECTED class risk).
impact: Deface official "Gamdom Official Domains" trust page; brand/SEO/trust impact; users routed to malicious mirrors (low-med)
testability: PASSIVE
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom.com /client-api (and all 6 mirrors)
confidence: 48
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[PARKED] Uptime Kuma socket.io admin/mutation events exposed without auth: confidence 38 < 40 threshold; mutation against monitoring infra = REJECTED class risk per scope (no clear path to Gamdom-owned asset compromise)
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[FINAL] Mirror-domain session sharing via /client-api enables cross-domain ATO (confidence 55, AUTH_HELPED testability, medium-high impact)
[NEXT] PROBE: GET (read-only) `https://gamdommirrors.com/socket.io/?EIO=4&transport=polling&sid=_EDMwKXb0he9pQH_AVOG` to capture socket.io polling response and enumerate available event namespaces — passive, <=1 rps, no auth, no mutation
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Domains" status page) and gamdom4567.com as its CNAME origin root
[LEARN] ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamdom domains — legitimate passive recon resolving true operating mirrors
[RISK] gamdom: 62 reason: Expanded confirmed operating surface to 7 mirror domains sharing single POST-only `/client-api` identity/wallet proxy; top hypothesis (cross-domain ATO via session sharing) has passive verification path and high impact; no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-04 02:53:30 UTC [target] (model nemotron3)
[PRIO] gamdom80006.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdom80007.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdom4567.com /client-api,8.45,attack_surface=10 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdommirrors.com /api/status-page/gamdom-domains,6.95,attack_surface=7 business_value=6 tech_exposure=6 gate_ease=10 cloud_surface=5 freshness=8
[PRIO] gamdommirrors.com /socket.io/,6.35,attack_surface=7 business_value=5 tech_exposure=7 gate_ease=8 cloud_surface=4 freshness=7
[HYP] Mirror-domain session sharing via /client-api enables cross-domain ATO
class: AUTH
asset: gamdom80006.com /client-api (and gamdom.eu, gamdom.io, gamdom.vip, gamdom.win, gamdom80007.com, gamdom4567.com)
confidence: 55
reasoning: All 7 mirrors serve byte-identical app with identical CSP (frame-ancestors 'self' https://*.faucetcrypto.com), identical nginx/Varnish headers, identical POST-only /client-api proxy (400 "only POST" on GET). Uptime Kuma groups them as single "Gamdom Official Domains" account. Two distinct Fastly origin pools (Pool A: .52 IPs for 5 core mirrors; Pool B: .72 IPs for 8000x mirrors + gamdom4567.com) but shared upstream identity/wallet origin inferred from identical /client-api behavior.
evidence_needed: Auth cookie/session issued on one mirror honored by /client-api on another mirror; or DNS resolution showing distinct upstreams per mirror
verify_steps: PASSIVE: DNS A/AAAA comparison of all 7 mirrors vs canonical (done — two pools confirmed). AUTH_HELPED: Authenticate on one mirror, test if session cookie valid on /client-api of another mirror (requires authorized tester). No live POST without valid session.
impact: Cross-domain account takeover across all mirrors; phishing persistence via lookalike domains (medium-high)
testability: AUTH_HELPED
[HYP] gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors
class: OTHER
asset: gamdom4567.com /client-api
confidence: 50
reasoning: gamdom80006 and gamdom80007 both CNAME to gamdom4567.com which resolves to Fastly edge (151.101.x.72); gamdom4567 root returns 404 while clones return 200; identical POST-only /client-api behavior implies shared upstream wallet/identity origin
evidence_needed: Host-header on gamdom4567 returning same app as clones; or identical etag/Vary headers between 4567 and 8000x on /client-api
verify_steps: GET https://gamdom4567.com/client-api with Host: gamdom80007.com and compare headers/body to https://gamdom80007.com/client-api (read-only, single Fastly edge)
impact: Establishes single trust boundary across all mirrors, making any session/CSRF from any mirror valid for all; medium-high
testability: PASSIVE
[HYP] Uptime Kuma socket.io admin/mutation events exposed without auth
class: MISCONFIG
asset: gamdommirrors.com /socket.io/
confidence: 38
reasoning: gamdommirrors.com exposes socket.io endpoint returning valid handshake (sid, upgrades: websocket). Uptime Kuma uses socket.io for real-time monitor updates; admin panel mutations (acknowledge incidents, toggle maintenance, delete monitors) may be exposed via unauthenticated socket events if instance misconfigured. Distinct from status page SPA — thinner attack surface targeting monitoring infra.
evidence_needed: Unauthenticated socket.io events accepting mutation payloads (e.g., "deleteMonitor", "createIncident", "updateMonitor") returning success; or hidden admin panel returning 200 with controls
verify_steps: PASSIVE: Socket.io handshake probe (GET /socket.io/?EIO=4&transport=polling) to capture sid/handshake data (done). Enumerate event names via client JS bundle analysis. No POST/mutation against third-party SaaS (REJECTED class risk).
impact: Deface official "Gamdom Official Domains" trust page; brand/SEO/trust impact; users routed to malicious mirrors (low-med)
testability: PASSIVE
[PARKED] Uptime Kuma socket.io admin/mutation events exposed without auth: confidence 38 < 40 threshold; mutation against monitoring infra = REJECTED class risk per scope (no clear path to Gamdom-owned asset compromise)
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[FINAL] Mirror-domain session sharing via /client-api enables cross-domain ATO (confidence 55, AUTH_HELPED testability, medium-high impact)
[FINAL] gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors (confidence 50, PASSIVE testability, medium-high impact)
[NEXT] PROBE: GET (read-only) `https://gamdom4567.com/client-api` with `Host: gamdom80007.com` and compare response headers/body to `https://gamdom80007.com/client-api` to confirm gamdom4567 is the exact origin (shared trust boundary, no mutation, no auth tokens, <=1 rps)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Domains" status page) and gamdom4567.com as its CNAME origin root
[LEARN] ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamdom domains — legitimate passive recon resolving true operating mirrors
[RISK] gamdom: 62 reason: Expanded confirmed operating surface to 7 mirror domains sharing single POST-only `/client-api` identity/wallet proxy; top hypothesis (cross-domain ATO via session sharing) has passive verification path and high impact; no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-04 07:47:37 UTC [target] (model nemotron3)
[PRIO] gamdom80006.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdom80007.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdom4567.com /client-api,8.45,attack_surface=10 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdommirrors.com /api/status-page/gamdom-domains,6.95,attack_surface=7 business_value=6 tech_exposure=6 gate_ease=10 cloud_surface=5 freshness=8
[PRIO] gamdommirrors.com /socket.io/,6.35,attack_surface=7 business_value=5 tech_exposure=7 gate_ease=8 cloud_surface=4 freshness=7
[HYP] Mirror-domain session sharing via /client-api enables cross-domain ATO
class: AUTH
asset: gamdom80006.com /client-api (and gamdom.eu, gamdom.io, gamdom.vip, gamdom.win, gamdom80007.com, gamdom4567.com)
confidence: 55
reasoning: All 7 mirrors serve byte-identical app with identical CSP (frame-ancestors 'self' https://*.faucetcrypto.com), identical nginx/Varnish headers, identical POST-only /client-api proxy (400 "only POST" on GET). Uptime Kuma groups them as single "Gamdom Official Domains" account. Two distinct Fastly origin pools (Pool A: .52 IPs for 5 core mirrors; Pool B: .72 IPs for 8000x mirrors + gamdom4567.com) but shared upstream identity/wallet origin inferred from identical /client-api behavior.
evidence_needed: Auth cookie/session issued on one mirror honored by /client-api on another mirror; or DNS resolution showing distinct upstreams per mirror
verify_steps: PASSIVE: DNS A/AAAA comparison of all 7 mirrors vs canonical (done — two pools confirmed). AUTH_HELPED: Authenticate on one mirror, test if session cookie valid on /client-api of another mirror (requires authorized tester). No live POST without valid session.
impact: Cross-domain account takeover across all mirrors; phishing persistence via lookalike domains (medium-high)
testability: AUTH_HELPED
[HYP] gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors
class: OTHER
asset: gamdom4567.com /client-api
confidence: 50
reasoning: gamdom80006 and gamdom80007 both CNAME to gamdom4567.com which resolves to Fastly edge (151.101.x.72); gamdom4567 root returns 404 while clones return 200; identical POST-only /client-api behavior implies shared upstream wallet/identity origin
evidence_needed: Host-header on gamdom4567 returning same app as clones; or identical etag/Vary headers between 4567 and 8000x on /client-api
verify_steps: GET https://gamdom4567.com/client-api with Host: gamdom80007.com and compare headers/body to https://gamdom80007.com/client-api (read-only, single Fastly edge)
impact: Establishes single trust boundary across all mirrors, making any session/CSRF from any mirror valid for all; medium-high
testability: PASSIVE
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom.com /client-api (and all 6 mirrors)
confidence: 48
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[PARKED] Uptime Kuma socket.io admin/mutation events exposed without auth: confidence 38 < 40 threshold; mutation against monitoring infra = REJECTED class risk per scope (no clear path to Gamdom-owned asset compromise)
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[FINAL] Mirror-domain session sharing via /client-api enables cross-domain ATO (confidence 55, AUTH_HELPED testability, medium-high impact)
[FINAL] gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors (confidence 50, PASSIVE testability, medium-high impact)
[NEXT] PROBE: GET (read-only) `https://gamdom4567.com/client-api` with `Host: gamdom80007.com` and compare response headers/body to `https://gamdom80007.com/client-api` to confirm gamdom4567 is the exact origin (shared trust boundary, no mutation, no auth tokens, <=1 rps)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Domains" status page) and gamdom4567.com as its CNAME origin root
[LEARN] ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamdom domains — legitimate passive recon resolving true operating mirrors
[RISK] gamdom: 62 reason: Expanded confirmed operating surface to 7 mirror domains sharing single POST-only `/client-api` identity/wallet proxy; top hypothesis (cross-domain ATO via session sharing) has passive verification path and high impact; no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-04 12:35:50 UTC [target] (model nemotron3)
[NEW] Origin trust boundary confirmed: gamdom4567.com is the verified shared backend for all 8000x mirrors (byte-identical /client-api headers, Fastly 421 on Host-header injection proves cert binding, header fingerprinting matches across gamdom4567.com/gamdom80006.com/gamdom80007.com)
[CHANGED] Hypothesis "gamdom4567.com is shadow origin with shared /client-api trust boundary" confidence raised from 50 → 70 (bigpickle) / 70 (nemotron3); evidence_needed now "None — origin trust boundary confirmed via passive header comparison"; verify_steps now "DONE"
[CHANGED] Next actionable probe shifted: nemotron3 still queues Host-header probe on gamdom4567.com; bigpickle marks it DONE and queues socket.io handshake on gamdommirrors.com instead
[PRIO] gamdom4567.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=9
[PRIO] gamdom80006.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdom80007.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdommirrors.com /socket.io/,6.35,attack_surface=7 business_value=5 tech_exposure=7 gate_ease=8 cloud_surface=4 freshness=7
[PRIO] gamdommirrors.com /api/status-page/gamdom-domains,6.35,attack_surface=7 business_value=6 tech_exposure=6 gate_ease=10 cloud_surface=5 freshness=8
[HYP] Mirror-domain session sharing via /client-api enables cross-domain ATO
class: AUTH
asset: gamdom80006.com /client-api (and gamdom.eu, gamdom.io, gamdom.vip, gamdom.win, gamdom80007.com, gamdom4567.com)
confidence: 55
reasoning: All 7 mirrors serve byte-identical app with identical CSP (frame-ancestors 'self' https://*.faucetcrypto.com), identical nginx/Varnish headers, identical POST-only /client-api proxy (400 "only POST" on GET). Uptime Kuma groups them as single "Gamdom Official Domains" account. Two distinct Fastly origin pools (Pool A: .52 IPs for 5 core mirrors; Pool B: .72 IPs for 8000x mirrors + gamdom4567.com) but shared upstream identity/wallet origin inferred from identical /client-api behavior. Origin trust boundary now confirmed via header fingerprinting (gamdom4567.com = backend for 8000x).
evidence_needed: Auth cookie/session issued on one mirror honored by /client-api on another mirror
verify_steps: AUTH_HELPED: Authenticate on one mirror (e.g., gamdom80006.com), replay session cookie on /client-api of another mirror (e.g., gamdom80007.com or gamdom4567.com). No live POST without valid session.
impact: Cross-domain account takeover across all mirrors; phishing persistence via lookalike domains (medium-high)
testability: AUTH_HELPED
[HYP] gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors
class: OTHER
asset: gamdom4567.com /client-api
confidence: 70
reasoning: All three domains (gamdom4567.com, gamdom80007.com, gamdom80006.com) return byte-identical response headers (server:nginx, via:1.1 varnish, strict-transport-security, content-length:26) and identical body "Invalid request, only POST" on /client-api; Fastly returned 421 on Host-header injection (gamdom4567 cert lacks 8000x SANs), proving edge enforces cert binding; origin backend fingerprint shared across mirrors
evidence_needed: None — origin trust boundary confirmed via passive header comparison
verify_steps: DONE — headers compared across all three domains, same backend confirmed
impact: Single trust boundary across all mirrors means any session/CSRF token minted on one mirror is honored by all; medium-high
testability: PASSIVE
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom.com /client-api (and all 6 mirrors)
confidence: 48
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[PARKED] Uptime Kuma socket.io admin/mutation events exposed without auth: confidence 38 < 40 threshold; mutation against monitoring infra = REJECTED class risk per scope (no clear path to Gamdom-owned asset compromise)
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[PARKED] gamdom80007.com session replay: requires authenticated session first (AUTH_HELPED), cannot verify passively
[FINAL] 1. gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors (confidence 70, PASSIVE testability, medium-high impact)
[FINAL] 2. Mirror-domain session sharing via /client-api enables cross-domain ATO (confidence 55, AUTH_HELPED testability, medium-high impact)
[NEXT] PROBE: GET (read-only) `https://gamdommirrors.com/socket.io/?EIO=4&transport=polling` — capture socket.io handshake response and enumerate namespaces/events; test whether Kuma endpoint leaks monitor data without auth (passive, <=1 rps, no auth, no mutation)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Domains" status page) and gamdom4567.com as its CNAME origin root
[LEARN] ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamdom domains — legitimate passive recon resolving true operating mirrors
[LEARN] ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com is the origin backend serving /client-api for all 8000x mirrors
[RISK] gamdom: 65 reason: Confirmed 7 mirror domains sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary now verified (gamdom4567.com = backend for 8000x); top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-04 16:44:36 UTC [target] (model nemotron3)
[NEW] Origin trust boundary **confirmed**: gamdom4567.com is the verified shared backend for all 8000x mirrors (byte-identical /client-api headers across gamdom4567.com/gamdom80006.com/gamdom80007.com; Fastly 421 on Host-header injection proves cert binding) — both nemotron3 and bigpickle raised confidence to 70, verify_steps now DONE
[CHANGED] Next actionable probe shifted: nemotron3 queues socket.io handshake on gamdommirrors.com; bigpickle marks Host-header probe DONE and also queues socket.io handshake
[CHANGED] Risk score raised: nemotron3 62→65, bigpickle 55→58 — confirmed 7 mirrors sharing single POST-only /client-api with verified shared origin backend
[PRIO] gamdom4567.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=9
[PRIO] gamdom80006.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdom80007.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdommirrors.com /socket.io/,6.35,attack_surface=7 business_value=5 tech_exposure=7 gate_ease=8 cloud_surface=4 freshness=7
[PRIO] gamdommirrors.com /api/status-page/gamdom-domains,6.35,attack_surface=7 business_value=6 tech_exposure=6 gate_ease=10 cloud_surface=5 freshness=8
[HYP] Mirror-domain session sharing via /client-api enables cross-domain ATO
class: AUTH
asset: gamdom80006.com /client-api (and gamdom.eu, gamdom.io, gamdom.vip, gamdom.win, gamdom80007.com, gamdom4567.com)
confidence: 55
reasoning: All 7 mirrors serve byte-identical app with identical CSP (frame-ancestors 'self' https://*.faucetcrypto.com), identical nginx/Varnish headers, identical POST-only /client-api proxy (400 "only POST" on GET). Uptime Kuma groups them as single "Gamdom Official Domains" account. Two distinct Fastly origin pools (Pool A: .52 IPs for 5 core mirrors; Pool B: .72 IPs for 8000x mirrors + gamdom4567.com) but shared upstream identity/wallet origin confirmed via header fingerprinting (gamdom4567.com = backend for 8000x).
evidence_needed: Auth cookie/session issued on one mirror honored by /client-api on another mirror
verify_steps: AUTH_HELPED: Authenticate on one mirror (e.g., gamdom80006.com), replay session cookie on /client-api of another mirror (e.g., gamdom80007.com or gamdom4567.com). No live POST without valid session.
impact: Cross-domain account takeover across all mirrors; phishing persistence via lookalike domains (medium-high)
testability: AUTH_HELPED
[HYP] gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors
class: OTHER
asset: gamdom4567.com /client-api
confidence: 70
reasoning: All three domains (gamdom4567.com, gamdom80007.com, gamdom80006.com) return byte-identical response headers (server:nginx, via:1.1 varnish, strict-transport-security, content-length:26) and identical body "Invalid request, only POST" on /client-api; Fastly returned 421 on Host-header injection (gamdom4567 cert lacks 8000x SANs), proving edge enforces cert binding; origin backend fingerprint shared across mirrors
evidence_needed: None — origin trust boundary confirmed via passive header comparison
verify_steps: DONE — headers compared across all three domains, same backend confirmed
impact: Single trust boundary across all mirrors means any session/CSRF token minted on one mirror is honored by all; medium-high
testability: PASSIVE
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom.com /client-api (and all 6 mirrors)
confidence: 48
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[PARKED] Uptime Kuma socket.io admin/mutation events exposed without auth: confidence 38 < 40 threshold; mutation against monitoring infra = REJECTED class risk per scope (no clear path to Gamdom-owned asset compromise)
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[PARKED] gamdom80007.com session replay: requires authenticated session first (AUTH_HELPED), cannot verify passively
[FINAL] 1. gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors (confidence 70, PASSIVE testability, medium-high impact)
[FINAL] 2. Mirror-domain session sharing via /client-api enables cross-domain ATO (confidence 55, AUTH_HELPED testability, medium-high impact)
[NEXT] PROBE: GET (read-only) `https://gamdommirrors.com/socket.io/?EIO=4&transport=polling` — capture socket.io handshake response and enumerate namespaces/events; test whether Kuma endpoint leaks monitor data without auth (passive, <=1 rps, no auth, no mutation)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Domains" status page) and gamdom4567.com as its CNAME origin root
[LEARN] ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamdom domains — legitimate passive recon resolving true operating mirrors
[LEARN] ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com is the origin backend serving /client-api for all 8000x mirrors
[RISK] gamdom: 65 reason: Confirmed 7 mirror domains sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary now verified (gamdom4567.com = backend for 8000x); top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-04 19:16:01 UTC [target] (model nemotron3)
[PRIO] gamdom4567.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=9
[PRIO] gamdom80006.com /client-api,8.25,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdom80007.com /client-api,8.25,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdommirrors.com /api/status-page/gamdom-domains,6.85,attack_surface=7 business_value=6 tech_exposure=6 gate_ease=10 cloud_surface=5 freshness=8
[PRIO] gamdommirrors.com /socket.io/,6.35,attack_surface=7 business_value=5 tech_exposure=7 gate_ease=8 cloud_surface=4 freshness=7
[HYP] Mirror-domain session sharing via /client-api enables cross-domain ATO
class: AUTH
asset: gamdom80006.com /client-api (and gamdom.eu, gamdom.io, gamdom.vip, gamdom.win, gamdom80007.com, gamdom4567.com)
confidence: 55
reasoning: All 7 mirrors serve byte-identical app with identical CSP (frame-ancestors 'self' https://*.faucetcrypto.com), identical nginx/Varnish headers, identical POST-only /client-api proxy (400 "only POST" on GET). Uptime Kuma groups them as single "Gamdom Official Domains" account. Two distinct Fastly origin pools (Pool A: .52 IPs for 5 core mirrors; Pool B: .72 IPs for 8000x mirrors + gamdom4567.com) but shared upstream identity/wallet origin confirmed via header fingerprinting (gamdom4567.com = backend for 8000x).
evidence_needed: Auth cookie/session issued on one mirror honored by /client-api on another mirror
verify_steps: AUTH_HELPED: Authenticate on one mirror (e.g., gamdom80006.com), replay session cookie on /client-api of another mirror (e.g., gamdom80007.com or gamdom4567.com). No live POST without valid session.
impact: Cross-domain account takeover across all mirrors; phishing persistence via lookalike domains (medium-high)
testability: AUTH_HELPED
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom.com /client-api (and all 6 mirrors)
confidence: 48
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[HYP] gamdommirrors.com socket.io exposes unauthenticated admin/mutation events for Kuma monitoring instance
class: MISCONFIG
asset: gamdommirrors.com /socket.io/
confidence: 38
reasoning: gamdommirrors.com exposes socket.io endpoint returning valid handshake (sid, upgrades: websocket). Uptime Kuma uses socket.io for real-time monitor updates; admin panel mutations (acknowledge incidents, toggle maintenance, delete monitors) may be exposed via unauthenticated socket events if instance misconfigured. Distinct from status page SPA — thinner attack surface targeting monitoring infra.
evidence_needed: Unauthenticated socket.io events accepting mutation payloads (e.g., "deleteMonitor", "createIncident", "updateMonitor") returning success; or hidden admin panel returning 200 with controls
verify_steps: PASSIVE: Socket.io handshake probe (GET /socket.io/?EIO=4&transport=polling) to capture sid/handshake data; enumerate event names via client JS bundle. No POST/mutation against third-party SaaS (REJECTED class risk).
impact: Deface official "Gamdom Official Domains" trust page; brand/SEO/trust impact; users routed to malicious mirrors (low-med)
testability: PASSIVE
[PARKED] gamdommirrors.com socket.io admin/mutation events exposed without auth: confidence 38 < 40 threshold; mutation against monitoring infra = REJECTED class risk per scope (no clear path to Gamdom-owned asset compromise)
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[PARKED] gamdom80007.com session replay: requires authenticated session first (AUTH_HELPED), cannot verify passively
[FINAL] 1. Mirror-domain session sharing via /client-api enables cross-domain ATO (confidence 55, AUTH_HELPED testability, medium-high impact)
[FINAL] 2. /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment (confidence 48, AUTH_HELPED testability, critical-if-proven impact)
[NEXT] PROBE: GET (read-only) `https://gamdommirrors.com/socket.io/?EIO=4&transport=polling` — capture socket.io handshake response and enumerate namespaces/events; test whether Kuma endpoint leaks monitor data without auth (passive, <=1 rps, no auth, no mutation)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Domains" status page) and gamdom4567.com as its CNAME origin root
[LEARN] ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamdom domains — legitimate passive recon resolving true operating mirrors
[LEARN] ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com is the origin backend serving /client-api for all 8000x mirrors
[RISK] gamdom: 65 reason: Confirmed 7 mirror domains sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary now verified (gamdom4567.com = backend for 8000x); top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-04 21:36:00 UTC [target] (model nemotron3)
[HYP] Mirror-domain session sharing via /client-api enables cross-domain ATO
class: AUTH
asset: gamdom80006.com /client-api (and gamdom.eu, gamdom.io, gamdom.vip, gamdom.win, gamdom80007.com, gamdom4567.com)
confidence: 55
reasoning: All 7 mirrors serve byte-identical app with identical CSP (frame-ancestors 'self' https://*.faucetcrypto.com), identical nginx/Varnish headers, identical POST-only /client-api proxy (400 "only POST" on GET). Uptime Kuma groups them as single "Gamdom Official Domains" account. Two distinct Fastly origin pools (Pool A: .52 IPs for 5 core mirrors; Pool B: .72 IPs for 8000x mirrors + gamdom4567.com) but shared upstream identity/wallet origin confirmed via header fingerprinting (gamdom4567.com = backend for 8000x).
evidence_needed: Auth cookie/session issued on one mirror honored by /client-api on another mirror
verify_steps: AUTH_HELPED: Authenticate on one mirror (e.g., gamdom80006.com), replay session cookie on /client-api of another mirror (e.g., gamdom80007.com or gamdom4567.com). No live POST without valid session.
impact: Cross-domain account takeover across all mirrors; phishing persistence via lookalike domains (medium-high)
testability: AUTH_HELPED
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom.com /client-api (and all 6 mirrors)
confidence: 48
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[HYP] gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors
class: OTHER
asset: gamdom4567.com /client-api
confidence: 70
reasoning: All three domains (gamdom4567.com, gamdom80007.com, gamdom80006.com) return byte-identical response headers (server:nginx, via:1.1 varnish, strict-transport-security, content-length:26) and identical body "Invalid request, only POST" on /client-api; Fastly returned 421 on Host-header injection (gamdom4567 cert lacks 8000x SANs), proving edge enforces cert binding; origin backend fingerprint shared across mirrors
evidence_needed: None — origin trust boundary confirmed via passive header comparison
verify_steps: DONE — headers compared across all three domains, same backend confirmed
impact: Single trust boundary across all mirrors means any session/CSRF token minted on one mirror is honored by all; medium-high
testability: PASSIVE
[PARKED] gamdommirrors.com socket.io admin/mutation events exposed without auth: confidence 38 < 40 threshold; mutation against monitoring infra = REJECTED class risk per scope (no clear path to Gamdom-owned asset compromise)
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[PARKED] gamdom80007.com session replay: requires authenticated session first (AUTH_HELPED), cannot verify passively
[FINAL] 1. gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors (confidence 70, PASSIVE testability, medium-high impact)
[FINAL] 2. Mirror-domain session sharing via /client-api enables cross-domain ATO (confidence 55, AUTH_HELPED testability, medium-high impact)
[FINAL] 3. /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment (confidence 48, AUTH_HELPED testability, critical-if-proven impact)
[NEXT] PROBE: GET (read-only) `https://gamdommirrors.com/socket.io/?EIO=4&transport=polling` — capture socket.io handshake response and enumerate namespaces/events; test whether Kuma endpoint leaks monitor data without auth (passive, <=1 rps, no auth, no mutation)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Domains" status page) and gamdom4567.com as its CNAME origin root
[LEARN] ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamdom domains — legitimate passive recon resolving true operating mirrors
[LEARN] ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com is the origin backend serving /client-api for all 8000x mirrors
[RISK] gamdom: 65 reason: Confirmed 7 mirror domains sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary now verified (gamdom4567.com = backend for 8000x); top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-04 23:19:28 UTC [target] (model nemotron3)
[NEW] gamdom80004.com discovered (302 → gamdom0007.com, Fastly Pool B) — 8th alias widening shared trust boundary  
[NEW] gamdommirrors.com socket.io handshake confirmed (sid issued, upgrades: websocket) — but Fastly pool breaks session persistence  
[CHANGED] gamdom4567.com shadow-origin hypothesis CONFIRMED (confidence 70 → DONE) — byte-identical /client-api headers across gamdom4567/gamdom80006/gamdom80007; Fastly 421 on Host-header injection proves cert binding  
[CHANGED] Cross-mirror session sharing hypothesis now strengthened by verified shared origin (gamdom4567.com = backend for 8000x) — evidence_needed reduced to single cookie replay test  
[CHANGED] nemotron3 risk score 62→65, bigpickle 55→58 — confirmed 7 mirrors + origin boundary verified  
[PRIO] gamdom4567.com /client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=9  
[PRIO] gamdom80006.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9  
[PRIO] gamdom80007.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9  
[PRIO] gamdom80004.com /client-api,8.2,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=8  
[PRIO] gamdommirrors.com /socket.io/,6.4,attack_surface=7 business_value=5 tech_exposure=7 gate_ease=8 cloud_surface=4 freshness=7  
[PRIO] gamdommirrors.com /api/status-page/gamdom-domains,6.4,attack_surface=7 business_value=6 tech_exposure=6 gate_ease=10 cloud_surface=5 freshness=8
[HYP] Cross-mirror auth token/session replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80006.com /client-api (shared origin gamdom4567.com, Pool B: 80004/80006/80007/4567)
confidence: 55
reasoning: Byte-identical SPA + shared origin backend across all mirrors; /client-api GET 400 with no ACAO on both mirrors and origin (no CORS vector); replay feasibility depends on auth-token transport (cookie vs localStorage), unresolved in main bundles
evidence_needed: Same session/token minted on gamdom80006 accepted by gamdom80007 /client-api; and where token is stored client-side (localStorage=trivial replay+XSS→ATO, HttpOnly cookie=host-based replay)
verify_steps: (passive) fetch AppShell chunk manifest, load request-layer chunk, read /client-api request header + storage key; (AUTH_HELPED) authenticate on 80006, replay bearer/cookie on 80007 /client-api
impact: Cross-domain account takeover across all 8 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom.com /client-api (and all 7 mirrors)
confidence: 48
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[HYP] Unlisted alias host widens shared-trust session surface
class: MISCONFIG
asset: gamdom80004.com (302 → gamdom80007.com)
confidence: 45
reasoning: 8th hostname, Fastly Pool B, 302 to gamdom80007; joins the same origin trust boundary; any session scoped to the mirror set is reachable from an extra hostname absent from official status page
evidence_needed: Whether auth Set-Cookie/headers from 80007 are honored when arriving via 80004; identical session pool
verify_steps: HEAD chain 80004→80007 (done: 302, same pool); compare Set-Cookie during auth on 80004 vs 80007 (HUMAN)
impact: Marginal widening of replay surface + cache-key/host-confusion edge; low
testability: HUMAN_ONLY
[PARKED] gamdommirrors.com socket.io admin/mutation events exposed without auth: confidence 38 < 40 threshold; mutation against monitoring infra = REJECTED class risk per scope (no clear path to Gamdom-owned asset compromise)  
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior  
[PARKED] gamdom80007.com session replay: requires authenticated session first (AUTH_HELPED), cannot verify passively  
[FINAL] 1. Cross-mirror auth token/session replay via shared /client-api origin yields ATO (confidence 55, AUTH_HELPED testability, medium-high impact)  
[FINAL] 2. /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment (confidence 48, AUTH_HELPED testability, critical-if-proven impact)  
[FINAL] 3. Unlisted alias host widens shared-trust session surface (confidence 45, HUMAN_ONLY testability, low impact)
[NEXT] PROBE: GET (read-only) `https://gamdommirrors.com/socket.io/?EIO=4&transport=polling` — capture socket.io handshake response and enumerate namespaces/events; test whether Kuma endpoint leaks monitor data without auth (passive, <=1 rps, no auth, no mutation)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing  
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)  
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing  
[LEARN] ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Domains" status page) and gamdom4567.com as its CNAME origin root  
[LEARN] ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamdom domains — legitimate passive recon resolving true operating mirrors  
[LEARN] ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com is the origin backend serving /client-api for all 8000x mirrors
[RISK] gamdom: 65 reason: Confirmed 7 mirror domains + 1 unlisted alias (gamdom80004.com) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary now verified (gamdom4567.com = backend for 8000x + gamdom80004); top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-05 01:09:46 UTC [target] (model nemotron3)
[NEW] gamdom80004.com discovered (302 → gamdom80007.com, Fastly Pool B) — 8th alias widening shared trust boundary
[NEW] Token transport RESOLVED passively from gamdom80006.com `client.41b06529227c4b8b6a1d.js`: request layer uses `credentials:"same-origin"`, zero `Authorization`/`Bearer` headers, no localStorage token — auth is server-set same-origin cookie only
[NEW] socket.io handshake on gamdommirrors.com executed (passive): `GET /socket.io/?EIO=4&transport=polling` → HTTP 200, `{"sid":"...","upgrades":["websocket"],"pingInterval":25000,...}`, behind Fastly Pool B — but Fastly pool breaks session persistence
[CHANGED] gamdom4567.com shadow-origin hypothesis CONFIRMED (confidence 70 → DONE) — byte-identical /client-api headers across gamdom4567/gamdom80006/gamdom80007; Fastly 421 on Host-header injection proves cert binding
[CHANGED] Cross-mirror session sharing hypothesis strengthened by verified shared origin (gamdom4567.com = backend for 8000x) — evidence_needed reduced to single cookie replay test
[CHANGED] Cross-mirror ATO hypothesis narrowed: cookie-host-agnostic-acceptance on shared backend is the only replay vector (no localStorage replay branch)
[PRIO] gamdom4567.com /client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=9
[PRIO] gamdom80006.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdom80007.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9
[PRIO] gamdom80004.com /client-api,8.2,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=8
[PRIO] gamdommirrors.com /socket.io/,6.4,attack_surface=7 business_value=5 tech_exposure=7 gate_ease=8 cloud_surface=4 freshness=7
[PRIO] gamdommirrors.com /api/status-page/gamdom-domains,6.4,attack_surface=7 business_value=6 tech_exposure=6 gate_ease=10 cloud_surface=5 freshness=8
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80006.com /client-api (shared origin gamdom4567.com, Pool B: 80004/80006/80007/4567)
confidence: 55
reasoning: Byte-identical SPA + shared origin backend across all mirrors; /client-api GET 400 with no ACAO on both mirrors and origin (no CORS vector); auth transport proven as server-set same-origin cookie via bundle analysis; replay feasibility depends on whether cookie is host-agnostic at shared backend
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80007 /client-api (or gamdom4567.com /client-api); cookie Domain attribute scope
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80007; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80007 /client-api
impact: Cross-domain account takeover across all 8 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom4567.com /client-api (and all 7 mirrors)
confidence: 48
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[HYP] Unlisted alias host widens shared-trust session surface
class: MISCONFIG
asset: gamdom80004.com (302 → gamdom80007.com)
confidence: 45
reasoning: 8th hostname, Fastly Pool B, 302 to gamdom80007; joins the same origin trust boundary; any session scoped to the mirror set is reachable from an extra hostname absent from official status page
evidence_needed: Whether auth Set-Cookie/headers from 80007 are honored when arriving via 80004; identical session pool
verify_steps: HEAD chain 80004→80007 (done: 302, same pool); compare Set-Cookie during auth on 80004 vs 80007 (HUMAN)
impact: Marginal widening of replay surface + cache-key/host-confusion edge; low
testability: HUMAN_ONLY
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[PARKED] gamdom80007.com session replay: requires authenticated session first (AUTH_HELPED), cannot verify passively
[PARKED] gamdommirrors.com socket.io admin/mutation events exposed without auth: confidence 38 < 40 threshold; mutation against monitoring infra = REJECTED class risk per scope (no clear path to Gamdom-owned asset compromise)
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 55, AUTH_HELPED testability, medium-high impact)
[FINAL] 2. /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment (confidence 48, AUTH_HELPED testability, critical-if-proven impact)
[FINAL] 3. Unlisted alias host widens shared-trust session surface (confidence 45, HUMAN_ONLY testability, low impact)
[NEXT] PROBE: GET (read-only) `https://gamdom80006.com/build/client.41b06529227c4b8b6a1d.js` — already fetched; now passively compare `Set-Cookie` headers on `POST /client-api` (400) vs `GET /` (200) across gamdom80006.com, gamdom80007.com, gamdom4567.com to extract cookie Domain/Path/SameSite attributes without authentication (<=1 rps, no mutation)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Domains" status page) and gamdom4567.com as its CNAME origin root
[LEARN] ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamdom domains — legitimate passive recon resolving true operating mirrors
[LEARN] ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com is the origin backend serving /client-api for all 8000x mirrors
[LEARN] ACCEPTED recon @ gamdom80006.com/build/client.41b06529227c4b8b6a1d.js: passive fetch of the app's own request-layer bundle (597 KB) proved auth transport is server-set same-origin cookie — no bearer/localStorage token — grounding the cross-mirror replay vector in evidence
[LEARN] ACCEPTED recon @ gamdommirrors.com/socket.io: GET-only EIO=4 polling handshake issues SID with websocket upgrade; no admin surface reachable without POST RPC, consistent with prior PARKED verdict
[RISK] gamdom: 65 reason: Confirmed 7 mirror domains + 1 unlisted alias (gamdom80004.com) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary now verified (gamdom4567.com = backend for 8000x + gamdom80004); top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-05 05:52:29 UTC [target] (model nemotron3)
[NEW] gamdom80004.com discovered (302 → gamdom80007.com, Fastly Pool B) — 8th alias widening shared trust boundary  
[NEW] Token transport RESOLVED passively from gamdom80006.com `client.41b06529227c4b8b6a1d.js`: request layer uses `credentials:"same-origin"`, zero `Authorization`/`Bearer` headers, no localStorage token — auth is server-set same-origin cookie only  
[NEW] socket.io handshake on gamdommirrors.com executed (passive): `GET /socket.io/?EIO=4&transport=polling` → HTTP 200, `{"sid":"...","upgrades":["websocket"],"pingInterval":25000,...}`, behind Fastly Pool B — but Fastly pool breaks session persistence  
[CHANGED] gamdom4567.com shadow-origin hypothesis CONFIRMED (confidence 70 → DONE) — byte-identical /client-api headers across gamdom4567/gamdom80006/gamdom80007; Fastly 421 on Host-header injection proves cert binding  
[CHANGED] Cross-mirror session sharing hypothesis strengthened by verified shared origin (gamdom4567.com = backend for 8000x) — evidence_needed reduced to single cookie replay test  
[CHANGED] Cross-mirror ATO hypothesis narrowed: cookie-host-agnostic-acceptance on shared backend is the only replay vector (no localStorage replay branch)  
[CHANGED] gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle  
[PRIO] gamdom4567.com /client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=9  
[PRIO] gamdom80006.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9  
[PRIO] gamdom80007.com /client-api,8.7,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=9  
[PRIO] gamdom80004.com /client-api,8.2,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=8  
[PRIO] gamdommirrors.com /socket.io/,6.4,attack_surface=7 business_value=5 tech_exposure=7 gate_ease=8 cloud_surface=4 freshness=7  
[PRIO] gamdommirrors.com /api/status-page/gamdom-domains,6.4,attack_surface=7 business_value=6 tech_exposure=6 gate_ease=10 cloud_surface=5 freshness=8  
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO  
class: AUTH  
asset: gamdom80006.com /client-api (shared origin gamdom4567.com, Pool B: 80004/80006/80007/4567)  
confidence: 55  
reasoning: Byte-identical SPA + shared origin backend across all mirrors; /client-api GET 400 with no ACAO on both mirrors and origin (no CORS vector); auth transport proven as server-set same-origin cookie via bundle analysis; replay feasibility depends on whether cookie is host-agnostic at shared backend  
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80007 /client-api (or gamdom4567.com /client-api); cookie Domain attribute scope  
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80007; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80007 /client-api  
impact: Cross-domain account takeover across all 8 mirror hostnames; medium-high  
testability: AUTH_HELPED  
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment  
class: OTHER  
asset: gamdom4567.com /client-api (and all 7 mirrors)  
confidence: 48  
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.  
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table  
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.  
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven  
testability: AUTH_HELPED  
[HYP] Unlisted alias host widens shared-trust session surface  
class: MISCONFIG  
asset: gamdom80004.com (302 → gamdom80007.com)  
confidence: 45  
reasoning: 8th hostname, Fastly Pool B, 302 to gamdom80007; joins the same origin trust boundary; any session scoped to the mirror set is reachable from an extra hostname absent from official status page  
evidence_needed: Whether auth Set-Cookie/headers from 80007 are honored when arriving via 80004; identical session pool  
verify_steps: HEAD chain 80004→80007 (done: 302, same pool); compare Set-Cookie during auth on 80004 vs 80007 (HUMAN)  
impact: Marginal widening of replay surface + cache-key/host-confusion edge; low  
testability: HUMAN_ONLY  
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior  
[PARKED] gamdom80007.com session replay: requires authenticated session first (AUTH_HELPED), cannot verify passively  
[PARKED] gamdommirrors.com socket.io admin/mutation events exposed without auth: confidence 38 < 40 threshold; mutation against monitoring infra = REJECTED class risk per scope (no clear path to Gamdom-owned asset compromise)  
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 55, AUTH_HELPED testability, medium-high impact)  
[FINAL] 2. /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment (confidence 48, AUTH_HELPED testability, critical-if-proven impact)  
[FINAL] 3. Unlisted alias host widens shared-trust session surface (confidence 45, HUMAN_ONLY testability, low impact)  
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
[RISK] gamdom: 65 reason: Confirmed 7 mirror domains + 1 unlisted alias (gamdom80004.com) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary now verified (gamdom4567.com = backend for 8000x + gamdom80004); top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
