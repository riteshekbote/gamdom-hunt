# Hypotheses (ranked)

## RANKED HYPOTHESES 2026-09-02 21:57:15 UTC

## RANKED HYPOTHESES 2026-09-02 23:52:37 UTC

## RANKED HYPOTHESES 2026-09-03 02:53:37 UTC

## RANKED HYPOTHESES 2026-09-03 07:46:26 UTC

## RANKED HYPOTHESES 2026-09-03 12:35:37 UTC

## RANKED HYPOTHESES 2026-09-03 17:00:20 UTC
- [52] gamdom80006.com: Mirror-domains share wallet/auth/API trust boundary -> cross-domain ATO via client-api (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET (read-only) `https://gamdommirrors.com/api/status-page/gamdom-domains/incidents` to pull the historical incident object (UptimeRobot status page expo
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POSt to live identity/wallet proxy is prohibited (no-auth-bypass/mutate-against-live-data); require valid se
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public UptimeRobot status page of an in-scope org service is legitimate passive recon that resolves true operating 
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com is not operated by Gamdom; only reportable as brand-jacking/phishing.

## RANKED HYPOTHESES 2026-09-03 19:41:38 UTC
- [55] gamdom80006.com: Mirror-domain session sharing via /client-api enables cross-domain ATO (from art/lead_nemotron3.txt)
- [38] gamdommirrors.com: Uptime Kuma instance on gamdommirrors.com exposes unauthenticated socket.io admin/mutation events (from art/lead_bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdommirrors.com/api/status-page/gamdom-domains/incidents` to pull historical incident objects — may leak internal monitoring U
- NEXT(hypotheses-bigpickle.txt): PROBE: GET (read-only) `https://gamdom4567.com/client-api` with `Host: gamdom80007.com` via a single Fastly edge and compare response headers/body to `https://g
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public UptimeRobot status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd

## RANKED HYPOTHESES 2026-09-03 21:58:07 UTC
- [55] gamdom80006.com: Mirror-domain session sharing via /client-api enables cross-domain ATO (from art/lead_nemotron3.txt)
- [50] gamdom4567.com: gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors (from art/lead_bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdommirrors.com/socket.io/?EIO=4&transport=polling` to capture socket.io handshake (sid, upgrades, pingInterval) and enumerate
- NEXT(hypotheses-bigpickle.txt): PROBE: GET (read-only) `https://gamdom4567.com/client-api` with `Host: gamdom80007.com` and compare response headers/body to `https://gamdom80007.com/client-api
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified 7th mirror (same Fastly origin + POST-only /client-api + listed on status page) and gamdom4567.com as CNAME origi
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page publicly publishes full monitor config + 24h heartbeats for all 7 domains.

## RANKED HYPOTHESES 2026-09-03 23:50:18 UTC
- [55] gamdom80006.com: Mirror-domain session sharing via /client-api enables cross-domain ATO (from art/lead_nemotron3.txt)
- [50] gamdom4567.com: gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET (read-only) `https://gamdom4567.com/client-api` with `Host: gamdom80007.com` and compare response headers/body to `https://gamdom80007.com/client-api
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdommirrors.com/socket.io/?EIO=4&transport=polling&sid=_EDMwKXb0he9pQH_AVOG` to capture socket.io polling response and enumera
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd

## RANKED HYPOTHESES 2026-09-04 02:53:41 UTC
- [55] gamdom80006.com: Mirror-domain session sharing via /client-api enables cross-domain ATO (from art/lead_nemotron3.txt)
- [50] gamdom4567.com: gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors (from art/lead_bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom4567.com/client-api` with `Host: gamdom80007.com` and compare response headers/body to `https://gamdom80007.com/client-api
- NEXT(hypotheses-bigpickle.txt): PROBE: GET (read-only) `https://gamdom4567.com/client-api` with `Host: gamdom80007.com` and compare response headers/body to `https://gamdom80007.com/client-api
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified 7th mirror (same Fastly origin + POST-only /client-api + listed on status page) and gamdom4567.com as CNAME origi
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page publicly publishes full monitor config + 24h heartbeats for all 7 domains

## RANKED HYPOTHESES 2026-09-04 07:47:47 UTC
- [70] gamdom4567.com: gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors (from art/lead_bigpickle.txt)
- [55] gamdom80006.com: Mirror-domain session sharing via /client-api enables cross-domain ATO (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom4567.com/client-api` with `Host: gamdom80007.com` and compare response headers/body to `https://gamdom80007.com/client-api
- NEXT(hypotheses-bigpickle.txt): PROBE: GET (read-only) `https://gamdommirrors.com/socket.io/?EIO=4&transport=polling` — capture socket.io handshake response and enumerate namespaces/events; te
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd
- LEARN: ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com 

## RANKED HYPOTHESES 2026-09-04 12:45:23 UTC
- [55] gamdom80006.com: Mirror-domain session sharing via /client-api enables cross-domain ATO (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdommirrors.com/socket.io/?EIO=4&transport=polling` — capture socket.io handshake response and enumerate namespaces/events; te
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd
- LEARN: ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com 

## RANKED HYPOTHESES 2026-09-04 16:44:47 UTC
- [70] gamdom4567.com: gamdom4567.com is shadow origin with shared /client-api trust boundary across all 7 mirrors (from art/lead_bigpickle.txt)
- [55] gamdom80006.com: Mirror-domain session sharing via /client-api enables cross-domain ATO (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): HUMAN: Gamdom80006.com and gamdom4567.com hypotheses are either DONE or blocked on authenticated testing. The only remaining passive probe in the survivor queue
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdommirrors.com/socket.io/?EIO=4&transport=polling` — capture socket.io handshake response and enumerate namespaces/events; te
- LEARN: ACCEPTED origin-boundary @ gamdom4567.com: Header fingerprinting confirmed gamdom4567.com as shared origin backend for all 8000x mirrors; Fastly cert validation
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: Blind POST to live identity/wallet proxy prohibited; require valid session + authorized scope before POST probing
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: Public Uptime Kuma status page is legitimate passive recon resolving true operating domains
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd
- LEARN: ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com 

## RANKED HYPOTHESES 2026-09-04 19:16:10 UTC
- [55] gamdom80006.com: Mirror-domain session sharing via /client-api enables cross-domain ATO (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdommirrors.com/socket.io/?EIO=4&transport=polling` — capture socket.io handshake response and enumerate namespaces/events; te
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd
- LEARN: ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com 

## RANKED HYPOTHESES 2026-09-04 21:37:03 UTC
- [55] gamdom80006.com: Mirror-domain session sharing via /client-api enables cross-domain ATO (from art/lead_nemotron3.txt)
- [55] gamdom80006.com: Cross-mirror auth token/session replay via shared /client-api origin yields ATO (from art/lead_bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdommirrors.com/socket.io/?EIO=4&transport=polling` — capture socket.io handshake response and enumerate namespaces/events; te
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd
- LEARN: ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com 

## RANKED HYPOTHESES 2026-09-04 23:19:40 UTC
- [55] gamdom80006.com: Cross-mirror auth token/session replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [55] gamdom4567.com: Cross-mirror session replay: cookie auth is hostname-agnostic at the shared origin (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: AUTH_HELPED human gate — this session's passive work is exhausted. Exact passive read-only confirmation left: compare `Diffie`... none. Recommend the aut
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdommirrors.com/socket.io/?EIO=4&transport=polling` — capture socket.io handshake response and enumerate namespaces/events; te
- LEARN: ACCEPTED recon @ gamdom80006.com/build/client.<hash>.js: passive fetch of the app's own request-layer bundle (597 KB) proved auth transport is server-set same-o
- LEARN: ACCEPTED recon @ gamdommirrors.com/socket.io: GET-only EIO=4 polling handshake issues SID with websocket upgrade; no admin surface reachable without POST RPC, c
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd
- LEARN: ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com 

## RANKED HYPOTHESES 2026-09-05 01:09:54 UTC
- [55] gamdom80006.com: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [55] gamdom4567.com: Cross-mirror session replay: server-set same-origin cookie is validated host-blind at the shared 4567 origin (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): HUMAN: authenticate on gamdom80006.com, replay exact session cookie header against gamdom80007.com/client-api (and gamdom4567.com/client-api); confirm cross-hos
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/build/client.41b06529227c4b8b6a1d.js` — already fetched; now passively compare `Set-Cookie` headers on `POST /cl
- LEARN: ACCEPTED recon @ gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle and stones the certificate of th
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd
- LEARN: ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com 
- LEARN: ACCEPTED recon @ gamdom80006.com/build/client.41b06529227c4b8b6a1d.js: passive fetch of the app's own request-layer bundle (597 KB) proved auth transport is ser
- LEARN: ACCEPTED recon @ gamdommirrors.com/socket.io: GET-only EIO=4 polling handshake issues SID with websocket upgrade; no admin surface reachable without POST RPC, c

## RANKED HYPOTHESES 2026-09-05 05:52:38 UTC
- [55] gamdom80006.com: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [50] gamdom.com/client-api: Brand-wide origin trust pool: auth cookie validated host-blind at one backend behind every Gamdom domain including flagship (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): HUMAN: authenticate on gamdom80006.com, replay the exact session Cookie header to gamdom.com/client-api AND gamdom80007.com/client-api; log the response vs an i
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/build/client.41b06529227c4b8b6a1d.js` — already fetched; now passively compare `Set-Cookie` headers on `POST /cl
- LEARN: ACCEPTED recon @ gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend si
- LEARN: ACCEPTED recon @ gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory that implied an 8th API-serving mi
- LEARN: ACCEPTED recon @ gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credenti
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd
- LEARN: ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com 
- LEARN: ACCEPTED recon @ gamdom80006.com/build/client.41b06529227c4b8b6a1d.js: passive fetch of the app's own request-layer bundle (597 KB) proved auth transport is ser
- LEARN: ACCEPTED recon @ gamdommirrors.com/socket.io: GET-only EIO=4 polling handshake issues SID with websocket upgrade; no admin surface reachable without POST RPC, c
- LEARN: ACCEPTED recon @ gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle and stones the certificate of th
