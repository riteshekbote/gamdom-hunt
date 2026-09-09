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
## 2026-09-05 10:01:20 UTC [target] (model nemotron3)
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
[PRIO] gamdommirrors.com/socket.io/,6.4,attack_surface=7 business_value=5 tech_exposure=7 gate_ease=8 cloud_surface=4 freshness=7
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
## 2026-09-05 13:24:05 UTC [target] (model nemotron3)
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
## 2026-09-05 16:21:31 UTC [target] (model nemotron3)
[NEW] gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend spans entire brand, not just mirrors
[NEW] gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory implying 8th API-serving mirror
[NEW] gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credentialed replay blocked
[NEW] dashboard.gamdom.com: resolves on flagship Fastly Pool A, 403-locked at edge (Varnish Error 54113) across all probed paths (/ /login /api /graphql /health /static) — genuine scoped admin hostname, no bypass
[NEW] click.gamdom.com: CNAME eu-proxy-1.symplifymail.com → eu-iv-1.symplifymail.com (192.165.55.11, third-party SymplifyMail) serving stock nginx default page — not dangling; subdomain-takeover watchlist item
[NEW] help.gamdom.com: Intercom-hosted help center (x-intercom-version, /en/ 302, Intercom CSP) — standard third-party SaaS, benign
[CHANGED] gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle
[PRIO] gamdom4567.com/client-api,9.3,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=10
[PRIO] gamdom80006.com/client-api,8.9,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=10
[PRIO] gamdom80007.com/client-api,8.9,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=10
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom80004.com/client-api,8.2,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=8
[PRIO] dashboard.gamdom.com,5.3,attack_surface=4 business_value=6 tech_exposure=6 gate_ease=3 cloud_surface=6 freshness=9
[PRIO] click.gamdom.com,4.9,attack_surface=5 business_value=5 tech_exposure=4 gate_ease=4 cloud_surface=6 freshness=8
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
[PARKED] click.gamdom.com third-party subdomain takeover: confidence 25 < 40; provider currently serves the host (not dangling), takeover conditional on future lapse
[PARKED] dashboard.gamdom.com edge 403: confidence 30 < 40; fully ACL'd, no bypass found, locked-down admin host is inventory knowledge only
[PARKED] gamdommirrors.com Kuma admin leak: confidence 35-38 < 40; verification requires prohibited POST RPC; REST view now empty
[PARKED] gamdom80004.com hidden host: confidence 20-45; disproven as API-serving host this cycle (302-only for both / and /client-api)
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
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
[LEARN] ACCEPTED recon @ click.gamdom.com: passive DNS + GET resolved in-scope subdomain to third-party SymplifyMail email provider (eu-iv-1.symplifymail.com 192.165.55.11) serving stock nginx default page — not currently dangling; subdomain-takeover watchlist item, not an active finding
[LEARN] ACCEPTED recon @ dashboard.gamdom.com: passive GET/HEAD shows genuine scoped admin hostname on flagship Fastly Pool A, fully 403-locked at edge (Varnish Error 54113) across all probed paths; access-controlled admin vhost exists but no bypass — inventory knowledge only
[LEARN] ACCEPTED recon @ help.gamdom.com: passive GET shows Intercom-hosted help center (x-intercom-version, /en/ 302) — standard third-party helpdesk SaaS, benign surface
[RISK] gamdom: 65 reason: Confirmed 7 mirror domains + 1 unlisted alias (gamdom80004.com) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary now verified (gamdom4567.com = backend for 8000x + gamdom80004); brand-wide trust pool extended to flagship gamdom.com via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-05 18:29:49 UTC [target] (model nemotron3)
[NEW] Brand-wide origin trust pool confirmed: `gamdom.com/io/eu/vip/win/client-api` GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend spans entire brand
[NEW] `gamdom80004.com/client-api`: 302 → `gamdom80007.com/client-api` (redirect alias only); corrects prior inventory implying 8th API-serving mirror
[NEW] `gamdom80007.com/client-api`: OPTIONS preflight and Origin-header GET → 400 with no `Access-Control-Allow-*` headers; browser cross-origin credentialed replay blocked
[NEW] `dashboard.gamdom.com`: resolves on flagship Fastly Pool A, 403-locked at edge (Varnish Error 54113) across all paths — genuine scoped admin hostname, no bypass
[NEW] `click.gamdom.com`: CNAME `eu-proxy-1.symplifymail.com` → `eu-iv-1.symplifymail.com` (192.165.55.11) serving stock nginx default page — not dangling; subdomain-takeover watchlist item
[NEW] `help.gamdom.com`: Intercom-hosted help center (`x-intercom-version`, `/en/` 302) — standard third-party SaaS, benign
[NEW] `gamdom80007.com` port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle
[CHANGED] Origin trust boundary now spans flagship `gamdom.com` (Pool A) + all 7 mirrors + `gamdom4567.com` (Pool B) via byte-identical `/client-api` signature
[PRIO] gamdom4567.com/client-api,9.3,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=10
[PRIO] gamdom80006.com/client-api,8.9,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=10
[PRIO] gamdom80007.com/client-api,8.9,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=10
[PRIO] gamdom.com/io/eu/vip/win/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom80004.com/client-api,8.2,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=8
[PRIO] dashboard.gamdom.com,5.3,attack_surface=4 business_value=6 tech_exposure=6 gate_ease=3 cloud_surface=6 freshness=9
[PRIO] click.gamdom.com,4.9,attack_surface=5 business_value=5 tech_exposure=4 gate_ease=4 cloud_surface=6 freshness=8
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
[PARKED] Uptime Kuma socket.io admin/mutation events exposed without auth: confidence 35-38 < 40; verification requires prohibited POST RPC; REST view now empty
[PARKED] gamdom80004.com hidden host: confidence 20-45; disproven as API-serving host this cycle (302-only for both / and /client-api)
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
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
[LEARN] ACCEPTED recon @ click.gamdom.com: passive DNS + GET resolved in-scope subdomain to third-party SymplifyMail email provider (eu-iv-1.symplifymail.com 192.165.55.11) serving stock nginx default page — not currently dangling; subdomain-takeover watchlist item, not an active finding
[LEARN] ACCEPTED recon @ dashboard.gamdom.com: passive GET/HEAD shows genuine scoped admin hostname on flagship Fastly Pool A, fully 403-locked at edge (Varnish Error 54113) across all probed paths; access-controlled admin vhost exists but no bypass — inventory knowledge only
[LEARN] ACCEPTED recon @ help.gamdom.com: passive GET shows Intercom-hosted help center (x-intercom-version, /en/ 302) — standard third-party helpdesk SaaS, benign surface
[RISK] gamdom: 65 reason: Confirmed 7 mirror domains + 1 unlisted alias (gamdom80004.com) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary now verified (gamdom4567.com = backend for 8000x + gamdom80004); brand-wide trust pool extended to flagship gamdom.com via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-05 20:48:30 UTC [target] (model nemotron3)
## 2026-09-05 22:42:51 UTC [target] (model nemotron3)
[NEW] 4 regional TLDs confirmed on Pool A: gamdom.eu, gamdom.io, gamdom.vip, gamdom.win (151.101.x.52) — each serves full SPA + byte-identical `/client-api` 400 `Invalid request, only POST`; widens trust pool to 13 hosts (flagship + 4 regional + 7 mirrors + origin)
[NEW] Auth/admin-flavored subdomains (login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com) all NXDOMAIN — live scoped surface fully enumerated, no hidden auth endpoints
[NEW] Cookie issuance policy uniform: gamdom.eu/gamdom.win root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — per-host across entire 13-host fleet
[CHANGED] Origin trust boundary now spans flagship `gamdom.com` (Pool A) + 4 regional TLDs + all 7 mirrors + `gamdom4567.com` (Pool B) via byte-identical `/client-api` signature
[CHANGED] gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrected prior inventory implying 8th API-serving mirror
[CHANGED] gamdom80007.com/client-api: OPTIONS preflight + Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credentialed replay blocked
[CHANGED] dashboard.gamdom.com: genuine scoped admin hostname on Pool A, fully 403-locked at edge (Varnish Error 54113) across all paths — inventory knowledge only
[CHANGED] click.gamdom.com: CNAME to SymplifyMail (eu-iv-1.symplifymail.com 192.165.55.11) serving stock nginx default — not dangling; subdomain-takeover watchlist item
[CHANGED] help.gamdom.com: Intercom-hosted help center — benign third-party SaaS
[PRIO] gamdom4567.com/client-api,9.3,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=10
[PRIO] gamdom80006.com/client-api,8.9,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=10
[PRIO] gamdom80007.com/client-api,8.9,attack_surface=10 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=10
[PRIO] gamdom.com/io/eu/vip/win/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom80004.com/client-api,7.8,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=8
[PRIO] dashboard.gamdom.com,5.3,attack_surface=4 business_value=6 tech_exposure=6 gate_ease=3 cloud_surface=6 freshness=9
[PRIO] click.gamdom.com,4.9,attack_surface=5 business_value=5 tech_exposure=4 gate_ease=4 cloud_surface=6 freshness=8
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
[PARKED] Uptime Kuma socket.io admin/mutation events exposed without auth: confidence 35-38 < 40; verification requires prohibited POST RPC; REST view now empty
[PARKED] gamdom80004.com hidden host: confidence 20-45; disproven as API-serving host this cycle (302-only for both / and /client-api)
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 48 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
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
[LEARN] ACCEPTED recon @ click.gamdom.com: passive DNS + GET resolved in-scope subdomain to third-party SymplifyMail email provider (eu-iv-1.symplifymail.com 192.165.55.11) serving stock nginx default page — not currently dangling; subdomain-takeover watchlist item, not an active finding
[LEARN] ACCEPTED recon @ dashboard.gamdom.com: passive GET/HEAD shows genuine scoped admin hostname on flagship Fastly Pool A, fully 403-locked at edge (Varnish Error 54113) across all probed paths; access-controlled admin vhost exists but no bypass — inventory knowledge only
[LEARN] ACCEPTED recon @ help.gamdom.com: passive GET shows Intercom-hosted help center (x-intercom-version, /en/ 302) — standard third-party helpdesk SaaS, benign surface
[RISK] gamdom: 65 reason: Confirmed 7 mirror domains + 1 unlisted alias (gamdom80004.com) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary now verified (gamdom4567.com = backend for 8000x + gamdom80004); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature (13-host fleet); top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-06 00:18:45 UTC [target] (model nemotron3)
[PRIO] gamdom4567.com/client-api,9.3,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=10
[PRIO] gamdom80006.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80007.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom90471.com/client-api,8.7,attack_surface=9 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.eu/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.io/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.vip/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.win/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom80004.com/client-api,7.8,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=8
[PRIO] dashboard.gamdom.com,5.3,attack_surface=4 business_value=6 tech_exposure=6 gate_ease=3 cloud_surface=6 freshness=9
[PRIO] click.gamdom.com,4.9,attack_surface=5 business_value=5 tech_exposure=4 gate_ease=4 cloud_surface=6 freshness=8
[PRIO] gamdommirrors.com/api/status-page/gamdom-domains,6.5,attack_surface=7 business_value=6 tech_exposure=6 gate_ease=10 cloud_surface=5 freshness=8
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80006.com/client-api (shared origin gamdom4567.com, Pool B: 80004/80006/80007/90471/4567)
confidence: 60
reasoning: Byte-identical SPA + shared origin backend across all 14 hosts; /client-api GET 400 with no ACAO on all mirrors and origin (no CORS vector); auth transport proven as server-set same-origin cookie via bundle analysis; replay feasibility depends on whether cookie is host-agnostic at shared backend
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80007/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80007 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80007/client-api and 90471/client-api
impact: Cross-domain account takeover across all 14 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship
class: AUTH
asset: gamdom.com/client-api (Pool A) and gamdom4567.com/client-api (Pool B)
confidence: 55
reasoning: gamdom.com/io/eu/vip/win/client-api returns byte-identical GET 400 body as Pool B mirrors; shared origin backend signature spans entire 14-host brand; if session cookie Domain attribute is .gamdom.com or host-agnostic at backend, flagship domain + 4 regional TLDs join replay surface
evidence_needed: Set-Cookie Domain attribute from auth flow on gamdom.com vs mirrors; whether cookie from gamdom.com accepted by gamdom4567.com/client-api
verify_steps: (passive) compare Set-Cookie headers on auth endpoints across gamdom.com (Pool A) and gamdom80006.com (Pool B); (AUTH_HELPED) replay flagship session cookie against Pool B /client-api
impact: Cross-pool ATO across flagship + 4 regional TLDs + all 7 mirrors + 2 aliases; high
testability: AUTH_HELPED
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom4567.com/client-api (and all 14 hosts)
confidence: 50
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 50 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 60, AUTH_HELPED testability, medium-high impact)
[FINAL] 2. Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (confidence 55, AUTH_HELPED testability, high impact)
[FINAL] 3. /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment (confidence 50, AUTH_HELPED testability, critical-if-proven impact) — PARKED pending authorized session
[NEXT] PROBE: GET (read-only) `https://gamdom80006.com/build/client.41b06529227c4b8b6a1d.js` — already fetched; now passively compare `Set-Cookie` headers on `POST /client-api` (400) vs `GET /` (200) across gamdom80006.com, gamdom80007.com, gamdom90471.com, gamdom4567.com to extract cookie Domain/Path/SameSite attributes without authentication (<=1 rps, no mutation)
[LEARN] ACCEPTED inventory @ gamdom90471.com: CNAME→gamdom4567.com (verified origin, same as 80006/80007), Fastly Pool B (151.101.67.72), self-referenced 18× in the official flagship HomePage bundle via the brand's own Turkish SEO hub gamdom-girisi.com which tags it "Aktif"; CT shows real DigiCert/Certainly host certs since 2025-10; currently only the fallback.tls.fastly.net SNI cert is served → provisioned-but-not-yet-live on current edge node = 8th mirror alias / 14th hostname in the brand trust pool (off the 7-monitor status page)
[LEARN] ACCEPTED recon @ gamdom-girisi.com: official brand SEO/redirect hub (Cloudflare, Turkish, 61 KB) funneling to gamdom90471.com and discord/telegram; linked from the flagship HomePage.js — a legitimate discovery seed for the rotating mirror fleet, not a threat
[LEARN] ACCEPTED recon @ gamdomgiris.link: third-party Cloudflare landing alongside the SEO hub; no Fastly origin /client-api signature → NOT in-scope brand origin (watchlist only)
[LEARN] ACCEPTED inventory @ gamdom.eu/gamdom.io/gamdom.vip/gamdom.win: passive mining of the flagship SPA's own link list yielded 4 live official regional TLDs on Pool A (151.101.x.52), each serving full SPA + byte-identical `/client-api` 400 `Invalid request, only POST` — same-family brand domains widening the trust pool by 4 hosts
[LEARN] ACCEPTED recon @ login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com: all NXDOMAIN (matches api/auth.gamdom.com 000) — the inventory's auth/admin-flavored subdomains are dead DNS; live scoped surface is now fully enumerated
[LEARN] ACCEPTED recon @ gamdom.eu/gamdom.win: root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — server cookie-issuance policy uniform per-host across the entire 13-host fleet
[LEARN] ACCEPTED recon @ gamdommirrors.com/status/gamdom-domains: official Uptime status page lists exactly 7 monitors = com/eu/io/vip/win/80006/80007 → independently ratifies the 4 regional TLDs as official and confirms gamdom90471.com is not yet on the monitor list (rotating alias before listing)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 68 reason: Confirmed 7 mirror domains + 1 unlisted alias (gamdom80004.com) + 1 provisioned alias (gamdom90471.com) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for 8000x + 90471); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature (14-host fleet); top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-06 04:49:51 UTC [target] (model nemotron3)
[NEW] gamdom90471.com confirmed as 8th mirror alias / 14th hostname in brand trust pool (CNAME→gamdom4567.com, Fastly Pool B, self-referenced 18× in flagship HomePage.js via gamdom-girisi.com SEO hub, DigiCert certs since 2025-10, fallback.tls.fastly.net SNI cert only → provisioned-but-not-yet-live on current edge node)
[NEW] gamdom-girisi.com identified as official brand SEO/redirect hub (Cloudflare, Turkish, 61 KB) funneling to gamdom90471.com + discord/telegram; linked from flagship HomePage.js — legitimate discovery seed for rotating mirror fleet
[NEW] gamdomgiris.link confirmed as third-party Cloudflare landing (no Fastly origin /client-api signature) — NOT in-scope brand origin (watchlist only)
[NEW] 4 regional TLDs (gamdom.eu/io/vip/win) confirmed on Pool A (151.101.x.52) — each serves full SPA + byte-identical `/client-api` 400 `Invalid request, only POST`; widens trust pool to 13 live hosts + 1 provisioned alias
[NEW] Auth/admin-flavored subdomains (login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com) all NXDOMAIN — live scoped surface fully enumerated, no hidden auth endpoints
[NEW] Cookie issuance policy uniform: gamdom.eu/gamdom.win root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — per-host across entire 13-host fleet
[NEW] gamdommirrors.com/status/gamdom-domains official Uptime status page lists exactly 7 monitors = com/eu/io/vip/win/80006/80007 → independently ratifies 4 regional TLDs as official and confirms gamdom90471.com not yet on monitor list (rotating alias before listing)
[CHANGED] Origin trust boundary now spans flagship gamdom.com (Pool A) + 4 regional TLDs + all 7 mirrors + gamdom4567.com (Pool B) + gamdom90471.com (Pool B alias) + gamdom80004.com (redirect alias) = 14-host fleet via byte-identical `/client-api` signature
[CHANGED] Risk score increased from 65 → 68: confirmed 14-host fleet sharing single POST-only `/client-api` identity/wallet proxy; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay
[PRIO] gamdom4567.com/client-api,9.3,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=10
[PRIO] gamdom80006.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80007.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom90471.com/client-api,8.7,attack_surface=9 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.eu/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.io/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.vip/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.win/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom80004.com/client-api,7.8,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=8
[PRIO] gamdommirrors.com/api/status-page/gamdom-domains,6.5,attack_surface=7 business_value=6 tech_exposure=6 gate_ease=10 cloud_surface=5 freshness=8
[PRIO] dashboard.gamdom.com,5.3,attack_surface=4 business_value=6 tech_exposure=6 gate_ease=3 cloud_surface=6 freshness=9
[PRIO] click.gamdom.com,4.9,attack_surface=5 business_value=5 tech_exposure=4 gate_ease=4 cloud_surface=6 freshness=8
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80006.com/client-api (shared origin gamdom4567.com, Pool B: 80004/80006/80007/90471/4567)
confidence: 60
reasoning: Byte-identical SPA + shared origin backend across all 14 hosts; /client-api GET 400 with no ACAO on all mirrors and origin (no CORS vector); auth transport proven as server-set same-origin cookie via bundle analysis (credentials:"same-origin", zero Authorization/Bearer, no localStorage token); replay feasibility depends on whether cookie is host-agnostic at shared backend
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80007/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80007 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80007/client-api and 90471/client-api
impact: Cross-domain account takeover across all 14 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship
class: AUTH
asset: gamdom.com/client-api (Pool A) and gamdom4567.com/client-api (Pool B)
confidence: 55
reasoning: gamdom.com/io/eu/vip/win/client-api returns byte-identical GET 400 body as Pool B mirrors; shared origin backend signature spans entire 14-host brand; if session cookie Domain attribute is .gamdom.com or host-agnostic at backend, flagship domain + 4 regional TLDs join replay surface
evidence_needed: Set-Cookie Domain attribute from auth flow on gamdom.com vs mirrors; whether cookie from gamdom.com accepted by gamdom4567.com/client-api
verify_steps: (passive) compare Set-Cookie headers on auth endpoints across gamdom.com (Pool A) and gamdom80006.com (Pool B); (AUTH_HELPED) replay flagship session cookie against Pool B /client-api
impact: Cross-pool ATO across flagship + 4 regional TLDs + all 7 mirrors + 2 aliases; high
testability: AUTH_HELPED
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom4567.com/client-api (and all 14 hosts)
confidence: 50
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 50 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 60, AUTH_HELPED testability, medium-high impact)
[FINAL] 2. Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (confidence 55, AUTH_HELPED testability, high impact)
[NEXT] PROBE: GET (read-only) `https://gamdom80006.com/build/client.41b06529227c4b8b6a1d.js` — already fetched; now passively compare `Set-Cookie` headers on `POST /client-api` (400) vs `GET /` (200) across gamdom80006.com, gamdom80007.com, gamdom90471.com, gamdom4567.com to extract cookie Domain/Path/SameSite attributes without authentication (<=1 rps, no mutation)
[LEARN] ACCEPTED inventory @ gamdom90471.com: CNAME→gamdom4567.com (verified origin, same as 80006/80007), Fastly Pool B (151.101.67.72), self-referenced 18× in the official flagship HomePage bundle via the brand's own Turkish SEO hub gamdom-girisi.com which tags it "Aktif"; CT shows real DigiCert/Certainly host certs since 2025-10; currently only the fallback.tls.fastly.net SNI cert is served → provisioned-but-not-yet-live on current edge node = 8th mirror alias / 14th hostname in the brand trust pool (off the 7-monitor status page)
[LEARN] ACCEPTED recon @ gamdom-girisi.com: official brand SEO/redirect hub (Cloudflare, Turkish, 61 KB) funneling to gamdom90471.com and discord/telegram; linked from the flagship HomePage.js — a legitimate discovery seed for the rotating mirror fleet, not a threat
[LEARN] ACCEPTED recon @ gamdomgiris.link: third-party Cloudflare landing alongside the SEO hub; no Fastly origin /client-api signature → NOT in-scope brand origin (watchlist only)
[LEARN] ACCEPTED inventory @ gamdom.eu/gamdom.io/gamdom.vip/gamdom.win: passive mining of the flagship SPA's own link list yielded 4 live official regional TLDs on Pool A (151.101.x.52), each serving full SPA + byte-identical `/client-api` 400 `Invalid request, only POST` — same-family brand domains widening the trust pool by 4 hosts
[LEARN] ACCEPTED recon @ login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com: all NXDOMAIN (matches api/auth.gamdom.com 000) — the inventory's auth/admin-flavored subdomains are dead DNS; live scoped surface is now fully enumerated
[LEARN] ACCEPTED recon @ gamdom.eu/gamdom.win: root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — server cookie-issuance policy uniform per-host across the entire 13-host fleet
[LEARN] ACCEPTED recon @ gamdommirrors.com/status/gamdom-domains: official Uptime status page lists exactly 7 monitors = com/eu/io/vip/win/80006/80007 → independently ratifies the 4 regional TLDs as official and confirms gamdom90471.com is not yet on the monitor list (rotating alias before listing)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 68 reason: Confirmed 7 mirror domains + 1 unlisted alias (gamdom80004.com) + 1 provisioned alias (gamdom90471.com) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for 8000x + 90471); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature (14-host fleet); top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-06 09:14:42 UTC [target] (model nemotron3)
[NEW] gamdom90471.com confirmed as 8th mirror alias / 14th hostname in brand trust pool (CNAME→gamdom4567.com, Fastly Pool B, self-referenced 18× in flagship HomePage.js via gamdom-girisi.com SEO hub, DigiCert certs since 2025-10, fallback.tls.fastly.net SNI cert only → provisioned-but-not-yet-live)
[NEW] gamdom-girisi.com identified as official brand SEO/redirect hub (Cloudflare, Turkish, 61 KB) funneling to gamdom90471.com + discord/telegram; linked from flagship HomePage.js — legitimate discovery seed for rotating mirror fleet
[NEW] gamdomgiris.link confirmed as third-party Cloudflare landing (no Fastly origin /client-api signature) — NOT in-scope brand origin (watchlist only)
[NEW] 4 regional TLDs (gamdom.eu/io/vip/win) confirmed on Pool A (151.101.x.52) — each serves full SPA + byte-identical `/client-api` 400 `Invalid request, only POST`; widens trust pool to 13 live hosts + 1 provisioned alias
[NEW] Auth/admin-flavored subdomains (login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com) all NXDOMAIN — live scoped surface fully enumerated, no hidden auth endpoints
[NEW] Cookie issuance policy uniform: gamdom.eu/gamdom.win root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — per-host across entire 13-host fleet
[NEW] gamdommirrors.com/status/gamdom-domains official Uptime status page lists exactly 7 monitors = com/eu/io/vip/win/80006/80007 → independently ratifies 4 regional TLDs as official and confirms gamdom90471.com not yet on monitor list (rotating alias before listing)
[CHANGED] Origin trust boundary now spans flagship gamdom.com (Pool A) + 4 regional TLDs + all 7 mirrors + gamdom4567.com (Pool B) + gamdom90471.com (Pool B alias) + gamdom80004.com (redirect alias) = 14-host fleet via byte-identical `/client-api` signature
[CHANGED] Risk score increased from 65 → 68: confirmed 14-host fleet sharing single POST-only `/client-api` identity/wallet proxy; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay
[PRIO] gamdom4567.com/client-api,9.3,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=10
[PRIO] gamdom80006.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80007.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom90471.com/client-api,8.7,attack_surface=9 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.eu/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.io/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.vip/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.win/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom80004.com/client-api,7.8,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=8
[PRIO] gamdommirrors.com/api/status-page/gamdom-domains,6.5,attack_surface=7 business_value=6 tech_exposure=6 gate_ease=10 cloud_surface=5 freshness=8
[PRIO] dashboard.gamdom.com,5.3,attack_surface=4 business_value=6 tech_exposure=6 gate_ease=3 cloud_surface=6 freshness=9
[PRIO] click.gamdom.com,4.9,attack_surface=5 business_value=5 tech_exposure=4 gate_ease=4 cloud_surface=6 freshness=8
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80006.com/client-api (shared origin gamdom4567.com, Pool B: 80004/80006/80007/90471/4567)
confidence: 60
reasoning: Byte-identical SPA + shared origin backend across all 14 hosts; /client-api GET 400 with no ACAO on all mirrors and origin (no CORS vector); auth transport proven as server-set same-origin cookie via bundle analysis (credentials:"same-origin", zero Authorization/Bearer, no localStorage token); replay feasibility depends on whether cookie is host-agnostic at shared backend
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80007/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80007 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80007/client-api and 90471/client-api
impact: Cross-domain account takeover across all 14 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship
class: AUTH
asset: gamdom.com/client-api (Pool A) and gamdom4567.com/client-api (Pool B)
confidence: 55
reasoning: gamdom.com/io/eu/vip/win/client-api returns byte-identical GET 400 body as Pool B mirrors; shared origin backend signature spans entire 14-host brand; if session cookie Domain attribute is .gamdom.com or host-agnostic at backend, flagship domain + 4 regional TLDs join replay surface
evidence_needed: Set-Cookie Domain attribute from auth flow on gamdom.com vs mirrors; whether cookie from gamdom.com accepted by gamdom4567.com/client-api
verify_steps: (passive) compare Set-Cookie headers on auth endpoints across gamdom.com (Pool A) and gamdom80006.com (Pool B); (AUTH_HELPED) replay flagship session cookie against Pool B /client-api
impact: Cross-pool ATO across flagship + 4 regional TLDs + all 7 mirrors + 2 aliases; high
testability: AUTH_HELPED
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom4567.com/client-api (and all 14 hosts)
confidence: 50
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 50 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 60, AUTH_HELPED testability, medium-high impact)
[FINAL] 2. Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (confidence 55, AUTH_HELPED testability, high impact)
[NEXT] PROBE: GET (read-only) `https://gamdom80006.com/build/client.41b06529227c4b8b6a1d.js` — already fetched; now passively compare `Set-Cookie` headers on `POST /client-api` (400) vs `GET /` (200) across gamdom80006.com, gamdom80007.com, gamdom90471.com, gamdom4567.com to extract cookie Domain/Path/SameSite attributes without authentication (<=1 rps, no mutation)
[LEARN] ACCEPTED inventory @ gamdom90471.com: CNAME→gamdom4567.com (verified origin, same as 80006/80007), Fastly Pool B (151.101.67.72), self-referenced 18× in the official flagship HomePage bundle via the brand's own Turkish SEO hub gamdom-girisi.com which tags it "Aktif"; CT shows real DigiCert/Certainly host certs since 2025-10; currently only the fallback.tls.fastly.net SNI cert is served → provisioned-but-not-yet-live on current edge node = 8th mirror alias / 14th hostname in the brand trust pool (off the 7-monitor status page)
[LEARN] ACCEPTED recon @ gamdom-girisi.com: official brand SEO/redirect hub (Cloudflare, Turkish, 61 KB) funneling to gamdom90471.com and discord/telegram; linked from the flagship HomePage.js — a legitimate discovery seed for the rotating mirror fleet, not a threat
[LEARN] ACCEPTED recon @ gamdomgiris.link: third-party Cloudflare landing alongside the SEO hub; no Fastly origin /client-api signature → NOT in-scope brand origin (watchlist only)
[LEARN] ACCEPTED inventory @ gamdom.eu/gamdom.io/gamdom.vip/gamdom.win: passive mining of the flagship SPA's own link list yielded 4 live official regional TLDs on Pool A (151.101.x.52), each serving full SPA + byte-identical `/client-api` 400 `Invalid request, only POST` — same-family brand domains widening the trust pool by 4 hosts
[LEARN] ACCEPTED recon @ login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com: all NXDOMAIN (matches api/auth.gamdom.com 000) — the inventory's auth/admin-flavored subdomains are dead DNS; live scoped surface is now fully enumerated
[LEARN] ACCEPTED recon @ gamdom.eu/gamdom.win: root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — server cookie-issuance policy uniform per-host across the entire 13-host fleet
[LEARN] ACCEPTED recon @ gamdommirrors.com/status/gamdom-domains: official Uptime status page lists exactly 7 monitors = com/eu/io/vip/win/80006/80007 → independently ratifies the 4 regional TLDs as official and confirms gamdom90471.com is not yet on the monitor list (rotating alias before listing)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 68 reason: Confirmed 7 mirror domains + 1 unlisted alias (gamdom80004.com) + 1 provisioned alias (gamdom90471.com) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for 8000x + 90471); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature (14-host fleet); top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-06 13:03:44 UTC [target] (model nemotron3)
[NEW] gamdom90471.com confirmed as 8th mirror alias / 14th hostname in brand trust pool (CNAME→gamdom4567.com, Fastly Pool B, self-referenced 18× in flagship HomePage.js via gamdom-girisi.com SEO hub, DigiCert certs since 2025-10, fallback.tls.fastly.net SNI cert only → provisioned-but-not-yet-live)
[NEW] gamdom-girisi.com identified as official brand SEO/redirect hub (Cloudflare, Turkish, 61 KB) funneling to gamdom90471.com + discord/telegram; linked from flagship HomePage.js — legitimate discovery seed for rotating mirror fleet
[NEW] gamdomgiris.link confirmed as third-party Cloudflare landing (no Fastly origin /client-api signature) — NOT in-scope brand origin (watchlist only)
[NEW] 4 regional TLDs (gamdom.eu/io/vip/win) confirmed on Pool A (151.101.x.52) — each serves full SPA + byte-identical `/client-api` 400 `Invalid request, only POST`; widens trust pool to 13 live hosts + 1 provisioned alias
[NEW] Auth/admin-flavored subdomains (login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com) all NXDOMAIN — live scoped surface fully enumerated, no hidden auth endpoints
[NEW] Cookie issuance policy uniform: gamdom.eu/gamdom.win root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — per-host across entire 13-host fleet
[NEW] gamdommirrors.com/status/gamdom-domains official Uptime status page lists exactly 7 monitors = com/eu/io/vip/win/80006/80007 → independently ratifies 4 regional TLDs as official and confirms gamdom90471.com not yet on monitor list (rotating alias before listing)
[CHANGED] Origin trust boundary now spans flagship gamdom.com (Pool A) + 4 regional TLDs + all 7 mirrors + gamdom4567.com (Pool B) + gamdom90471.com (Pool B alias) + gamdom80004.com (redirect alias) = 14-host fleet via byte-identical `/client-api` signature
[CHANGED] Risk score increased from 65 → 68: confirmed 14-host fleet sharing single POST-only `/client-api` identity/wallet proxy; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay
[PRIO] gamdom4567.com/client-api,9.3,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=10
[PRIO] gamdom80006.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80007.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom90471.com/client-api,8.7,attack_surface=9 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.eu/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.io/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.vip/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.win/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom80004.com/client-api,7.8,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=7 freshness=8
[PRIO] gamdommirrors.com/api/status-page/gamdom-domains,6.5,attack_surface=7 business_value=6 tech_exposure=6 gate_ease=10 cloud_surface=5 freshness=8
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80006.com/client-api (shared origin gamdom4567.com, Pool B: 80004/80006/80007/90471/4567)
confidence: 60
reasoning: Byte-identical SPA + shared origin backend across all 14 hosts; /client-api GET 400 with no ACAO on all mirrors and origin (no CORS vector); auth transport proven as server-set same-origin cookie via bundle analysis (credentials:"same-origin", zero Authorization/Bearer, no localStorage token); replay feasibility depends on whether cookie is host-agnostic at shared backend
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80007/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80007 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80007/client-api and 90471/client-api
impact: Cross-domain account takeover across all 14 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship
class: AUTH
asset: gamdom.com/client-api (Pool A) and gamdom4567.com/client-api (Pool B)
confidence: 55
reasoning: gamdom.com/io/eu/vip/win/client-api returns byte-identical GET 400 body as Pool B mirrors; shared origin backend signature spans entire 14-host brand; if session cookie Domain attribute is .gamdom.com or host-agnostic at backend, flagship domain + 4 regional TLDs join replay surface
evidence_needed: Set-Cookie Domain attribute from auth flow on gamdom.com vs mirrors; whether cookie from gamdom.com accepted by gamdom4567.com/client-api
verify_steps: (passive) compare Set-Cookie headers on auth endpoints across gamdom.com (Pool A) and gamdom80006.com (Pool B); (AUTH_HELPED) replay flagship session cookie against Pool B /client-api
impact: Cross-pool ATO across flagship + 4 regional TLDs + all 7 mirrors + 2 aliases; high
testability: AUTH_HELPED
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom4567.com/client-api (and all 14 hosts)
confidence: 50
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 50 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 60, AUTH_HELPED testability, medium-high impact)
[FINAL] 2. Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (confidence 55, AUTH_HELPED testability, high impact)
[NEXT] PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response headers/body to baseline (no cookie) to detect any differential signaling session recognition at shared backend without mutation (<=1 rps, no body, read-only)
[LEARN] ACCEPTED inventory @ gamdom90471.com: CNAME→gamdom4567.com (verified origin, same as 80006/80007), Fastly Pool B (151.101.67.72), self-referenced 18× in the official flagship HomePage bundle via the brand's own Turkish SEO hub gamdom-girisi.com which tags it "Aktif"; CT shows real DigiCert/Certainly host certs since 2025-10; currently only the fallback.tls.fastly.net SNI cert is served → provisioned-but-not-yet-live on current edge node = 8th mirror alias / 14th hostname in the brand trust pool (off the 7-monitor status page)
[LEARN] ACCEPTED recon @ gamdom-girisi.com: official brand SEO/redirect hub (Cloudflare, Turkish, 61 KB) funneling to gamdom90471.com and discord/telegram; linked from the flagship HomePage.js — a legitimate discovery seed for the rotating mirror fleet, not a threat
[LEARN] ACCEPTED recon @ gamdomgiris.link: third-party Cloudflare landing alongside the SEO hub; no Fastly origin /client-api signature → NOT in-scope brand origin (watchlist only)
[LEARN] ACCEPTED inventory @ gamdom.eu/gamdom.io/gamdom.vip/gamdom.win: passive mining of the flagship SPA's own link list yielded 4 live official regional TLDs on Pool A (151.101.x.52), each serving full SPA + byte-identical `/client-api` 400 `Invalid request, only POST` — same-family brand domains widening the trust pool by 4 hosts
[LEARN] ACCEPTED recon @ login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com: all NXDOMAIN (matches api/auth.gamdom.com 000) — the inventory's auth/admin-flavored subdomains are dead DNS; live scoped surface is now fully enumerated
[LEARN] ACCEPTED recon @ gamdom.eu/gamdom.win: root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — server cookie-issuance policy uniform per-host across the entire 13-host fleet
[LEARN] ACCEPTED recon @ gamdommirrors.com/status/gamdom-domains: official Uptime status page lists exactly 7 monitors = com/eu/io/vip/win/80006/80007 → independently ratifies the 4 regional TLDs as official and confirms gamdom90471.com is not yet on the monitor list (rotating alias before listing)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 68 reason: Confirmed 14-host fleet (flagship + 4 regional TLDs + 7 mirrors + gamdom4567.com origin + gamdom90471.com provisioned alias + gamdom80004.com redirect alias) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-06 16:18:08 UTC [target] (model nemotron3)
## 2026-09-06 18:28:53 UTC [target] (model nemotron3)
[NEW] gamdom80003.com confirmed as 9th live mirror: CNAME→gamdom4567.com, Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", /client-api 400 (26B) byte-identical — 15th hostname in brand trust pool, off 7-monitor status page
[NEW] gamdom90472.com confirmed as 16th hostname: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, off 7-monitor status page
[NEW] gamdom80005.com resolves to 8.8.8.8 (Google DNS IP, not Fastly) — REJECTED out-of-scope
[NEW] gamdom80008.com resolves to 192.64.119.33 (not Fastly) — REJECTED out-of-scope
[CHANGED] Brand trust pool expanded from 14 → 17 hosts (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect + gamdom90472.com provisioned)
[CHANGED] Origin trust boundary now spans 17 hosts across Pool A (gamdom.com/eu/io/vip/win) and Pool B (80003/80004/80006/80007/90471/90472/4567) via byte-identical /client-api signature and shared weak-ETag on /health
[PRIO] gamdom4567.com/client-api,9.3,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=10
[PRIO] gamdom80006.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80007.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80003.com/client-api,9.0,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom90471.com/client-api,8.7,attack_surface=9 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.eu/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.io/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.vip/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease[0m
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80007/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80007 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80007/client-api and 90471/client-api
impact: Cross-domain account takeover across all 17 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship
class: AUTH
asset: gamdom.com/client-api (Pool A) and gamdom4567.com/client-api (Pool B)
confidence: 57
reasoning: gamdom.com/io/eu/vip/win/client-api returns byte-identical GET 400 body as Pool B mirrors; shared origin backend signature spans entire 17-host brand; if session cookie Domain attribute is .gamdom.com or host-agnostic at backend, flagship domain + 4 regional TLDs join replay surface
evidence_needed: Set-Cookie Domain attribute from auth flow on gamdom.com vs mirrors; whether cookie from gamdom.com accepted by gamdom4567.com/client-api
verify_steps: (passive) compare Set-Cookie headers on auth endpoints across gamdom.com (Pool A) and gamdom80006.com (Pool B); (AUTH_HELPED) replay flagship session cookie against Pool B /client-api
impact: Cross-pool ATO across flagship + 4 regional TLDs + all 9 mirrors + 3 aliases; high
testability: AUTH_HELPED
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom4567.com/client-api (and all 17 hosts)
confidence: 50
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 50 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 62, AUTH_HELPED testability, medium-high impact)
[FINAL] 2. Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (confidence 57, AUTH_HELPED testability, high impact)
[NEXT] PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response headers/body to baseline (no cookie) to detect any differential signaling session recognition at shared backend without mutation (<=1 rps, no body, read-only)
[LEARN] ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" + /client-api 400 (26B) byte-identical — confirmed 9th live mirror, 15th hostname in brand trust pool; off the 7-monitor status page
[LEARN] ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool; off the 7-monitor status page
[LEARN] REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure
[LEARN] REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 70 reason: Confirmed 17-host fleet (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect + gamdom90472.com provisioned) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-06 20:32:39 UTC [target] (model nemotron3)
[NEW] gamdom80003.com confirmed as 9th live mirror: CNAME→gamdom4567.com, Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", /client-api 400 (26B) byte-identical — 15th hostname in brand trust pool, off 7-monitor status page
[NEW] gamdom90472.com confirmed as 16th hostname: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, off 7-monitor status page
[NEW] gamdom80005.com resolves to 8.8.8.8 (Google DNS IP, not Fastly) — REJECTED out-of-scope
[NEW] gamdom80008.com resolves to 192.64.119.33 (not Fastly) — REJECTED out-of-scope
[CHANGED] Brand trust pool expanded from 14 → 17 hosts (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned)
[CHANGED] Origin trust boundary now spans 17 hosts across Pool A (gamdom.com/eu/io/vip/win) and Pool B (80003/80004/80006/80007/90471/90472/4567) via byte-identical /client-api signature and shared weak-ETag on /health
[PRIO] gamdom4567.com/client-api,9.3,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=10
[PRIO] gamdom80006.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80007.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80003.com/client-api,9.0,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom90471.com/client-api,8.7,attack_surface=9 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.eu/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.io/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.vip/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.win/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom80004.com/client-api,7.8,attack_surface=8 business_value=9 tech_exposure=8 gate_ease=5 cloud_surface=8 freshness=9
[PRIO] gamdom90472.com/client-api,7.2,attack_surface=7 business_value=9 tech_exposure=8 gate_ease=5 cloud_surface=8 freshness=8
[PRIO] dashboard.gamdom.com,7.0,attack_surface=8 business_value=8 tech_exposure=7 gate_ease=4 cloud_surface=8 freshness=8
[PRIO] gamdommirrors.com/api,6.5,attack_surface=7 business_value=6 tech_exposure=7 gate_ease=6 cloud_surface=7 freshness=8
[PRIO] click.gamdom.com,5.5,attack_surface=6 business_value=5 tech_exposure=6 gate_ease=5 cloud_surface=7 freshness=7
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80006.com/client-api (and all Pool B mirrors + gamdom4567.com)
confidence: 62
reasoning: Single shared backend (gamdom4567.com) proven via byte-identical /client-api headers + weak-ETag on /health across 9 live Pool B mirrors; auth transport confirmed as server-set same-origin cookie (credentials: same-origin, no Bearer/localStorage) from passive bundle analysis; cookie Domain attribute is host-only (no Domain=) per gd-lang cookie observation; if backend validates cookie host-agnostically, session from any mirror works on all others
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80007/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope from auth flow Set-Cookie headers
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80007 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80007/client-api and 90471/client-api
impact: Cross-domain account takeover across all 17 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship
class: AUTH
asset: gamdom.com/client-api (Pool A) and gamdom4567.com/client-api (Pool B)
confidence: 57
reasoning: gamdom.com/io/eu/vip/win/client-api returns byte-identical GET 400 body as Pool B mirrors; shared origin backend signature spans entire 17-host brand; if session cookie Domain attribute is .gamdom.com or host-agnostic at backend, flagship domain + 4 regional TLDs join replay surface
evidence_needed: Set-Cookie Domain attribute from auth flow on gamdom.com vs mirrors; whether cookie from gamdom.com accepted by gamdom4567.com/client-api
verify_steps: (passive) compare Set-Cookie headers on auth endpoints across gamdom.com (Pool A) and gamdom80006.com (Pool B); (AUTH_HELPED) replay flagship session cookie against Pool B /client-api
impact: Cross-pool ATO across flagship + 4 regional TLDs + all 9 mirrors + 3 aliases; high
testability: AUTH_HELPED
[HYP] /client-api catch-all proxy forwards attacker-controlled action/payload to upstream wallet/auth — SSRF or mass-assignment
class: OTHER
asset: gamdom4567.com/client-api (and all 17 hosts)
confidence: 50
reasoning: /client-api is POST-only catch-all proxy (400 on GET/OPTIONS for /v1, /v2, /health, /graphql, /openapi.json). Minified bundle shows api factory (o.kT with base "/client-api") calling methods by action name. If proxy forwards subpath/payload to upstream identity/wallet service, SSRF-on-proxy or mass-assignment-by-copy risks emerge.
evidence_needed: POST with crafted body returning upstream internal error/stack/truthy status differing by injected subpath; or bundle resolving action->endpoint table
verify_steps: PASSIVE: status-code differential on GET/OPTIONS variants (done — all 400). Bundle analysis for action routing map (requires authorized access). No live POST without authorized session (REJECTED auth-bypass). Await authorized tester with valid session to probe action routing.
impact: If wallet/deposit/withdrawal/auth endpoint reachable via /client-api without correct authorization -> fund theft or ATO (critical) — unproven
testability: AUTH_HELPED
[PARKED] /client-api catch-all proxy SSRF/mass-assignment: confidence 50 but verify_steps requires AUTH_HELPED live POST with valid session; currently blocked by REJECTED auth-bypass learning; no passive-only verification path to confirm upstream routing behavior
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 62, AUTH_HELPED testability, medium-high impact)
[FINAL] 2. Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (confidence 57, AUTH_HELPED testability, high impact)
[NEXT] PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response headers/body to baseline (no cookie) to detect any differential signaling session recognition at shared backend without mutation (<=1 rps, no body, read-only)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" + /client-api 400 (26B) byte-identical — confirmed 9th live mirror, 15th hostname in brand trust pool; off the 7-monitor status page
[LEARN] ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool; off the 7-monitor status page
[LEARN] REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure
[LEARN] REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure
[RISK] gamdom: 70 reason: Confirmed 17-host fleet (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-06 22:21:41 UTC [target] (model nemotron3)
[NEW] gamdom80003.com confirmed as 9th live mirror: CNAME→gamdom4567.com, Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", /client-api 400 (26B) byte-identical — 15th hostname in brand trust pool, off 7-monitor status page  
[NEW] gamdom90472.com confirmed as 16th hostname: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, off 7-monitor status page  
[NEW] gamdom80005.com resolves to 8.8.8.8 (Google DNS IP, not Fastly) — REJECTED out-of-scope  
[NEW] gamdom80008.com resolves to 192.64.119.33 (not Fastly) — REJECTED out-of-scope  
[NEW] teamgamdom.com confirmed as 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert same CA) hosting staging.teamgamdom.com (503 Maintenance) + shreeram-dynamic-test.teamgamdom.com (nginx Basic auth realm="secret" 401) — new origin realm distinct from gamdom4567 Starlette pool  
[NEW] gamdom90488.com public numbered alias CNAME→shreeram-dynamic-test.teamgamdom.com pins brand mirror onto internal Basic-auth-gated nginx test backend; alias edge SNI not yet live  
[NEW] gamdom90480.com/gamdom90482.com both CNAME→gamdom4567.com, Pool B, client-api 421 — 5th/6th provisioned 9047x-family alias  
[NEW] gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com staging page assets public-read (200), ListObjects/root denied (403) — closed hosting, inventory only  
[NEW] 10x gamdom9047x.com (90474/90476/90477/90478/90479/90481/90483/90484/90485/90486) resolve to 192.64.119.x/162.255.119.x (non-Fastly) — REJECTED out-of-scope  
[CHANGED] Brand trust pool expanded from 14 → 17 hosts (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned)  
[CHANGED] Origin trust boundary now spans 17 hosts across Pool A (gamdom.com/eu/io/vip/win) and Pool B (80003/80004/80006/80007/90471/90472/4567) via byte-identical /client-api signature and shared weak-ETag on /health  
[CHANGED] New distinct origin realm discovered: teamgamdom.com (Pool A) with Basic-auth-gated test backend shreeram-dynamic-test.teamgamdom.com — separate from gamdom4567.com Starlette pool
[PRIO] gamdom4567.com/client-api,9.3,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=10  
[PRIO] gamdom80006.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10  
[PRIO] gamdom80007.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10  
[PRIO] gamdom80003.com/client-api,9.0,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10  
[PRIO] gamdom90471.com/client-api,8.7,attack_surface=9 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=9  
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9  
[PRIO] gamdom.eu/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9  
[PRIO] gamdom.io/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9  
[PRIO] gamdom.vip/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9  
[PRIO] gamdom.win/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9  
[PRIO] gamdom80004.com/client-api,7.8,attack_surface=8 business_value=9 tech_exposure=8 gate_ease=5 cloud_surface=8 freshness=9  
[PRIO] gamdom90472.com/client-api,7.2,attack_surface=7 business_value=9 tech_exposure=8 gate_ease=5 cloud_surface=8 freshness=8  
[PRIO] shreeram-dynamic-test.teamgamdom.com,6.8,attack_surface=7 business_value=7 tech_exposure=8 gate_ease=4 cloud_surface=7 freshness=8  
[PRIO] dashboard.gamdom.com,7.0,attack_surface=8 business_value=8 tech_exposure=7 gate_ease=4 cloud_surface=8 freshness=8  
[PRIO] gamdommirrors.com/api,6.5,attack_surface=7 business_value=6 tech_exposure=7 gate_ease=6 cloud_surface=7 freshness=8  
[PRIO] click.gamdom.com,5.5,attack_surface=6 business_value=5 tech_exposure=6 gate_ease=5 cloud_surface=7 freshness=7
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO  
class: AUTH  
asset: gamdom80006.com/client-api (and all Pool B mirrors + gamdom4567.com)  
confidence: 62  
reasoning: Single shared backend (gamdom4567.com) proven via byte-identical /client-api headers + weak-ETag on /health across 9 live Pool B mirrors; auth transport confirmed as server-set same-origin cookie (credentials: same-origin, no Bearer/localStorage) from passive bundle analysis; cookie Domain attribute is host-only (no Domain=) per gd-lang cookie observation; if backend validates cookie host-agnostically, session from any mirror works on all others  
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80007/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope from auth flow Set-Cookie headers  
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80007 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80007/client-api and 90471/client-api  
impact: Cross-domain account takeover across all 17 mirror hostnames; medium-high  
testability: AUTH_HELPED  
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship  
class: AUTH  
asset: gamdom.com/client-api (Pool A) and gamdom4567.com/client-api (Pool B)  
confidence: 57  
reasoning: gamdom.com/io/eu/vip/win/client-api returns byte-identical GET 400 body as Pool B mirrors; shared origin backend signature spans entire 17-host brand; if session cookie Domain attribute is .gamdom.com or host-agnostic at backend, flagship domain + 4 regional TLDs join replay surface  
evidence_needed: Set-Cookie Domain attribute from auth flow on gamdom.com vs mirrors; whether cookie from gamdom.com accepted by gamdom4567.com/client-api  
verify_steps: (passive) compare Set-Cookie headers on auth endpoints across gamdom.com (Pool A) and gamdom80006.com (Pool B); (AUTH_HELPED) replay flagship session cookie against Pool B /client-api  
impact: Cross-pool ATO across flagship + 4 regional TLDs + all 9 mirrors + 3 aliases; high  
testability: AUTH_HELPED  
[HYP] teamgamdom.com Basic-auth test backend exposes internal staging endpoints via shreeram-dynamic-test subdomain  
class: MISCONFIG  
asset: shreeram-dynamic-test.teamgamdom.com  
confidence: 48  
reasoning: teamgamdom.com is 2nd in-scope brand TLD on Fastly Pool A with Certainly cert; shreeram-dynamic-test.teamgamdom.com serves nginx with Basic auth realm="secret" (fixture-wide 401); gamdom90488.com CNAME points to this test backend, pinning a brand mirror alias onto internal infrastructure; if credentials are weak/default or auth bypass exists, internal staging endpoints could be exposed  
evidence_needed: Weak/default credentials for Basic auth realm="secret"; or auth bypass on nginx config; or exposed internal API endpoints behind auth  
verify_steps: (passive) enumerate paths on shreeram-dynamic-test.teamgamdom.com with common admin/staging paths (/api, /admin, /health, /metrics, /actuator, /debug); (AUTH_HELPED) test common weak credentials (admin:admin, admin:secret, gamdom:gamdom, test:test) against Basic auth  
impact: Access to internal staging/test backend potentially exposing debug endpoints, config, or non-prod wallet/auth logic; medium  
testability: AUTH_HELPED
[PARKED] teamgamdom.com Basic-auth test backend exposes internal staging endpoints via shreeram-dynamic-test subdomain: confidence 48 but verify_steps requires AUTH_HELPED credential testing; Basic auth on internal test fixture is low business value unless it guards sensitive non-prod wallet endpoints; no evidence of actual sensitive exposure yet  
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 62, AUTH_HELPED testability, medium-high impact)  
[FINAL] 2. Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (confidence 57, AUTH_HELPED testability, high impact)
[NEXT] PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response headers/body to baseline (no cookie) to detect any differential signaling session recognition at shared backend without mutation (<=1 rps, no body, read-only)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing  
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)  
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing  
[LEARN] ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" + /client-api 400 (26B) byte-identical — confirmed 9th live mirror, 15th hostname in brand trust pool; off the 7-monitor status page  
[LEARN] ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool; off the 7-monitor status page  
[LEARN] REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure  
[LEARN] REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure  
[LEARN] ACCEPTED inventory @ teamgamdom.com: 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert same CA as mirror fleet) hosting staging.teamgamdom.com (live "Maintenance - Gamdom.com" 503 page) + shreeram-dynamic-test.teamgamdom.com (nginx Basic realm="secret" fixture-wide 401) — new origin realm distinct from gamdom4567 Starlette pool  
[LEARN] ACCEPTED recon @ gamdom90488.com: public numbered alias CNAME→shreeram-dynamic-test.teamgamdom.com pins a brand mirror onto the internal Basic-auth-gated nginx test backend; alias edge SNI not yet live  
[LEARN] ACCEPTED inventory @ gamdom90480.com/gamdom90482.com: both CNAME→gamdom4567.com, Pool B, client-api 421 — 5th/6th provisioned 9047x-family alias  
[LEARN] ACCEPTED recon @ gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com: staging page assets public-read (200), ListObjects/root denied (403) — closed hosting, inventory only  
[LEARN] REJECTED out-of-scope @ gamdom90474/90476/90477/90478/90479/90481/90483/90484/90485/90486.com: resolve to 192.64.119.x/162.255.119.x (non-Fastly) — not brand infrastructure
[RISK] gamdom: 70 reason: Confirmed 17-host fleet (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-07 00:07:56 UTC [target] (model nemotron3)
[NEW] gamdom80003.com confirmed 9th live mirror: CNAME→gamdom4567.com, Fastly Pool B (4 edges), /health 200 weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", /client-api 400 26B byte-identical
[NEW] gamdom90472.com confirmed 16th hostname: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live
[NEW] teamgamdom.com confirmed 2nd in-scope brand TLD: Route53, Fastly Pool A, Certainly cert; hosts staging.teamgamdom.com (503 Maintenance) + shreeram-dynamic-test.teamgamdom.com (nginx Basic realm="secret" 401)
[NEW] gamdom90488.com public alias CNAME→shreeram-dynamic-test.teamgamdom.com pins brand mirror onto internal Basic-auth test backend; alias edge SNI not live (TLS-NOMATCH)
[NEW] gamdom90480.com/gamdom90482.com both CNAME→gamdom4567.com, Pool B, /client-api 421 — 5th/6th provisioned 9047x-family aliases
[CHANGED] Brand trust pool expanded 14→17 hosts across Pool A (gamdom.com/eu/io/vip/win) and Pool B (80003/80004/80006/80007/90471/90472/4567) via byte-identical /client-api + shared weak-ETag
[CHANGED] New distinct origin realm discovered: teamgamdom.com (Pool A) with Basic-auth-gated test backend shreeram-dynamic-test.teamgamdom.com — separate from gamdom4567.com Starlette pool
[CHANGED] gamdommirrors.com status page still exactly 7 monitors (com/eu/io/vip/win/80006/80007) — 90471/90472/80003/80004/90480/90482/90488 off monitor list
[PRIO] gamdom4567.com/client-api,9.3,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=7 cloud_surface=8 freshness=10
[PRIO] gamdom80006.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80007.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80003.com/client-api,9.0,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom90471.com/client-api,8.7,attack_surface=9 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.eu/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.io/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.vip/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.win/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] shreeram-dynamic-test.teamgamdom.com,7.2,attack_surface=7 business_value=7 tech_exposure=8 gate_ease=4 cloud_surface=7 freshness=8
[PRIO] gamdom90488.com,7.0,attack_surface=7 business_value=7 tech_exposure=8 gate_ease=4 cloud_surface=7 freshness=8
[PRIO] dashboard.gamdom.com,7.0,attack_surface=8 business_value=8 tech_exposure=7 gate_ease=4 cloud_surface=8 freshness=8
[PRIO] gamdom90480.com/client-api,6.8,attack_surface=7 business_value=7 tech_exposure=8 gate_ease=5 cloud_surface=8 freshness=8
[PRIO] gamdom90482.com/client-api,6.8,attack_surface=7 business_value=7 tech_exposure=8 gate_ease=5 cloud_surface=8 freshness=8
[PRIO] gamdommirrors.com/api,6.5,attack_surface=7 business_value=6 tech_exposure=7 gate_ease=6 cloud_surface=7 freshness=8
[PRIO] click.gamdom.com,5.5,attack_surface=6 business_value=5 tech_exposure=6 gate_ease=5 cloud_surface=7 freshness=7
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80006.com/client-api (and all Pool B mirrors + gamdom4567.com)
confidence: 62
reasoning: Single shared backend (gamdom4567.com) proven via byte-identical /client-api headers + weak-ETag on /health across 9 live Pool B mirrors; auth transport confirmed as server-set same-origin cookie (credentials: same-origin, no Bearer/localStorage) from passive bundle analysis; cookie Domain attribute is host-only (no Domain=) per gd-lang cookie observation; if backend validates cookie host-agnostically, session from any mirror works on all others
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80007/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope from auth flow Set-Cookie headers
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80007 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80007/client-api and 90471/client-api
impact: Cross-domain account takeover across all 17 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship
class: AUTH
asset: gamdom.com/client-api (Pool A) and gamdom4567.com/client-api (Pool B)
confidence: 57
reasoning: gamdom.com/io/eu/vip/win/client-api returns byte-identical GET 400 body as Pool B mirrors; shared origin backend signature spans entire 17-host brand; if session cookie Domain attribute is .gamdom.com or host-agnostic at backend, flagship domain + 4 regional TLDs join replay surface
evidence_needed: Set-Cookie Domain attribute from auth flow on gamdom.com vs mirrors; whether cookie from gamdom.com accepted by gamdom4567.com/client-api
verify_steps: (passive) compare Set-Cookie headers on auth endpoints across gamdom.com (Pool A) and gamdom80006.com (Pool B); (AUTH_HELPED) replay flagship session cookie against Pool B /client-api
impact: Cross-pool ATO across flagship + 4 regional TLDs + all 9 mirrors + 3 aliases; high
testability: AUTH_HELPED
[HYP] teamgamdom.com Basic-auth test backend exposes internal staging endpoints via shreeram-dynamic-test subdomain
class: MISCONFIG
asset: shreeram-dynamic-test.teamgamdom.com
confidence: 48
reasoning: teamgamdom.com is 2nd in-scope brand TLD on Fastly Pool A with Certainly cert; shreeram-dynamic-test.teamgamdom.com serves nginx with Basic auth realm="secret" (fixture-wide 401); gamdom90488.com CNAME points to this test backend, pinning a brand mirror alias onto internal infrastructure; if credentials are weak/default or auth bypass exists, internal staging endpoints could be exposed
evidence_needed: Weak/default credentials for Basic auth realm="secret"; or auth bypass on nginx config; or exposed internal API endpoints behind auth
verify_steps: (passive) enumerate paths on shreeram-dynamic-test.teamgamdom.com with common admin/staging paths (/api, /admin, /health, /metrics, /actuator, /debug); (AUTH_HELPED) test common weak credentials (admin:admin, admin:secret, gamdom:gamdom, test:test) against Basic auth
impact: Access to internal staging/test backend potentially exposing debug endpoints, config, or non-prod wallet/auth logic; medium
testability: AUTH_HELPED
[PARKED] teamgamdom.com Basic-auth test backend exposes internal staging endpoints via shreeram-dynamic-test subdomain: confidence 48 but verify_steps requires AUTH_HELPED credential testing; Basic auth on internal test fixture is low business value unless it guards sensitive non-prod wallet endpoints; no evidence of actual sensitive exposure yet
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 62, AUTH_HELPED testability, medium-high impact)
[FINAL] 2. Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (confidence 57, AUTH_HELPED testability, high impact)
[NEXT] PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response headers/body to baseline (no cookie) to detect any differential signaling session recognition at shared backend without mutation (<=1 rps, no body, read-only)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" + /client-api 400 (26B) byte-identical — confirmed 9th live mirror, 15th hostname in brand trust pool; off the 7-monitor status page
[LEARN] ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool; off the 7-monitor status page
[LEARN] REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure
[LEARN] REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure
[LEARN] ACCEPTED inventory @ teamgamdom.com: 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert same CA as mirror fleet) hosting staging.teamgamdom.com (live "Maintenance - Gamdom.com" 503 page) + shreeram-dynamic-test.teamgamdom.com (nginx Basic realm="secret" fixture-wide 401) — new origin realm distinct from gamdom4567 Starlette pool
[LEARN] ACCEPTED recon @ gamdom90488.com: public numbered alias CNAME→shreeram-dynamic-test.teamgamdom.com pins a brand mirror onto the internal Basic-auth-gated nginx test backend; alias edge SNI not yet live
[LEARN] ACCEPTED inventory @ gamdom90480.com/gamdom90482.com: both CNAME→gamdom4567.com, Pool B, client-api 421 — 5th/6th provisioned 9047x-family alias
[LEARN] ACCEPTED recon @ gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com: staging page assets public-read (200), ListObjects/root denied (403) — closed hosting, inventory only
[LEARN] REJECTED out-of-scope @ gamdom90474/90476/90477/90478/90479/90481/90483/90484/90485/90486.com: resolve to 192.64.119.x/162.255.119.x (non-Fastly) — not brand infrastructure
[RISK] gamdom: 70 reason: Confirmed 17-host fleet (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-07 04:54:59 UTC [target] (model nemotron3)
[NEW] teamgamdom.com confirmed as 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert) hosting staging.teamgamdom.com (503 Maintenance) + shreeram-dynamic-test.teamgamdom.com (nginx Basic realm="secret" fixture-wide 401) — distinct origin realm from gamdom4567.com Starlette pool
[NEW] gamdom90488.com public alias CNAME→shreeram-dynamic-test.teamgamdom.com pins brand mirror onto internal Basic-auth-gated nginx test backend; alias edge SNI not yet live (TLS-NOMATCH)
[NEW] gamdom90480.com/gamdom90482.com both CNAME→gamdom4567.com, Pool B, /client-api 421 — 5th/6th provisioned 9047x-family aliases
[NEW] gamdom80003.com confirmed 9th live mirror: CNAME→gamdom4567.com, Fastly Pool B (4 edges), /health 200 weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", /client-api 400 26B byte-identical
[NEW] gamdom90472.com confirmed 16th hostname: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live
[CHANGED] Brand trust pool expanded 14→17 hosts across Pool A (gamdom.com/eu/io/vip/win) and Pool B (80003/80004/80006/80007/90471/90472/4567) via byte-identical /client-api + shared weak-ETag
[CHANGED] gamdommirrors.com status page still exactly 7 monitors (com/eu/io/vip/win/80006/80007) — 90471/90472/80003/80004/90480/90482/90488 off monitor list
[PRIO] gamdom80006.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80007.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80003.com/client-api,9.0,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom90471.com/client-api,8.7,attack_surface=9 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.eu/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.io/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.vip/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.win/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] shreeram-dynamic-test.teamgamdom.com,7.2,attack_surface=7 business_value=7 tech_exposure=8 gate_ease=4 cloud_surface=7 freshness=8
[PRIO] gamdom90488.com,7.0,attack_surface=7 business_value=7 tech_exposure=8 gate_ease=4 cloud_surface=7 freshness=8
[PRIO] dashboard.gamdom.com,7.0,attack_surface=8 business_value=8 tech_exposure=7 gate_ease=4 cloud_surface=8 freshness=8
[PRIO] gamdom90480.com/client-api,6.8,attack_surface=7 business_value=7 tech_exposure=8 gate_ease=5 cloud_surface=8 freshness=8
[PRIO] gamdom90482.com/client-api,6.8,attack_surface=7 business_value=7 tech_exposure=8 gate_ease=5 cloud_surface=8 freshness=8
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80006.com/client-api (and all Pool B mirrors + gamdom4567.com)
confidence: 62
reasoning: Single shared backend (gamdom4567.com) proven via byte-identical /client-api headers + weak-ETag on /health across 9 live Pool B mirrors; auth transport confirmed as server-set same-origin cookie (credentials: same-origin, no Bearer/localStorage) from passive bundle analysis; cookie Domain attribute is host-only (no Domain=) per gd-lang cookie observation; if backend validates cookie host-agnostically, session from any mirror works on all others
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80007/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope from auth flow Set-Cookie headers
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80007 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80007/client-api and 90471/client-api
impact: Cross-domain account takeover across all 17 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship
class: AUTH
asset: gamdom.com/client-api (Pool A) and gamdom4567.com/client-api (Pool B)
confidence: 57
reasoning: gamdom.com/io/eu/vip/win/client-api returns byte-identical GET 400 body as Pool B mirrors; shared origin backend signature spans entire 17-host brand; if session cookie Domain attribute is .gamdom.com or host-agnostic at backend, flagship domain + 4 regional TLDs join replay surface
evidence_needed: Set-Cookie Domain attribute from auth flow on gamdom.com vs mirrors; whether cookie from gamdom.com accepted by gamdom4567.com/client-api
verify_steps: (passive) compare Set-Cookie headers on auth endpoints across gamdom.com (Pool A) and gamdom80006.com (Pool B); (AUTH_HELPED) replay flagship session cookie against Pool B /client-api
impact: Cross-pool ATO across flagship + 4 regional TLDs + all 9 mirrors + 3 aliases; high
testability: AUTH_HELPED
[HYP] teamgamdom.com Basic-auth test backend exposes internal staging endpoints via shreeram-dynamic-test subdomain
class: MISCONFIG
asset: shreeram-dynamic-test.teamgamdom.com
confidence: 48
reasoning: teamgamdom.com is 2nd in-scope brand TLD on Fastly Pool A with Certainly cert; shreeram-dynamic-test.teamgamdom.com serves nginx with Basic auth realm="secret" (fixture-wide 401); gamdom90488.com CNAME points to this test backend, pinning a brand mirror alias onto internal infrastructure; if credentials are weak/default or auth bypass exists, internal staging endpoints could be exposed
evidence_needed: Weak/default credentials for Basic auth realm="secret"; or auth bypass on nginx config; or exposed internal API endpoints behind auth
verify_steps: (passive) enumerate paths on shreeram-dynamic-test.teamgamdom.com with common admin/staging paths (/api, /admin, /health, /metrics, /actuator, /debug); (AUTH_HELPED) test common weak credentials (admin:admin, admin:secret, gamdom:gamdom, test:test) against Basic auth
impact: Access to internal staging/test backend potentially exposing debug endpoints, config, or non-prod wallet/auth logic; medium
testability: AUTH_HELPED
[PARKED] teamgamdom.com Basic-auth test backend exposes internal staging endpoints via shreeram-dynamic-test subdomain: confidence 48 but verify_steps requires AUTH_HELPED credential testing; Basic auth on internal test fixture is low business value unless it guards sensitive non-prod wallet endpoints; no evidence of actual sensitive exposure yet
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 62, AUTH_HELPED testability, medium-high impact)
[FINAL] 2. Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (confidence 57, AUTH_HELPED testability, high impact)
[NEXT] PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response headers/body to baseline (no cookie) to detect any differential signaling session recognition at shared backend without mutation (<=1 rps, no body, read-only)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" + /client-api 400 (26B) byte-identical — confirmed 9th live mirror, 15th hostname in brand trust pool; off the 7-monitor status page
[LEARN] ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool; off the 7-monitor status page
[LEARN] REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure
[LEARN] REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure
[LEARN] ACCEPTED inventory @ teamgamdom.com: 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert same CA as mirror fleet) hosting staging.teamgamdom.com (live "Maintenance - Gamdom.com" 503 page) + shreeram-dynamic-test.teamgamdom.com (nginx Basic realm="secret" fixture-wide 401) — new origin realm distinct from gamdom4567 Starlette pool
[LEARN] ACCEPTED recon @ gamdom90488.com: public numbered alias CNAME→shreeram-dynamic-test.teamgamdom.com pins a brand mirror onto the internal Basic-auth-gated nginx test backend; alias edge SNI not yet live
[LEARN] ACCEPTED inventory @ gamdom90480.com/gamdom90482.com: both CNAME→gamdom4567.com, Pool B, client-api 421 — 5th/6th provisioned 9047x-family alias
[LEARN] ACCEPTED recon @ gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com: staging page assets public-read (200), ListObjects/root denied (403) — closed hosting, inventory only
[LEARN] REJECTED out-of-scope @ gamdom90474/90476/90477/90478/90479/90481/90483/90484/90485/90486.com: resolve to 192.64.119.x/162.255.119.x (non-Fastly) — not brand infrastructure
[RISK] gamdom: 70 reason: Confirmed 17-host fleet (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-07 09:58:04 UTC [target] (model nemotron3)
[PRIO] gamdom80006.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80007.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80003.com/client-api,9.0,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom90471.com/client-api,8.7,attack_surface=9 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.eu/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.io/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.vip/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.win/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] shreeram-dynamic-test.teamgamdom.com,7.2,attack_surface=7 business_value=7 tech_exposure=8 gate_ease=4 cloud_surface=7 freshness=8
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
[HYP] teamgamdom.com Basic-auth test backend exposes internal staging endpoints via shreeram-dynamic-test subdomain
class: MISCONFIG
asset: shreeram-dynamic-test.teamgamdom.com
confidence: 48
reasoning: teamgamdom.com is 2nd in-scope brand TLD on Fastly Pool A with Certainly cert; shreeram-dynamic-test.teamgamdom.com serves nginx with Basic auth realm="secret" (fixture-wide 401); gamdom90488.com CNAME points to this test backend, pinning a brand mirror alias onto internal infrastructure; if credentials are weak/default or auth bypass exists, internal staging endpoints could be exposed
evidence_needed: Weak/default credentials for Basic auth realm="secret"; or auth bypass on nginx config; or exposed internal API endpoints behind auth
verify_steps: (passive) enumerate paths on shreeram-dynamic-test.teamgamdom.com with common admin/staging paths (/api, /admin, /health, /metrics, /actuator, /debug); (AUTH_HELPED) test common weak credentials (admin:admin, admin:secret, gamdom:gamdom, test:test) against Basic auth
impact: Access to internal staging/test backend potentially exposing debug endpoints, config, or non-prod wallet/auth logic; medium
testability: AUTH_HELPED
[PARKED] teamgamdom.com Basic-auth test backend exposes internal staging endpoints via shreeram-dynamic-test subdomain: confidence 48 but verify_steps requires AUTH_HELPED credential testing; Basic auth on internal test fixture is low business value unless it guards sensitive non-prod wallet endpoints; no evidence of actual sensitive exposure yet; debug paths /.git/config /env /server-status /status /_metrics /actuator all return identical 401 (172B) — no gate gap or debug leak confirmed
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 62, AUTH_HELPED testability, medium-high impact)
[FINAL] 2. Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (confidence 57, AUTH_HELPED testability, high impact)
[NEXT] PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response headers/body to baseline (no cookie) to detect any differential signaling session recognition at shared backend without mutation (<=1 rps, no body, read-only)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" + /client-api 400 (26B) byte-identical — confirmed 9th live mirror, 15th hostname in brand trust pool; off the 7-monitor status page
[LEARN] ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool; off the 7-monitor status page
[LEARN] REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure
[LEARN] REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure
[LEARN] ACCEPTED inventory @ teamgamdom.com: 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert same CA as mirror fleet) hosting staging.teamgamdom.com (live "Maintenance - Gamdom.com" 503 page) + shreeram-dynamic-test.teamgamdom.com (nginx Basic realm="secret" fixture-wide 401) — new origin realm distinct from gamdom4567 Starlette pool
[LEARN] ACCEPTED recon @ gamdom90488.com: public numbered alias CNAME→shreeram-dynamic-test.teamgamdom.com pins a brand mirror onto the internal Basic-auth-gated nginx test backend; alias edge SNI not yet live
[LEARN] ACCEPTED inventory @ gamdom90480.com/gamdom90482.com: both CNAME→gamdom4567.com, Pool B, client-api 421 — 5th/6th provisioned 9047x-family alias
[LEARN] ACCEPTED recon @ gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com: staging page assets public-read (200), ListObjects/root denied (403) — closed hosting, inventory only
[LEARN] REJECTED out-of-scope @ gamdom90474/90476/90477/90478/90479/90481/90483/90484/90485/90486.com: resolve to 192.64.119.x/162.255.119.x (non-Fastly) — not brand infrastructure
[LEARN] ACCEPTED recon @ shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate fixture-wide — /.git/config /env /server-status /status /_metrics /actuator all 401 (172B) identical; no gate gap or debug leak on internal test backend; watch reduced to cert-propagation-only
[LEARN] ACCEPTED watch @ gamdom-girisi.com: SEO hub unchanged, still 17× gamdom90471.com only, no new alias advertised; provisioned fleet (90480/90482/90488/90473/90475) still unpublished → provisioning-in-progress confirmed for another cycle
[RISK] gamdom: 70 reason: Confirmed 17-host fleet (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-07 15:40:37 UTC [target] (model nemotron3)
class: AUTH
asset: gamdom80006.com/client-api (and all Pool B mirrors + gamdom4567.com)
confidence: 62
reasoning: Single shared backend (gamdom4567.com) proven via byte-identical /client-api headers + weak-ETag on /health across 9 live Pool B mirrors; auth transport confirmed as server-set same-origin cookie (credentials: same-origin, no Bearer/localStorage) from passive bundle analysis; cookie Domain attribute is host-only (no Domain=) per gd-lang cookie observation; if backend validates cookie host-agnostically, session from any mirror works on all others
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80007/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope from auth flow Set-Cookie headers
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80007 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80007/client-api and 90471/client-api
impact: Cross-domain account takeover across all 17 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship
class: AUTH
asset: gamdom.com/client-api (Pool A) and gamdom4567.com/client-api (Pool B)
confidence: 57
reasoning: gamdom.com/io/eu/vip/win/client-api returns byte-identical GET 400 body as Pool B mirrors; shared origin backend signature spans entire 17-host brand; if session cookie Domain attribute is .gamdom.com or host-agnostic at backend, flagship domain + 4 regional TLDs join replay surface
evidence_needed: Set-Cookie Domain attribute from auth flow on gamdom.com vs mirrors; whether cookie from gamdom.com accepted by gamdom4567.com/client-api
verify_steps: (passive) compare Set-Cookie headers on auth endpoints across gamdom.com (Pool A) and gamdom80006.com (Pool B); (AUTH_HELPED) replay flagship session cookie against Pool B /client-api
impact: Cross-pool ATO across flagship + 4 regional TLDs + all 9 mirrors + 3 aliases; high
testability: AUTH_HELPED
[HYP] teamgamdom.com Basic-auth test backend exposes internal staging endpoints via shreeram-dynamic-test subdomain
class: MISCONFIG
asset: shreeram-dynamic-test.teamgamdom.com
confidence: 48
reasoning: teamgamdom.com is 2nd in-scope brand TLD on Fastly Pool A with Certainly cert; shreeram-dynamic-test.teamgamdom.com serves nginx with Basic auth realm="secret" (fixture-wide 401); gamdom90488.com CNAME points to this test backend, pinning a brand mirror alias onto internal infrastructure; if credentials are weak/default or auth bypass exists, internal staging endpoints could be exposed
evidence_needed: Weak/default credentials for Basic auth realm="secret"; or auth bypass on nginx config; or exposed internal API endpoints behind auth
verify_steps: (passive) enumerate paths on shreeram-dynamic-test.teamgamdom.com with common admin/staging paths (/api, /admin, /health, /metrics, /actuator, /debug); (AUTH_HELPED) test common weak credentials (admin:admin, admin:secret, gamdom:gamdom, test:test) against Basic auth
impact: Access to internal staging/test backend potentially exposing debug endpoints, config, or non-prod wallet/auth logic; medium
testability: AUTH_HELPED
[PARKED] teamgamdom.com Basic-auth test backend exposes internal staging endpoints via shreeram-dynamic-test subdomain: confidence 48 but verify_steps requires AUTH_HELPED credential testing; Basic auth on internal test fixture is low business value unless it guards sensitive non-prod wallet endpoints; no evidence of actual sensitive exposure yet
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 62, AUTH_HELPED testability, medium-high impact)
[FINAL] 2. Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (confidence 57, AUTH_HELPED testability, high impact)
[NEXT] PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response headers/body to baseline (no cookie) to detect any differential signaling session recognition at shared backend without mutation (<=1 rps, no body, read-only)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" + /client-api 400 (26B) byte-identical — confirmed 9th live mirror, 15th hostname in brand trust pool; off the 7-monitor status page
[LEARN] ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool; off the 7-monitor status page
[LEARN] REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure
[LEARN] REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure
[LEARN] ACCEPTED inventory @ teamgamdom.com: 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert same CA as mirror fleet) hosting staging.teamgamdom.com (live "Maintenance - Gamdom.com" 503 page) + shreeram-dynamic-test.teamgamdom.com (nginx Basic realm="secret" fixture-wide 401) — new origin realm distinct from gamdom4567 Starlette pool
[LEARN] ACCEPTED recon @ gamdom90488.com: public numbered alias CNAME→shreeram-dynamic-test.teamgamdom.com pins a brand mirror onto the internal Basic-auth-gated nginx test backend; alias edge SNI not yet live
[LEARN] ACCEPTED inventory @ gamdom90480.com/gamdom90482.com: both CNAME→gamdom4567.com, Pool B, client-api 421 — 5th/6th provisioned 9047x-family alias
[LEARN] ACCEPTED recon @ gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com: staging page assets public-read (200), ListObjects/root denied (403) — closed hosting, inventory only
[LEARN] REJECTED out-of-scope @ gamdom90474/90476/90477/90478/90479/90481/90483/90484/90485/90486.com: resolve to 192.64.119.x/162.255.119.x (non-Fastly) — not brand infrastructure
[RISK] gamdom: 70 reason: Confirmed 17-host fleet (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
[PRIO] gamdom80006.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80007.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80003.com/client-api,9.0,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom90471.com/client-api,8.7,attack_surface=9 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.eu/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.io/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.vip/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.win/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] shreeram-dynamic-test.teamgamdom.com,7.2,attack_surface=7 business_value=7 tech_exposure=8 gate_ease=4 cloud_surface=7 freshness=8
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
[HYP] teamgamdom.com Basic-auth test backend exposes internal staging endpoints via shreeram-dynamic-test subdomain
class: MISCONFIG
asset: shreeram-dynamic-test.teamgamdom.com
confidence: 48
reasoning: teamgamdom.com is 2nd in-scope brand TLD on Fastly Pool A with Certainly cert; shreeram-dynamic-test.teamgamdom.com serves nginx with Basic auth realm="secret" (fixture-wide 401); gamdom90488.com CNAME points to this test backend, pinning a brand mirror alias onto internal infrastructure; if credentials are weak/default or auth bypass exists, internal staging endpoints could be exposed
evidence_needed: Weak/default credentials for Basic auth realm="secret"; or auth bypass on nginx config; or exposed internal API endpoints behind auth
verify_steps: (passive) enumerate paths on shreeram-dynamic-test.teamgamdom.com with common admin/staging paths (/api, /admin, /health, /metrics, /actuator, /debug); (AUTH_HELPED) test common weak credentials (admin:admin, admin:secret, gamdom:gamdom, test:test) against Basic auth
impact: Access to internal staging/test backend potentially exposing debug endpoints, config, or non-prod wallet/auth logic; medium
testability: AUTH_HELPED
[PARKED] teamgamdom.com Basic-auth test backend exposes internal staging endpoints via shreeram-dynamic-test subdomain: confidence 48 but verify_steps requires AUTH_HELPED credential testing; Basic auth on internal test fixture is low business value unless it guards sensitive non-prod wallet endpoints; no evidence of actual sensitive exposure yet; debug paths /.git/config /env /server-status /status /_metrics /actuator all return identical 401 (172B) — no gate gap or debug leak confirmed
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 62, AUTH_HELPED testability, medium-high impact)
[FINAL] 2. Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (confidence 57, AUTH_HELPED testability, high impact)
[NEXT] PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response headers/body to baseline (no cookie) to detect any differential signaling session recognition at shared backend without mutation (<=1 rps, no body, read-only)
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[LEARN] ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" + /client-api 400 (26B) byte-identical — confirmed 9th live mirror, 15th hostname in brand trust pool; off the 7-monitor status page
[LEARN] ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool; off the 7-monitor status page
[LEARN] REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure
[LEARN] REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure
[LEARN] ACCEPTED inventory @ teamgamdom.com: 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert same CA as mirror fleet) hosting staging.teamgamdom.com (live "Maintenance - Gamdom.com" 503 page) + shreeram-dynamic-test.teamgamdom.com (nginx Basic realm="secret" fixture-wide 401) — new origin realm distinct from gamdom4567 Starlette pool
[LEARN] ACCEPTED recon @ gamdom90488.com: public numbered alias CNAME→shreeram-dynamic-test.teamgamdom.com pins a brand mirror onto the internal Basic-auth-gated nginx test backend; alias edge SNI not yet live
[LEARN] ACCEPTED inventory @ gamdom90480.com/gamdom90482.com: both CNAME→gamdom4567.com, Pool B, client-api 421 — 5th/6th provisioned 9047x-family alias
[LEARN] ACCEPTED recon @ gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com: staging page assets public-read (200), ListObjects/root denied (403) — closed hosting, inventory only
[LEARN] REJECTED out-of-scope @ gamdom90474/90476/90477/90478/90479/90481/90483/90484/90485/90486.com: resolve to 192.64.119.x/162.255.119.x (non-Fastly) — not brand infrastructure
[LEARN] ACCEPTED recon @ shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate fixture-wide — /.git/config /env /server-status /status /_metrics /actuator all 401 (172B) identical; no gate gap or debug leak on internal test backend; watch reduced to cert-propagation-only
[LEARN] ACCEPTED watch @ gamdom-girisi.com: SEO hub unchanged, still 17× gamdom90471.com only, no new alias advertised; provisioned fleet (90480/90482/90488/90473/90475) still unpublished → provisioning-in-progress confirmed for another cycle
[RISK] gamdom: 70 reason: Confirmed 17-host fleet (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
asset: gamdom.com/client-api catch-all proxy
confidence: 45
reasoning: bundle hardcodes staffRefillConfig (30M coins/interval) + verifyProxies gate; /client-api routes by action name; Starlette origin both pools; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST.
evidence_needed: authenticated POST returning upstream-differentiated status for staff action names.
verify_steps: AUTH_HELPED only; no passive path remains.
impact: coin/wallet refill or staff-op abuse; critical if proven.
testability: AUTH_HELPED
[RISK] gamdom: 65 — fleet still 3 realms (gamdom4567 Starlette pool incl. +2 new provisioned aliases 90473/90475; teamgamdom Go/nginx realm; S3 closed). No gate/cert/S3 flip on any watched control and no alias went live; all new aliases remain 421-provisioned (no new exploitable surface). Top in-scope chain (cross-host cookie replay, 62) unchanged and human-gated; single live passive thread remains the 90488→internal-host SNI/gate watch (55). No data touched.
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
[PRIO] gamdom80006.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80007.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80003.com/client-api,9.0,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom90471.com/client-api,8.7,attack_surface=9 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.eu/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.io/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.vip/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.win/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom90488.com,5.2,attack_surface=6 business_value=7 tech_exposure=7 gate_ease=3 cloud_surface=6 freshness=8
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
[PARKED] teamgamdom.com Basic-auth test backend exposes internal staging endpoints via shreeram-dynamic-test subdomain: confidence 48 but verify_steps requires AUTH_HELPED credential testing; Basic auth on internal test fixture is low business value unless it guards sensitive non-prod wallet endpoints; no evidence of actual sensitive exposure yet; debug paths /.git/config /env /server-status /status /_metrics /actuator all return identical 401 (172B) — no gate gap or debug leak confirmed
[PARKED] Client-asserted privilege in /client-api action-name routing: confidence 45; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST; no passive path remains
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 62, AUTH_HELPED testability, medium-high impact)
[FINAL] 2. Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (confidence 57, AUTH_HELPED testability, high impact)
[FINAL] 3. Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on 2nd brand TLD teamgamdom.com (confidence 52, PASSIVE testability, medium impact)
[NEXT] PROBE: PASSIVE deployment-watch (read-only ≤1 rps): `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (TLS-NOMATCH→200/401 = alias cert deployed); `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (421→400/401 = live); `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/env` (401 baseline; change = gate flip); `curl -sS -o /dev/null -w "%{http_code}" https://gamdom80003.com/health` (200 ETag-identical = pool stable); re-grep gamdom-girisi.com for new numbered alias.
[LEARN] ACCEPTED watch @ 90472/90488/90473/90475 aliases: all still 421/TLS-NOMATCH — no alias went live; provisioning-in-progress continues
[LEARN] ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com + teamgamdom Go realm: gate holds 401 fixture-wide (7 paths), api.teamgamdom.com 404 default — no new surface
[LEARN] ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors, SEO hub still 17× 90471 only
[LEARN] ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 70 reason: Confirmed 17-host fleet (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-07 19:30:20 UTC [target] (model nemotron3)
[PRIO] gamdom80006.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10  
[PRIO] gamdom80007.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10  
[PRIO] gamdom80003.com/client-api,9.0,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10  
[PRIO] gamdom90471.com/client-api,8.7,attack_surface=9 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=9  
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9  
[PRIO] gamdom.eu/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9  
[PRIO] gamdom.io/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9  
[PRIO] gamdom.vip/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9  
[PRIO] gamdom.win/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9  
[PRIO] gamdom90488.com,5.2,attack_surface=6 business_value=7 tech_exposure=7 gate_ease=3 cloud_surface=6 freshness=8
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
[PARKED] teamgamdom.com Basic-auth test backend exposes internal staging endpoints via shreeram-dynamic-test subdomain: confidence 48 but verify_steps requires AUTH_HELPED credential testing; Basic auth on internal test fixture is low business value unless it guards sensitive non-prod wallet endpoints; no evidence of actual sensitive exposure yet; debug paths /.git/config /env /server-status /status /_metrics /actuator all return identical 401 (172B) — no gate gap or debug leak confirmed  
[PARKED] Client-asserted privilege in /client-api action-name routing: confidence 45; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST; no passive path remains  
[FINAL] 1. Cross-mirror auth cookie replay via shared /client-api origin yields ATO (confidence 62, AUTH_HELPED testability, medium-high impact)  
[FINAL] 2. Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (confidence 57, AUTH_HELPED testability, high impact)  
[FINAL] 3. Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on 2nd brand TLD teamgamdom.com (confidence 52, PASSIVE testability, medium impact)
[NEXT] PROBE: passive deployment-watch (read-only ≤1 rps): `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (TLS-NOMATCH→200/401 = alias cert deployed); `curl -sSk --resolve gamdom90488.com:443:151.101.67.52 -o /dev/null -w "%{http_code}" https://gamdom90488.com/client-api` (421→400/401 = live); `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/env` (401 baseline; change = gate flip); `curl -sS -o /dev/null -w "%{http_code}" https://gamdom80003.com/health` (200 ETag-identical = pool stable); re-grep gamdom-girisi.com for new numbered alias.
[LEARN] ACCEPTED watch @ 90472/90488/90473/90475 aliases: all still 421/TLS-NOMATCH — no alias went live; provisioning-in-progress continues  
[LEARN] ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com + teamgamdom Go realm: gate holds 401 fixture-wide (7 paths), api.teamgamdom.com 404 default — no new surface  
[LEARN] ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors, SEO hub still 17× 90471 only  
[LEARN] ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable  
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing  
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)  
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 70 reason: Confirmed 17-host fleet (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-07 22:19:20 UTC [target] (model nemotron3)
[PRIO] gamdom80006.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80007.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80003.com/client-api,9.0,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom90471.com/client-api,8.7,attack_surface=9 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.eu/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.io/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.vip/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.win/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom90488.com,5.2,attack_surface=6 business_value=7 tech_exposure=7 gate_ease=3 cloud_surface=6 freshness=8
[PRIO] shreeram-dynamic-test.teamgamdom.com,4.8,attack_surface=5 business_value=6 tech_exposure=6 gate_ease=2 cloud_surface=5 freshness=8
[PRIO] dashboard.gamdom.com,4.5,attack_surface=6 business_value=8 tech_exposure=5 gate_ease=1 cloud_surface=6 freshness=7
[PRIO] click.gamdom.com,3.2,attack_surface=4 business_value=3 tech_exposure=4 gate_ease=3 cloud_surface=4 freshness=6
[PRIO] gamdom80006.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80007.com/client-api,9.1,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom80003.com/client-api,9.0,attack_surface=10 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=10
[PRIO] gamdom90471.com/client-api,8.7,attack_surface=9 business_value=10 tech_exposure=9 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.com/client-api,8.6,attack_surface=9 business_value=10 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.eu/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.io/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.vip/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom.win/client-api,8.4,attack_surface=9 business_value=9 tech_exposure=8 gate_ease=6 cloud_surface=8 freshness=9
[PRIO] gamdom90488.com,5.2,attack_surface=6 business_value=7 tech_exposure=7 gate_ease=3 cloud_surface=6 freshness=8
[PRIO] shreeram-dynamic-test.teamgamdom.com,4.8,attack_surface=5 business_value=6 tech_exposure=6 gate_ease=2 cloud_surface=5 freshness=8
[PRIO] dashboard.gamdom.com,4.5,attack_surface=6 business_value=8 tech_exposure=5 gate_ease=1 cloud_surface=6 freshness=7
[PRIO] click.gamdom.com,3.2,attack_surface=4 business_value=3 tech_exposure=4 gate_ease=3 cloud_surface=4 freshness=6
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
[NEXT] PROBE: passive deployment-watch (read-only ≤1 rps):
[LEARN] ACCEPTED watch @ 90472/90488/90473/90475 aliases: all still 421/TLS-NOMATCH — no alias went live; provisioning-in-progress continues
[LEARN] ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com + teamgamdom Go realm: gate holds 401 fixture-wide (7 paths), api.teamgamdom.com 404 default — no new surface
[LEARN] ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors, SEO hub still 17× 90471 only
[LEARN] ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 70 reason: Confirmed 17-host fleet (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-08 00:44:53 UTC [target] (model nemotron3)
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
## 2026-09-08 05:22:09 UTC [target] (model nemotron3)
[NEW] gamdom80003.com confirmed as 9th live mirror (Pool B): /health 200 weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical; /client-api 400 byte-identical across all 9 mirrors
[NEW] gamdom90488.com / gamdom90472.com / gamdom90480.com / gamdom90482.com all persist TLS-NOMATCH (cert not deployed); forced-resolve to edge returns 421 on /client-api
[CHANGED] gamdommirrors.com/status/gamdom-domains API still exactly 7 monitors (com/eu/io/vip/win/80006/80007) — provisioned fleet (90471/90472/80003/80004/90480/90482/90488) still off monitor list
[CHANGED] gamdom-girisi.com SEO hub unchanged — only gamdom90471.com referenced (17×)
[CHANGED] shreeram-dynamic-test.teamgamdom.com Basic realm="secret" gate holds 401 fixture-wide (7 paths tested)
[CHANGED] Starlette pool signature stable: /client-api 400 body md5 7e3a161d + /health weak-ETag byte-identical across Pool A (gamdom.com) + Pool B (80006/80007/80003) — shared origin confirmed
[CHANGED] teamgamdom.com Go realm (api.teamgamdom.com) returns 404 default; VCL fingerprint `bong_ke` confirmed distinct from Starlette pool
[PRIO] gamdom80006.com/client-api,8.5,attack_surface=10,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=9
[PRIO] gamdom.com/client-api,8.2,attack_surface=9,business_value=10,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=8
[PRIO] gamdom4567.com/client-api,8.0,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=5,cloud_surface=8,freshness=9
[PRIO] gamdom90471.com/client-api,7.3,attack_surface=8,business_value=8,tech_exposure=8,gate_ease=5,cloud_surface=8,freshness=9
[PRIO] gamdom80003.com/client-api,8.3,attack_surface=10,business_value=9,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom.eu/client-api,7.8,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=9
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
[HYP] Provisioned alias gamdom90480.com wired to gamdom4567.com origin — cert deployment would extend Pool B trust boundary
class: MISCONFIG
asset: gamdom90480.com
confidence: 48
reasoning: gamdom90480.com CNAME→gamdom4567.com, Fastly Pool B DNS, /client-api returns 421 on forced-resolve (TLS cert mismatch); same origin as 80003/80006/80007/90471; cert deployment would add 10th live Pool B mirror to trust pool; SEO hub unchanged (no public reference yet)
evidence_needed: TLS cert deployment (TLS-NOMATCH → 200/400 on /client-api); Set-Cookie headers on auth flow if live
verify_steps: PASSIVE — `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90480.com/` (TLS-NOMATCH→200/400 = cert live); `curl -sSk --resolve gamdom90480.com:443:151.101.67.72 -I https://gamdom90480.com/client-api` (421→400 = live); compare /health weak-ETag
impact: Extends cross-mirror replay surface to 10th Pool B host; medium
testability: PASSIVE
[PARKED] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on 2nd brand TLD teamgamdom.com: confidence 52 but no live cert deployment after 4+ cycles; purely speculative until TLS-NOMATCH resolves; no active bypass vector
[PARKED] Client-asserted privilege in /client-api action-name routing: confidence 45; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST; no passive path remains
[FINAL] 1) Cross-mirror auth cookie replay via shared /client-api origin yields ATO (62)
[FINAL] 2) Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (57)
[FINAL] 3) Provisioned alias gamdom90480.com wired to gamdom4567.com origin — cert deployment would extend Pool B trust boundary (48)
[NEXT] PROBE: passive auth flow cookie inspection — `curl -sS -I https://gamdom80006.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80007.com and gamdom.com; ≤1 rps, HEAD only
[LEARN] ACCEPTED watch @ 90472/90488/90473/90475 aliases: all still 421/TLS-NOMATCH — no alias went live; provisioning-in-progress continues
[LEARN] ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com + teamgamdom Go realm: gate holds 401 fixture-wide (7 paths), api.teamgamdom.com 404 default — no new surface
[LEARN] ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors, SEO hub still 17× 90471 only
[LEARN] ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 70 reason: Confirmed 17-host fleet (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-08 09:58:41 UTC [target] (model nemotron3)
[LEARN] ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable  
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing  
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)  
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing  
[RISK] gamdom: 70 reason: Confirmed 17-host fleet (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
[NEW] gamdom80003.com confirmed as 9th live mirror (Pool B): /health 200 weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical; /client-api 400 byte-identical across all 9 mirrors
[NEW] gamdom90488.com / gamdom90472.com / gamdom90480.com / gamdom90482.com all persist TLS-NOMATCH (cert not deployed); forced-resolve to edge returns 421 on /client-api
[CHANGED] gamdommirrors.com/status/gamdom-domains API still exactly 7 monitors (com/eu/io/vip/win/80006/80007) — provisioned fleet (90471/90472/80003/80004/90480/90482/90488) still off monitor list
[CHANGED] gamdom-girisi.com SEO hub unchanged — only gamdom90471.com referenced (17×)
[CHANGED] shreeram-dynamic-test.teamgamdom.com Basic realm="secret" gate holds 401 fixture-wide (7 paths tested)
[CHANGED] Starlette pool signature stable: /client-api 400 body md5 7e3a161d + /health weak-ETag byte-identical across Pool A (gamdom.com) + Pool B (80006/80007/80003) — shared origin confirmed
[CHANGED] teamgamdom.com Go realm (api.teamgamdom.com) returns 404 default; VCL fingerprint `bong_ke` confirmed distinct from Starlette pool
[PRIO] gamdom80006.com/client-api,8.5,attack_surface=10,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=9
[PRIO] gamdom.com/client-api,8.2,attack_surface=9,business_value=10,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=8
[PRIO] gamdom4567.com/client-api,8.0,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=5,cloud_surface=8,freshness=9
[PRIO] gamdom90471.com/client-api,7.3,attack_surface=8,business_value=8,tech_exposure=8,gate_ease=5,cloud_surface=8,freshness=9
[PRIO] gamdom80003.com/client-api,8.3,attack_surface=10,business_value=9,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom.eu/client-api,7.8,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=9
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
[HYP] Provisioned alias gamdom90480.com wired to gamdom4567.com origin — cert deployment would extend Pool B trust boundary
class: MISCONFIG
asset: gamdom90480.com
confidence: 48
reasoning: gamdom90480.com CNAME→gamdom4567.com, Fastly Pool B DNS, /client-api returns 421 on forced-resolve (TLS cert mismatch); same origin as 80003/80006/80007/90471; cert deployment would add 10th live Pool B mirror to trust pool; SEO hub unchanged (no public reference yet)
evidence_needed: TLS cert deployment (TLS-NOMATCH → 200/400 on /client-api); Set-Cookie headers on auth flow if live
verify_steps: PASSIVE — `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90480.com/` (TLS-NOMATCH→200/400 = cert live); `curl -sSk --resolve gamdom90480.com:443:151.101.67.72 -I https://gamdom90480.com/client-api` (421→400 = live); compare /health weak-ETag
impact: Extends cross-mirror replay surface to 10th Pool B host; medium
testability: PASSIVE
[PARKED] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on 2nd brand TLD teamgamdom.com: confidence 52 but no live cert deployment after 4+ cycles; purely speculative until TLS-NOMATCH resolves; no active bypass vector
[PARKED] Client-asserted privilege in /client-api action-name routing: confidence 45; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST; no passive path remains
[FINAL] 1) Cross-mirror auth cookie replay via shared /client-api origin yields ATO (62)
[FINAL] 2) Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (57)
[FINAL] 3) Provisioned alias gamdom90480.com wired to gamdom4567.com origin — cert deployment would extend Pool B trust boundary (48)
[NEXT] PROBE: passive auth flow cookie inspection — `curl -sS -I https://gamdom80006.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80007.com and gamdom.com; ≤1 rps, HEAD only
[LEARN] ACCEPTED watch @ 90472/90488/90473/90475 aliases: all still 421/TLS-NOMATCH — no alias went live; provisioning-in-progress continues
[LEARN] ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com + teamgamdom Go realm: gate holds 401 fixture-wide (7 paths), api.teamgamdom.com 404 default — no new surface
[LEARN] ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors, SEO hub still 17× 90471 only
[LEARN] ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 70 reason: Confirmed 17-host fleet (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned) sharing single POST-only `/client-api` identity/wallet proxy; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to flagship gamdom.com + 4 regional TLDs via byte-identical /client-api signature; top hypothesis (cross-domain ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
[NEW] fatbets.com + gamdom.one discovered as 2nd/3rd brands on shared Pool A (Fastly 151.101.x.52): byte-identical app bundle + `/client-api` md5 7e3a161d + `/health` weak-ETag + host-only `gd-lang` cookie → identity/wallet origin now serves **2 brands / 19+ hostnames**
[NEW] flagship bundle leaks dual `staffRefillConfig` (30M/75M coins) + moderator tip cap client-side; `trMirrorDomain=gamdom80004.com` hardcoded
[NEW] fatbets.com/gamdom.one `/auth/login` + `/graphql` → 404, only host-only `gd-lang` Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth
[CHANGED] Provisioning cycle 5 for aliases (90472/90473/90475/90480/90482/90488): all still 421/TLS-NOMATCH — no cert deployment
[CHANGED] gamdommirrors.com status page still exactly 7 monitors; SEO hub still 17× gamdom90471 only
[CHANGED] Starlette pool signature stable: `/client-api` 400 md5 7e3a161d + `/health` weak-ETag byte-identical Pool A + Pool B — shared origin confirmed
[CHANGED] shreeram-dynamic-test.teamgamdom.com Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm (api.teamgamdom.com) 404 + VCL `bong_ke`
[PRIO] fatbets.com/client-api,8.6,attack_surface=10,business_value=9,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom.one/client-api,8.6,attack_surface=10,business_value=9,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom80006.com/client-api,8.5,attack_surface=10,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=9
[PRIO] gamdom.com/client-api,8.2,attack_surface=9,business_value=10,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=8
[PRIO] gamdom4567.com/client-api,8.0,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=5,cloud_surface=8,freshness=9
[PRIO] gamdom80003.com/client-api,8.3,attack_surface=10,business_value=9,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom.eu/client-api,7.8,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=9
[PRIO] gamdom90471.com/client-api,7.3,attack_surface=8,business_value=8,tech_exposure=8,gate_ease=5,cloud_surface=8,freshness=9
[HYP] Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one
class: AUTH
asset: fatbets.com/client-api
confidence: 65
reasoning: fatbets.com + gamdom.one confirmed on Pool A with byte-identical /client-api signature (md5 7e3a161d), /health weak-ETag, and host-only gd-lang cookie policy identical to Gamdom fleet; single shared backend now serves 2 brands / 19+ hostnames; auth transport is server-set same-origin cookie (no Bearer/localStorage); if backend validates cookie host-agnostically across brands, session from any Gamdom mirror works on fatbets/gamdom.one and vice versa
evidence_needed: Set-Cookie Domain/Path/SameSite attributes from auth flow on fatbets.com vs gamdom.com; whether session cookie minted on gamdom80006 accepted by fatbets.com/client-api
verify_steps: (passive) `curl -sS -I https://fatbets.com/auth/login` (or actual login endpoint) capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80006.com; (AUTH_HELPED) authenticate on gamdom80006, replay cookie on fatbets.com/client-api and gamdom.one/client-api
impact: Cross-brand account takeover across 2 brands / 19+ hostnames; high
testability: AUTH_HELPED
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
[PARKED] Provisioned alias gamdom90480.com wired to gamdom4567.com origin — cert deployment would extend Pool B trust boundary: confidence 48 < 50 threshold; purely speculative until TLS-NOMATCH resolves; no active bypass vector
[PARKED] Client-asserted privilege in /client-api action-name routing: confidence 45; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST; no passive path remains
[PARKED] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on teamgamdom.com: confidence 52 but no live cert deployment after 5 cycles; purely speculative until TLS-NOMATCH resolves; no active bypass vector
[FINAL] 1) Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one (65)
[FINAL] 2) Cross-mirror auth cookie replay via shared /client-api origin yields ATO (62)
[FINAL] 3) Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (57)
[NEXT] PROBE: passive auth flow cookie inspection — `curl -sS -I https://fatbets.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80006.com and gamdom.com; ≤1 rps, HEAD only
[LEARN] ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identical app/bundle + `/client-api` md5 7e3a161d + `/health` weak-ETag + host-only gd-lang → shared identity/wallet origin serves 2 brands / 19+ hostnames
[LEARN] ACCEPTED recon @ fatbets.com + gamdom.one: `/auth/login` + `/graphql` → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth; replay remains backend-level
[LEARN] ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; `trMirrorDomain=gamdom80004.com`
[LEARN] ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress
[LEARN] ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 75 reason: Confirmed 19-host fleet across 2 brands (Gamdom: flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned; fatbets.com + gamdom.one) sharing single POST-only `/client-api` identity/wallet proxy with byte-identical signature (md5 7e3a161d) and weak-ETag; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to 2 brands via byte-identical /client-api signature; top hypothesis (cross-brand ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); client-side bundle leaks privileged config (staffRefillConfig 30M/75M coins, moderator tip cap, trMirrorDomain); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-08 14:26:47 UTC [target] (model nemotron3)
## 2026-09-08 18:11:24 UTC [target] (model nemotron3)
[NEW] gamdom80008.com went live: CNAME→gamdom4567.com Fastly Pool B, root 200, /client-api 400 (md5 7e3a161d), /health weak-ETag byte-identical, gd-lang cookie; status page monitor id:221 replaces gamdom80007.com as 7th listed domain — mirror fleet rotation event, 10th live mirror / 20th hostname in trust pool
[NEW] gamdom80007.com demoted: 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbered aliases by redirect
[NEW] gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames
[NEW] fatbets.com + gamdom.one: 2nd/3rd brands on shared Pool A (Fastly 151.101.x.52) — byte-identical app bundle + /client-api md5 7e3a161d + /health weak-ETag + host-only gd-lang cookie → single identity/wallet origin serves 2 brands / 19+ hostnames
[NEW] flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; trMirrorDomain=gamdom80004.com hardcoded
[NEW] fatbets.com/gamdom.one /auth/login + /graphql → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth
[CHANGED] Provisioning cycle 5 for 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — no cert deployment
[CHANGED] gamdommirrors.com status page: still exactly 7 monitors; SEO hub still 17× gamdom90471 only
[CHANGED] Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed stable
[CHANGED] shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm 404 + VCL bong_ke
[PRIO] fatbets.com/client-api,8.6,attack_surface=10,business_value=9,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom.one/client-api,8.6,attack_surface=10,business_value=9,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom80006.com/client-api,8.5,attack_surface=10,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=9
[PRIO] gamdom80008.com/client-api,8.5,attack_surface=10,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom.com/client-api,8.2,attack_surface=9,business_value=10,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=8
[PRIO] gamdom4567.com/client-api,8.0,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=5,cloud_surface=8,freshness=9
[PRIO] gamdom80003.com/client-api,8.3,attack_surface=10,business_value=9,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom.eu/client-api,7.8,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=9
[PRIO] gamdom90471.com/client-api,7.3,attack_surface=8,business_value=8,tech_exposure=8,gate_ease=5,cloud_surface=8,freshness=9
[PRIO] gamdom90488.com/client-api,7.0,attack_surface=8,business_value=7,tech_exposure=8,gate_ease=5,cloud_surface=8,freshness=10
[HYP] Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one
class: AUTH
asset: fatbets.com/client-api
confidence: 65
reasoning: fatbets.com + gamdom.one confirmed on Pool A with byte-identical /client-api signature (md5 7e3a161d), /health weak-ETag, and host-only gd-lang cookie policy identical to Gamdom fleet; single shared backend now serves 2 brands / 19+ hostnames; auth transport is server-set same-origin cookie (no Bearer/localStorage); if backend validates cookie host-agnostically across brands, session from any Gamdom mirror works on fatbets/gamdom.one and vice versa
evidence_needed: Set-Cookie Domain/Path/SameSite attributes from auth flow on fatbets.com vs gamdom.com; whether session cookie minted on gamdom80006 accepted by fatbets.com/client-api
verify_steps: (passive) `curl -sS -I https://fatbets.com/auth/login` (or actual login endpoint) capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80006.com; (AUTH_HELPED) authenticate on gamdom80006, replay cookie on fatbets.com/client-api and gamdom.one/client-api
impact: Cross-brand account takeover across 2 brands / 19+ hostnames; high
testability: AUTH_HELPED
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80006.com/client-api
confidence: 62
reasoning: Single shared backend (gamdom4567.com) proven via byte-identical /client-api headers + weak-ETag on /health across 9 live Pool B mirrors; auth transport confirmed as server-set same-origin cookie (credentials: same-origin, no Bearer/localStorage) from passive bundle analysis; cookie Domain attribute is host-only (no Domain=) per gd-lang cookie observation; if backend validates cookie host-agnostically, session from any mirror works on all others
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80007/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope from auth flow Set-Cookie headers
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80008 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80008/client-api and 90471/client-api
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
[PARKED] Provisioned alias gamdom90480.com wired to gamdom4567.com origin — cert deployment would extend Pool B trust boundary: confidence 48 < 50 threshold; purely speculative until TLS-NOMATCH resolves; no active bypass vector
[PARKED] Client-asserted privilege in /client-api action-name routing: confidence 45; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST; no passive path remains
[PARKED] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on teamgamdom.com: confidence 52 but no live cert deployment after 5+ cycles; purely speculative until TLS-NOMATCH resolves; no active bypass vector
[FINAL] 1) Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one (65)
[FINAL] 2) Cross-mirror auth cookie replay via shared /client-api origin yields ATO (62)
[FINAL] 3) Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (57)
[NEXT] PROBE: passive auth flow cookie inspection — `curl -sS -I https://fatbets.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80006.com and gamdom.com; ≤1 rps, HEAD only
[LEARN] ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); official status page monitor id:221 placed it as 7th listed domain REPLACING gamdom80007.com — mirror fleet rotation event, 10th live mirror / 20th hostname in trust pool
[LEARN] ACCEPTED inventory @ gamdom80007.com: demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbered aliases by redirect, 80008 inherits its Pool B slot
[LEARN] ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames, off the 7-monitor status page
[LEARN] ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identical app/bundle + `/client-api` md5 7e3a161d + `/health` weak-ETag + host-only gd-lang → shared identity/wallet origin serves 2 brands / 19+ hostnames
[LEARN] ACCEPTED recon @ fatbets.com + gamdom.one: `/auth/login` + `/graphql` → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth; replay remains backend-level
[LEARN] ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; `trMirrorDomain=gamdom80004.com`
[LEARN] ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress
[LEARN] ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 75 reason: Confirmed 19-host fleet across 2 brands (Gamdom: flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned + gamdom80008.com live + gamdom80001/80002 provisioned; fatbets.com + gamdom.one) sharing single POST-only `/client-api` identity/wallet proxy with byte-identical signature (md5 7e3a161d) and weak-ETag; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to 2 brands via byte-identical /client-api signature; top hypothesis (cross-brand ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); client-side bundle leaks privileged config (staffRefillConfig 30M/75M coins, moderator tip cap, trMirrorDomain); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-08 20:54:26 UTC [target] (model nemotron3)
[NEW] gamdom80008.com went live: CNAME→gamdom4567.com Fastly Pool B, root 200, /client-api 400 (md5 7e3a161d), /health weak-ETag byte-identical, gd-lang cookie; status page monitor id:221 replaces gamdom80007.com as 7th listed domain
[NEW] gamdom80007.com demoted: 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — brand retires numbered aliases by redirect
[NEW] gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames
[NEW] fatbets.com + gamdom.one confirmed as 2nd/3rd brands on shared Pool A (Fastly 151.101.x.52): byte-identical app bundle + /client-api md5 7e3a161d + /health weak-ETag + host-only gd-lang cookie → single identity/wallet origin serves 2 brands / 19+ hostnames
[NEW] flagship bundle leaks dual staffRefillConfig (30M/75M coins) + moderator tip cap client-side; trMirrorDomain=gamdom80004.com hardcoded
[NEW] fatbets.com/gamdom.one /auth/login + /graphql → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth
[CHANGED] Provisioning cycle 5 for 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — no cert deployment
[CHANGED] gamdommirrors.com status page: still exactly 7 monitors; SEO hub still 17× gamdom90471 only
[CHANGED] Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed stable
[CHANGED] shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm 404 + VCL bong_ke
[PRIO] fatbets.com/client-api,8.6,attack_surface=10,business_value=9,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom.one/client-api,8.6,attack_surface=10,business_value=9,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom80008.com/client-api,8.5,attack_surface=10,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom80006.com/client-api,8.5,attack_surface=10,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=9
[PRIO] gamdom.com/client-api,8.2,attack_surface=9,business_value=10,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=8
[PRIO] gamdom4567.com/client-api,8.0,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=5,cloud_surface=8,freshness=9
[PRIO] gamdom80003.com/client-api,8.3,attack_surface=10,business_value=9,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom.eu/client-api,7.8,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=9
[PRIO] gamdom90471.com/client-api,7.3,attack_surface=8,business_value=8,tech_exposure=8,gate_ease=5,cloud_surface=8,freshness=9
[PRIO] gamdom90488.com/client-api,7.0,attack_surface=8,business_value=7,tech_exposure=8,gate_ease=5,cloud_surface=8,freshness=10
[HYP] Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one
class: AUTH
asset: fatbets.com/client-api
confidence: 65
reasoning: fatbets.com + gamdom.one confirmed on Pool A with byte-identical /client-api signature (md5 7e3a161d), /health weak-ETag, and host-only gd-lang cookie policy identical to Gamdom fleet; single shared backend now serves 2 brands / 19+ hostnames; auth transport is server-set same-origin cookie (no Bearer/localStorage); if backend validates cookie host-agnostically across brands, session from any Gamdom mirror works on fatbets/gamdom.one and vice versa
evidence_needed: Set-Cookie Domain/Path/SameSite attributes from auth flow on fatbets.com vs gamdom.com; whether session cookie minted on gamdom80006 accepted by fatbets.com/client-api
verify_steps: (passive) `curl -sS -I https://fatbets.com/auth/login` capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80006.com; (AUTH_HELPED) authenticate on gamdom80006, replay cookie on fatbets.com/client-api and gamdom.one/client-api
impact: Cross-brand account takeover across 2 brands / 19+ hostnames; high
testability: AUTH_HELPED
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80008.com/client-api
confidence: 62
reasoning: Single shared backend (gamdom4567.com) proven via byte-identical /client-api headers + weak-ETag on /health across 9 live Pool B mirrors; auth transport confirmed as server-set same-origin cookie (credentials: same-origin, no Bearer/localStorage) from passive bundle analysis; cookie Domain attribute is host-only (no Domain=) per gd-lang cookie observation; if backend validates cookie host-agnostically, session from any mirror works on all others
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80008/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope from auth flow Set-Cookie headers
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80008 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80008/client-api and 90471/client-api
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
[PARKED] Provisioned alias gamdom90480.com wired to gamdom4567.com origin — cert deployment would extend Pool B trust boundary: confidence 48 < 50 threshold; purely speculative until TLS-NOMATCH resolves; no active bypass vector
[PARKED] Client-asserted privilege in /client-api action-name routing: confidence 45; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST; no passive path remains
[PARKED] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on teamgamdom.com: confidence 52 but no live cert deployment after 5+ cycles; purely speculative until TLS-NOMATCH resolves; no active bypass vector
[FINAL] 1) Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one (65)
[FINAL] 2) Cross-mirror auth cookie replay via shared /client-api origin yields ATO (62)
[FINAL] 3) Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (57)
[NEXT] PROBE: passive auth flow cookie inspection — `curl -sS -I https://fatbets.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80006.com and gamdom.com; ≤1 rps, HEAD only
[LEARN] ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); official status page monitor id:221 placed it as 7th listed domain REPLACING gamdom80007.com — mirror fleet rotation event, 10th live mirror / 20th hostname in trust pool
[LEARN] ACCEPTED inventory @ gamdom80007.com: demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbered aliases by redirect, 80008 inherits its Pool B slot
[LEARN] ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames, off the 7-monitor status page
[LEARN] ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identical app/bundle + `/client-api` md5 7e3a161d + `/health` weak-ETag + host-only gd-lang → shared identity/wallet origin serves 2 brands / 19+ hostnames
[LEARN] ACCEPTED recon @ fatbets.com + gamdom.one: `/auth/login` + `/graphql` → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth; replay remains backend-level
[LEARN] ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; `trMirrorDomain=gamdom80004.com`
[LEARN] ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress
[LEARN] ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 75 reason: Confirmed 20-host fleet across 3 brands (Gamdom: flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned + gamdom80008.com live + gamdom80001/80002 provisioned; fatbets.com + gamdom.one) sharing single POST-only `/client-api` identity/wallet proxy with byte-identical signature (md5 7e3a161d) and weak-ETag; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to 3 brands via byte-identical /client-api signature; top hypothesis (cross-brand ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); client-side bundle leaks privileged config (staffRefillConfig 30M/75M coins, moderator tip cap, trMirrorDomain); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-08 23:12:52 UTC [target] (model nemotron3)
[NEW] gamdom80008.com went live on Pool B (CNAME→gamdom4567.com, root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); status page monitor id:221 replaces gamdom80007.com as 7th listed domain — mirror fleet rotation event
[NEW] gamdom80007.com demoted to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — brand retires numbered aliases by redirect
[NEW] gamdom80001.com/gamdom80002.com newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames
[NEW] fatbets.com + gamdom.one confirmed as 2nd/3rd brands on shared Pool A (Fastly 151.101.x.52): byte-identical app bundle + /client-api md5 7e3a161d + /health weak-ETag + host-only gd-lang cookie → single identity/wallet origin serves 3 brands / 19+ hostnames
[NEW] flagship bundle leaks dual staffRefillConfig (30M/75M coins) + moderator tip cap client-side; trMirrorDomain=gamdom80004.com hardcoded
[NEW] fatbets.com/gamdom.one /auth/login + /graphql → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth
[CHANGED] Provisioning cycle 5 for 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — no cert deployment
[CHANGED] gamdommirrors.com status page: still exactly 7 monitors; SEO hub (gamdom-girisi.com) still 17× gamdom90471 only
[CHANGED] Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed stable
[CHANGED] shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm (api.teamgamdom.com) 404 + VCL bong_ke
[PRIO] fatbets.com/client-api,8.6,attack_surface=10,business_value=9,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom.one/client-api,8.6,attack_surface=10,business_value=9,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom80008.com/client-api,8.5,attack_surface=10,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom80006.com/client-api,8.5,attack_surface=10,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=9
[PRIO] gamdom.com/client-api,8.2,attack_surface=9,business_value=10,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=8
[PRIO] gamdom4567.com/client-api,8.0,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=5,cloud_surface=8,freshness=9
[PRIO] gamdom80003.com/client-api,8.3,attack_surface=10,business_value=9,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom.eu/client-api,7.8,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=9
[PRIO] gamdom90471.com/client-api,7.3,attack_surface=8,business_value=8,tech_exposure=8,gate_ease=5,cloud_surface=8,freshness=9
[PRIO] gamdom90488.com/client-api,7.0,attack_surface=8,business_value=7,tech_exposure=8,gate_ease=5,cloud_surface=8,freshness=10
[PRIO] teamgamdom.com/client-api,6.8,attack_surface=8,business_value=8,tech_exposure=7,gate_ease=5,cloud_surface=8,freshness=8
[PRIO] shreeram-dynamic-test.teamgamdom.com,5.5,attack_surface=6,business_value=6,tech_exposure=7,gate_ease=3,cloud_surface=7,freshness=8
[HYP] Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one
class: AUTH
asset: fatbets.com/client-api
confidence: 66
reasoning: fatbets.com + gamdom.one confirmed on Pool A with byte-identical /client-api signature (md5 7e3a161d), /health weak-ETag, and host-only gd-lang cookie policy identical to Gamdom fleet; single shared backend now serves 3 brands / 19+ hostnames; auth transport is server-set same-origin cookie (no Bearer/localStorage); if backend validates cookie host-agnostically across brands, session from any Gamdom mirror works on fatbets/gamdom.one and vice versa
evidence_needed: Set-Cookie Domain/Path/SameSite attributes from auth flow on fatbets.com vs gamdom.com; whether session cookie minted on gamdom80006 accepted by fatbets.com/client-api
verify_steps: (passive) `curl -sS -I https://fatbets.com/auth/login` capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80006.com; (AUTH_HELPED) authenticate on gamdom80006, replay cookie on fatbets.com/client-api and gamdom.one/client-api
impact: Cross-brand account takeover across 3 brands / 19+ hostnames; high
testability: AUTH_HELPED
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80008.com/client-api
confidence: 63
reasoning: Single shared backend (gamdom4567.com) proven via byte-identical /client-api headers + weak-ETag on /health across 9 live Pool B mirrors; auth transport confirmed as server-set same-origin cookie (credentials: same-origin, no Bearer/localStorage) from passive bundle analysis; cookie Domain attribute is host-only (no Domain=) per gd-lang cookie observation; if backend validates cookie host-agnostically, session from any mirror works on all others
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80008/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope from auth flow Set-Cookie headers
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80008 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80008/client-api and 90471/client-api
impact: Cross-domain account takeover across all 17 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship
class: AUTH
asset: gamdom.com/client-api
confidence: 58
reasoning: gamdom.com/io/eu/vip/win/client-api returns byte-identical GET 400 body as Pool B mirrors; shared origin backend signature spans entire 17-host brand; if session cookie Domain attribute is .gamdom.com or host-agnostic at backend, flagship domain + 4 regional TLDs join replay surface
evidence_needed: Set-Cookie Domain attribute from auth flow on gamdom.com vs mirrors; whether cookie from gamdom.com accepted by gamdom4567.com/client-api
verify_steps: (passive) compare Set-Cookie headers on auth endpoints across gamdom.com (Pool A) and gamdom80006.com (Pool B); (AUTH_HELPED) replay flagship session cookie against Pool B /client-api
impact: Cross-pool ATO across flagship + 4 regional TLDs + all 9 mirrors + 3 aliases; high
testability: AUTH_HELPED
[PARKED] Provisioned alias gamdom90480.com wired to gamdom4567.com origin — cert deployment would extend Pool B trust boundary: confidence 48 < 50 threshold; purely speculative until TLS-NOMATCH resolves; no active bypass vector
[PARKED] Client-asserted privilege in /client-api action-name routing: confidence 45; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST; no passive path remains
[PARKED] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on teamgamdom.com: confidence 52 but no live cert deployment after 5+ cycles; purely speculative until TLS-NOMATCH resolves; no active bypass vector
[FINAL] 1) Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one (66)
[FINAL] 2) Cross-mirror auth cookie replay via shared /client-api origin yields ATO (63)
[FINAL] 3) Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (58)
[NEXT] PROBE: passive auth flow cookie inspection — `curl -sS -I https://fatbets.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80006.com and gamdom.com; ≤1 rps, HEAD only
[LEARN] ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); official status page monitor id:221 placed it as 7th listed domain REPLACING gamdom80007.com — mirror fleet rotation event, 10th live mirror / 20th hostname in trust pool
[LEARN] ACCEPTED inventory @ gamdom80007.com: demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbered aliases by redirect, 80008 inherits its Pool B slot
[LEARN] ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames, off the 7-monitor status page
[LEARN] ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identical app/bundle + `/client-api` md5 7e3a161d + `/health` weak-ETag + host-only gd-lang → shared identity/wallet origin serves 2 brands / 19+ hostnames
[LEARN] ACCEPTED recon @ fatbets.com + gamdom.one: `/auth/login` + `/graphql` → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth; replay remains backend-level
[LEARN] ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; `trMirrorDomain=gamdom80004.com`
[LEARN] ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress
[LEARN] ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 78 reason: Confirmed 20-host fleet across 3 brands (Gamdom: flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned + gamdom80008.com live + gamdom80001/80002 provisioned; fatbets.com + gamdom.one) sharing single POST-only `/client-api` identity/wallet proxy with byte-identical signature (md5 7e3a161d) and weak-ETag; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to 3 brands via byte-identical /client-api signature; top hypothesis (cross-brand ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); client-side bundle leaks privileged config (staffRefillConfig 30M/75M coins, moderator tip cap, trMirrorDomain); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-09 01:34:40 UTC [target] (model nemotron3)
[NEW] gamdom80008.com went live on Pool B (CNAME→gamdom4567.com, root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); status page monitor id:221 replaces gamdom80007.com as 7th listed mirror
[NEW] gamdom80007.com demoted to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — brand retires numbered aliases by redirect
[NEW] gamdom80001.com/gamdom80002.com newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames
[NEW] fatbets.com + gamdom.one confirmed as 2nd/3rd brands on shared Pool A (Fastly 151.101.x.52): byte-identical app bundle + /client-api md5 7e3a161d + /health weak-ETag + host-only gd-lang cookie → single identity/wallet origin serves 3 brands / 19+ hostnames
[NEW] flagship bundle leaks dual staffRefillConfig (30M/75M coins) + moderator tip cap client-side; trMirrorDomain=gamdom80004.com hardcoded
[CHANGED] Provisioning cycle 5 for 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — no cert deployment
[CHANGED] Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed stable
[CHANGED] shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm 404 + VCL bong_ke
[PRIO] fatbets.com/client-api,8.8,attack_surface=10,business_value=10,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom.one/client-api,8.8,attack_surface=10,business_value=10,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom80008.com/client-api,8.6,attack_surface=10,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom80006.com/client-api,8.6,attack_surface=10,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=9
[PRIO] gamdom.com/client-api,8.4,attack_surface=9,business_value=10,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=8
[PRIO] gamdom4567.com/client-api,8.2,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=5,cloud_surface=8,freshness=9
[PRIO] gamdom80003.com/client-api,8.4,attack_surface=10,business_value=9,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom.eu/client-api,8.0,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=9
[PRIO] gamdom90471.com/client-api,7.5,attack_surface=8,business_value=8,tech_exposure=8,gate_ease=5,cloud_surface=8,freshness=9
[PRIO] gamdom90488.com/client-api,7.2,attack_surface=8,business_value=7,tech_exposure=8,gate_ease=5,cloud_surface=8,freshness=10
[PRIO] teamgamdom.com/client-api,7.0,attack_surface=8,business_value=8,tech_exposure=7,gate_ease=5,cloud_surface=8,freshness=8
[PRIO] shreeram-dynamic-test.teamgamdom.com,5.8,attack_surface=6,business_value=6,tech_exposure=7,gate_ease=3,cloud_surface=7,freshness=8
[HYP] Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one
class: AUTH
asset: fatbets.com/client-api
confidence: 68
reasoning: fatbets.com + gamdom.one + gamdom.com confirmed on Pool A with byte-identical /client-api signature (md5 7e3a161d), /health weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", and host-only gd-lang cookie policy identical across 3 brands / 19+ hostnames; single shared backend serves all; auth transport is server-set same-origin cookie (credentials: same-origin, no Bearer/localStorage) per passive bundle analysis; if backend validates cookie host-agnostically across brands, session from any Gamdom mirror works on fatbets/gamdom.one and vice versa
evidence_needed: Set-Cookie Domain/Path/SameSite attributes from auth flow on fatbets.com vs gamdom.com; whether session cookie minted on gamdom80006 accepted by fatbets.com/client-api
verify_steps: (passive) `curl -sS -I https://fatbets.com/auth/login` capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80006.com; (AUTH_HELPED) authenticate on gamdom80006, replay cookie on fatbets.com/client-api and gamdom.one/client-api
impact: Cross-brand account takeover across 3 brands / 19+ hostnames; high
testability: AUTH_HELPED
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80008.com/client-api
confidence: 65
reasoning: Single shared backend (gamdom4567.com) proven via byte-identical /client-api headers + weak-ETag on /health across 9 live Pool B mirrors + Pool A flagship; auth transport confirmed as server-set same-origin cookie (credentials: same-origin, no Bearer/localStorage) from passive bundle analysis; cookie Domain attribute is host-only (no Domain=) per gd-lang cookie observation; if backend validates cookie host-agnostically, session from any mirror works on all others
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80008/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope from auth flow Set-Cookie headers
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80008 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80008/client-api and 90471/client-api
impact: Cross-domain account takeover across all 17 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship
class: AUTH
asset: gamdom.com/client-api
confidence: 60
reasoning: gamdom.com/io/eu/vip/win/client-api returns byte-identical GET 400 body as Pool B mirrors; shared origin backend signature spans entire 17-host brand; if session cookie Domain attribute is .gamdom.com or host-agnostic at backend, flagship domain + 4 regional TLDs join replay surface
evidence_needed: Set-Cookie Domain attribute from auth flow on gamdom.com vs mirrors; whether cookie from gamdom.com accepted by gamdom4567.com/client-api
verify_steps: (passive) compare Set-Cookie headers on auth endpoints across gamdom.com (Pool A) and gamdom80006.com (Pool B); (AUTH_HELPED) replay flagship session cookie against Pool B /client-api
impact: Cross-pool ATO across flagship + 4 regional TLDs + all 9 mirrors + 3 aliases; high
testability: AUTH_HELPED
[PARKED] Provisioned alias gamdom90480.com wired to gamdom4567.com origin — cert deployment would extend Pool B trust boundary: confidence 48 < 50 threshold; purely speculative until TLS-NOMATCH resolves; no active bypass vector
[PARKED] Client-asserted privilege in /client-api action-name routing: confidence 45; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST; no passive path remains
[PARKED] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on teamgamdom.com: confidence 52 but no live cert deployment after 5+ cycles; purely speculative until TLS-NOMATCH resolves; no active bypass vector
[FINAL] 1) Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one (68)
[FINAL] 2) Cross-mirror auth cookie replay via shared /client-api origin yields ATO (65)
[FINAL] 3) Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (60)
[NEXT] PROBE: passive auth flow cookie inspection — `curl -sS -I https://fatbets.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80006.com and gamdom.com; ≤1 rps, HEAD only
[LEARN] ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); official status page monitor id:221 placed it as 7th listed domain REPLACING gamdom80007.com — mirror fleet rotation event, 10th live mirror / 20th hostname in trust pool
[LEARN] ACCEPTED inventory @ gamdom80007.com: demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbered aliases by redirect, 80008 inherits its Pool B slot
[LEARN] ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames, off the 7-monitor status page
[LEARN] ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identical app/bundle + `/client-api` md5 7e3a161d + `/health` weak-ETag + host-only gd-lang → shared identity/wallet origin serves 2 brands / 19+ hostnames
[LEARN] ACCEPTED recon @ fatbets.com + gamdom.one: `/auth/login` + `/graphql` → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth; replay remains backend-level
[LEARN] ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; `trMirrorDomain=gamdom80004.com`
[LEARN] ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress
[LEARN] ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 78 reason: Confirmed 20-host fleet across 3 brands (Gamdom: flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned + gamdom80008.com live + gamdom80001/80002 provisioned; fatbets.com + gamdom.one) sharing single POST-only `/client-api` identity/wallet proxy with byte-identical signature (md5 7e3a161d) and weak-ETag; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to 3 brands via byte-identical /client-api signature; top hypothesis (cross-brand ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); client-side bundle leaks privileged config (staffRefillConfig 30M/75M coins, moderator tip cap, trMirrorDomain); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-09 06:07:18 UTC [target] (model nemotron3)
[NEW] gamdom80008.com went live on Pool B (CNAME→gamdom4567.com, root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); status page monitor id:221 replaces gamdom80007.com as 7th listed mirror
[NEW] gamdom80007.com demoted to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — brand retires numbered aliases by redirect
[NEW] gamdom80001.com/gamdom80002.com newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames
[NEW] fatbets.com + gamdom.one confirmed as 2nd/3rd brands on shared Pool A (Fastly 151.101.x.52): byte-identical app bundle + /client-api md5 7e3a161d + /health weak-ETag + host-only gd-lang cookie → single identity/wallet origin serves 3 brands / 19+ hostnames
[CHANGED] Provisioning cycle 5 for 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — no cert deployment
[CHANGED] Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed stable
[CHANGED] shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm 404 + VCL bong_ke
[PRIO] fatbets.com/client-api,8.8,attack_surface=10,business_value=10,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom.one/client-api,8.8,attack_surface=10,business_value=10,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom80008.com/client-api,8.6,attack_surface=10,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=10
[HYP] Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one
class: AUTH
asset: fatbets.com/client-api
confidence: 68
reasoning: fatbets.com + gamdom.one + gamdom.com confirmed on Pool A with byte-identical /client-api signature (md5 7e3a161d), /health weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", and host-only gd-lang cookie policy identical across 3 brands / 19+ hostnames; single shared backend serves all; auth transport is server-set same-origin cookie (credentials: same-origin, no Bearer/localStorage) per passive bundle analysis; if backend validates cookie host-agnostically across brands, session from any Gamdom mirror works on fatbets/gamdom.one and vice versa
evidence_needed: Set-Cookie Domain/Path/SameSite attributes from auth flow on fatbets.com vs gamdom.com; whether session cookie minted on gamdom80006 accepted by fatbets.com/client-api
verify_steps: (passive) `curl -sS -I https://fatbets.com/auth/login` capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80006.com; (AUTH_HELPED) authenticate on gamdom80006, replay cookie on fatbets.com/client-api and gamdom.one/client-api
impact: Cross-brand account takeover across 3 brands / 19+ hostnames; high
testability: AUTH_HELPED
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80008.com/client-api
confidence: 65
reasoning: Single shared backend (gamdom4567.com) proven via byte-identical /client-api headers + weak-ETag on /health across 9 live Pool B mirrors + Pool A flagship; auth transport confirmed as server-set same-origin cookie (credentials: same-origin, no Bearer/localStorage) from passive bundle analysis; cookie Domain attribute is host-only (no Domain=) per gd-lang cookie observation; if backend validates cookie host-agnostically, session from any mirror works on all others
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80008/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope from auth flow Set-Cookie headers
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80008 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80008/client-api and 90471/client-api
impact: Cross-domain account takeover across all 17 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship
class: AUTH
asset: gamdom.com/client-api
confidence: 60
reasoning: gamdom.com/io/eu/vip/win/client-api returns byte-identical GET 400 body as Pool B mirrors; shared origin backend signature spans entire 17-host brand; if session cookie Domain attribute is .gamdom.com or host-agnostic at backend, flagship domain + 4 regional TLDs join replay surface
evidence_needed: Set-Cookie Domain attribute from auth flow on gamdom.com vs mirrors; whether cookie from gamdom.com accepted by gamdom4567.com/client-api
verify_steps: (passive) compare Set-Cookie headers on auth endpoints across gamdom.com (Pool A) and gamdom80006.com (Pool B); (AUTH_HELPED) replay flagship session cookie against Pool B /client-api
impact: Cross-pool ATO across flagship + 4 regional TLDs + all 9 mirrors + 3 aliases; high
testability: AUTH_HELPED
[PARKED] Provisioned alias gamdom90480.com wired to gamdom4567.com origin — cert deployment would extend Pool B trust boundary: confidence 48 < 50 threshold; purely speculative until TLS-NOMATCH resolves; no active bypass vector
[PARKED] Client-asserted privilege in /client-api action-name routing: confidence 45; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST; no passive path remains
[PARKED] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on teamgamdom.com: confidence 52 but no live cert deployment after 5+ cycles; purely speculative until TLS-NOMATCH resolves; no active bypass vector
[FINAL] 1) Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one (68)
[FINAL] 2) Cross-mirror auth cookie replay via shared /client-api origin yields ATO (65)
[FINAL] 3) Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (60)
[NEXT] PROBE: passive auth flow cookie inspection — `curl -sS -I https://fatbets.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80006.com and gamdom.com; ≤1 rps, HEAD only
[LEARN] ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); official status page monitor id:221 placed it as 7th listed domain REPLACING gamdom80007.com — mirror fleet rotation event, 10th live mirror / 20th hostname in trust pool
[LEARN] ACCEPTED inventory @ gamdom80007.com: demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbered aliases by redirect, 80008 inherits its Pool B slot
[LEARN] ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames, off the 7-monitor status page
[LEARN] ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identical app/bundle + `/client-api` md5 7e3a161d + `/health` weak-ETag + host-only gd-lang → shared identity/wallet origin serves 2 brands / 19+ hostnames
[LEARN] ACCEPTED recon @ fatbets.com + gamdom.one: `/auth/login` + `/graphql` → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth; replay remains backend-level
[LEARN] ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; `trMirrorDomain=gamdom80004.com`
[LEARN] ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress
[LEARN] ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 78 reason: Confirmed 20-host fleet across 3 brands (Gamdom: flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned + gamdom80008.com live + gamdom80001/80002 provisioned; fatbets.com + gamdom.one) sharing single POST-only `/client-api` identity/wallet proxy with byte-identical signature (md5 7e3a161d) and weak-ETag; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to 3 brands via byte-identical /client-api signature; top hypothesis (cross-brand ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); client-side bundle leaks privileged config (staffRefillConfig 30M/75M coins, moderator tip cap, trMirrorDomain); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
## 2026-09-09 11:50:54 UTC [target] (model nemotron3)
## 2026-09-09 15:26:32 UTC [target] (model nemotron3)
[NEW] gamdom80008.com went live on Pool B (CNAME→gamdom4567.com, root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); status page monitor id:221 replaces gamdom80007.com as 7th listed mirror — 10th live mirror / 20th hostname in trust pool
[NEW] gamdom80007.com demoted to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — brand retires numbered aliases by redirect
[NEW] gamdom80001.com/gamdom80002.com newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames
[NEW] fatbets.com + gamdom.one confirmed as 2nd/3rd brands on shared Pool A (Fastly 151.101.x.52): byte-identical app bundle + /client-api md5 7e3a161d + /health weak-ETag + host-only gd-lang cookie → single identity/wallet origin serves 3 brands / 19+ hostnames
[NEW] flagship bundle leaks dual staffRefillConfig (30M/75M coins) + moderator tip cap client-side; trMirrorDomain=gamdom80004.com hardcoded
[NEW] fatbets.com/gamdom.one /auth/login + /graphql → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth; replay remains backend-level
[CHANGED] Provisioning cycle 5 for 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — no cert deployment
[CHANGED] Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed stable
[CHANGED] shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm 404 + VCL bong_ke
[PRIO] fatbets.com/client-api,8.8,attack_surface=10,business_value=10,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom.one/client-api,8.8,attack_surface=10,business_value=10,tech_exposure=9,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom80008.com/client-api,8.6,attack_surface=10,business_value=9,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=10
[PRIO] gamdom.com/client-api,8.4,attack_surface=10,business_value=10,tech_exposure=8,gate_ease=5,cloud_surface=9,freshness=9
[PRIO] gamdom80003.com/client-api,8.2,attack_surface=10,business_value=8,tech_exposure=8,gate_ease=6,cloud_surface=9,freshness=9
[PRIO] gamdom90471.com/client-api,7.9,attack_surface=9,business_value=8,tech_exposure=8,gate_ease=6,cloud_surface=8,freshness=8
[PRIO] teamgamdom.com/client-api,6.5,attack_surface=7,business_value=7,tech_exposure=6,gate_ease=5,cloud_surface=7,freshness=7
[PRIO] shreeram-dynamic-test.teamgamdom.com/client-api,4.2,attack_surface=5,business_value=4,tech_exposure=4,gate_ease=3,cloud_surface=5,freshness=5
[HYP] Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one
class: AUTH
asset: fatbets.com/client-api
confidence: 68
reasoning: fatbets.com + gamdom.one + gamdom.com confirmed on Pool A with byte-identical /client-api signature (md5 7e3a161d), /health weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", and host-only gd-lang cookie policy identical across 3 brands / 19+ hostnames; single shared backend serves all; auth transport is server-set same-origin cookie (credentials: same-origin, no Bearer/localStorage) per passive bundle analysis; if backend validates cookie host-agnostically across brands, session from any Gamdom mirror works on fatbets/gamdom.one and vice versa
evidence_needed: Set-Cookie Domain/Path/SameSite attributes from auth flow on fatbets.com vs gamdom.com; whether session cookie minted on gamdom80006 accepted by fatbets.com/client-api
verify_steps: (passive) `curl -sS -I https://fatbets.com/auth/login` capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80006.com; (AUTH_HELPED) authenticate on gamdom80006, replay cookie on fatbets.com/client-api and gamdom.one/client-api
impact: Cross-brand account takeover across 3 brands / 19+ hostnames; high
testability: AUTH_HELPED
[HYP] Cross-mirror auth cookie replay via shared /client-api origin yields ATO
class: AUTH
asset: gamdom80008.com/client-api
confidence: 65
reasoning: Single shared backend (gamdom4567.com) proven via byte-identical /client-api headers + weak-ETag on /health across 9 live Pool B mirrors + Pool A flagship; auth transport confirmed as server-set same-origin cookie (credentials: same-origin, no Bearer/localStorage) from passive bundle analysis; cookie Domain attribute is host-only (no Domain=) per gd-lang cookie observation; if backend validates cookie host-agnostically, session from any mirror works on all others
evidence_needed: Same session cookie minted on gamdom80006 accepted by gamdom80008/client-api (or gamdom4567.com/client-api); cookie Domain attribute scope from auth flow Set-Cookie headers
verify_steps: (passive) fetch Set-Cookie headers during auth flow on gamdom80006 vs gamdom80008 vs gamdom90471; compare Domain/Path/SameSite attributes; (AUTH_HELPED) authenticate on 80006, replay cookie on 80008/client-api and 90471/client-api
impact: Cross-domain account takeover across all 17 mirror hostnames; medium-high
testability: AUTH_HELPED
[HYP] Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship
class: AUTH
asset: gamdom.com/client-api
confidence: 60
reasoning: gamdom.com/io/eu/vip/win/client-api returns byte-identical GET 400 body as Pool B mirrors; shared origin backend signature spans entire 17-host brand; if session cookie Domain attribute is .gamdom.com or host-agnostic at backend, flagship domain + 4 regional TLDs join replay surface
evidence_needed: Set-Cookie Domain attribute from auth flow on gamdom.com vs mirrors; whether cookie from gamdom.com accepted by gamdom4567.com/client-api
verify_steps: (passive) compare Set-Cookie headers on auth endpoints across gamdom.com (Pool A) and gamdom80006.com (Pool B); (AUTH_HELPED) replay flagship session cookie against Pool B /client-api
impact: Cross-pool ATO across flagship + 4 regional TLDs + all 9 mirrors + 3 aliases; high
testability: AUTH_HELPED
[PARKED] Provisioned alias gamdom90480.com wired to gamdom4567.com origin — cert deployment would extend Pool B trust boundary: confidence 48 < 50 threshold; purely speculative until TLS-NOMATCH resolves; no active bypass vector
[PARKED] Client-asserted privilege in /client-api action-name routing: confidence 45; catch-all 400s every GET subpath → action map unenumerable; privilege model untestable without authenticated POST; no passive path remains
[PARKED] Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on teamgamdom.com: confidence 52 but no live cert deployment after 5+ cycles; purely speculative until TLS-NOMATCH resolves; no active bypass vector
[FINAL] 1) Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one (68)
[FINAL] 2) Cross-mirror auth cookie replay via shared /client-api origin yields ATO (65)
[FINAL] 3) Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (60)
[NEXT] PROBE: passive auth flow cookie inspection — `curl -sS -I https://fatbets.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Domain/Path/SameSite vs gamdom80006.com and gamdom.com; ≤1 rps, HEAD only
[LEARN] ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); official status page monitor id:221 placed it as 7th listed domain REPLACING gamdom80007.com — mirror fleet rotation event, 10th live mirror / 20th hostname in trust pool
[LEARN] ACCEPTED inventory @ gamdom80007.com: demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbered aliases by redirect, 80008 inherits its Pool B slot
[LEARN] ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames, off the 7-monitor status page
[LEARN] ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identical app/bundle + `/client-api` md5 7e3a161d + `/health` weak-ETag + host-only gd-lang → shared identity/wallet origin serves 2 brands / 19+ hostnames
[LEARN] ACCEPTED recon @ fatbets.com + gamdom.one: `/auth/login` + `/graphql` → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth; replay remains backend-level
[LEARN] ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; `trMirrorDomain=gamdom80004.com`
[LEARN] ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress
[LEARN] ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable
[LEARN] REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid session + authorized scope before POST probing
[LEARN] ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains (bypasses wildcard/CDN ambiguity)
[LEARN] REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
[RISK] gamdom: 78 reason: Confirmed 20-host fleet across 3 brands (Gamdom: flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provisioned + gamdom80008.com live + gamdom80001/80002 provisioned; fatbets.com + gamdom.one) sharing single POST-only `/client-api` identity/wallet proxy with byte-identical signature (md5 7e3a161d) and weak-ETag; origin trust boundary verified (gamdom4567.com = backend for Pool B); brand-wide trust pool extended to 3 brands via byte-identical /client-api signature; top hypothesis (cross-brand ATO via session sharing) has passive verification path for origin boundary and AUTH_HELPED path for session replay; auth transport confirmed as server-set same-origin cookie (no localStorage/bearer); client-side bundle leaks privileged config (staffRefillConfig 30M/75M coins, moderator tip cap, trMirrorDomain); no confirmed HIGH-class vuln yet — best leads require authorized authenticated testing against scoped identity endpoints
