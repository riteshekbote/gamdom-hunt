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

## RANKED HYPOTHESES 2026-09-05 10:01:28 UTC
- [55] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/build/client.41b06529227c4b8b6a1d.js` — already fetched; now passively compare `Set-Cookie` headers on `POST /cl
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd
- LEARN: ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com 
- LEARN: ACCEPTED recon @ gamdom80006.com/build/client.41b06529227c4b8b6a1d.js: passive fetch of the app's own request-layer bundle (597 KB) proved auth transport is ser
- LEARN: ACCEPTED recon @ gamdommirrors.com/socket.io: GET-only EIO=4 polling handshake issues SID with websocket upgrade; no admin surface reachable without POST RPC, c
- LEARN: ACCEPTED recon @ gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle and stones the certificate of th
- LEARN: ACCEPTED recon @ gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend si
- LEARN: ACCEPTED recon @ gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory that implied an 8th API-serving mi
- LEARN: ACCEPTED recon @ gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credenti

## RANKED HYPOTHESES 2026-09-05 13:24:15 UTC
- [55] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [25] click.gamdom.com: click.gamdom.com third-party subdomain is a takeover/dangling-CNAME candidate (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: re-check `click.gamdom.com` CNAME chain + root page periodically for a defensive monitor, and re-enumerate `dashboard.gamdom.com` paths/Host variants (re
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/build/client.41b06529227c4b8b6a1d.js` — already fetched; now passively compare `Set-Cookie` headers on `POST /cl
- LEARN: ACCEPTED recon @ click.gamdom.com: passive DNS + GET resolved in-scope subdomain to third-party SymplifyMail email provider (eu-iv-1.symplifymail.com 192.165.55
- LEARN: ACCEPTED recon @ dashboard.gamdom.com: passive GET/HEAD shows genuine scoped admin hostname on flagship Fastly Pool A, fully 403-locked at edge (Varnish Error 5
- LEARN: ACCEPTED recon @ help.gamdom.com: passive GET shows Intercom-hosted help center (x-intercom-version, /en/ 302) — standard third-party helpdesk SaaS, benign surf
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd
- LEARN: ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com 
- LEARN: ACCEPTED recon @ gamdom80006.com/build/client.41b06529227c4b8b6a1d.js: passive fetch of the app's own request-layer bundle (597 KB) proved auth transport is ser
- LEARN: ACCEPTED recon @ gamdommirrors.com/socket.io: GET-only EIO=4 polling handshake issues SID with websocket upgrade; no admin surface reachable without POST RPC, c
- LEARN: ACCEPTED recon @ gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle and stones the certificate of th
- LEARN: ACCEPTED recon @ gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend si
- LEARN: ACCEPTED recon @ gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory that implied an 8th API-serving mi
- LEARN: ACCEPTED recon @ gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credenti

## RANKED HYPOTHESES 2026-09-05 16:21:43 UTC
- [55] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [50] gamdom.com/client-api: Brand-wide origin trust pool: auth cookie validated host-blind at one backend behind every Gamdom domain including flagship (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): HUMAN: authenticate on gamdom80006.com, replay exact session cookie header against gamdom80007.com/client-api (and gamdom4567.com/client-api); confirm cross-hos
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/build/client.41b06529227c4b8b6a1d.js` — already fetched; now passively compare `Set-Cookie` headers on `POST /cl
- LEARN: ACCEPTED recon @ gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle and stones the certificate of th
- LEARN: ACCEPTED recon @ gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend si
- LEARN: ACCEPTED recon @ gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory that implied an 8th API-serving mi
- LEARN: ACCEPTED recon @ gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credenti
- LEARN: ACCEPTED recon @ click.gamdom.com: passive DNS + GET resolved in-scope subdomain to third-party SymplifyMail email provider (eu-iv-1.symplifymail.com 192.165.55
- LEARN: ACCEPTED recon @ dashboard.gamdom.com: passive GET/HEAD shows genuine scoped admin hostname on flagship Fastly Pool A, fully 403-locked at edge (Varnish Error 5
- LEARN: ACCEPTED recon @ help.gamdom.com: passive GET shows Intercom-hosted help center (x-intercom-version, /en/ 302) — standard third-party helpdesk SaaS, benign surf
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd
- LEARN: ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com 
- LEARN: ACCEPTED recon @ gamdom80006.com/build/client.41b06529227c4b8b6a1d.js: passive fetch of the app's own request-layer bundle (597 KB) proved auth transport is ser
- LEARN: ACCEPTED recon @ gamdommirrors.com/socket.io: GET-only EIO=4 polling handshake issues SID with websocket upgrade; no admin surface reachable without POST RPC, c
- LEARN: ACCEPTED recon @ gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle and stones the certificate of th
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
- LEARN: ACCEPTED recon @ gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend si
- LEARN: ACCEPTED recon @ gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory that implied an 8th API-serving mi
- LEARN: ACCEPTED recon @ gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credenti
- LEARN: ACCEPTED recon @ gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend si
- LEARN: ACCEPTED recon @ gamdom80004.com/client-api: 302 → gamdom80007.com/client-api — redirect alias only, not API-serving
- LEARN: ACCEPTED recon @ gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no ACAO — browser cross-origin credentialed replay blocked
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd
- LEARN: ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com 
- LEARN: ACCEPTED recon @ gamdom80006.com/build/client.41b06529227c4b8b6a1d.js: passive fetch of the app's own request-layer bundle (597 KB) proved auth transport is ser
- LEARN: ACCEPTED recon @ gamdommirrors.com/socket.io: GET-only EIO=4 polling handshake issues SID with websocket upgrade; no admin surface reachable without POST RPC, c
- LEARN: ACCEPTED recon @ gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle and stones the certificate of th
- LEARN: ACCEPTED recon @ gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend si
- LEARN: ACCEPTED recon @ gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory that implied an 8th API-serving mi
- LEARN: ACCEPTED recon @ gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credenti
- LEARN: ACCEPTED recon @ click.gamdom.com: passive DNS + GET resolved in-scope subdomain to third-party SymplifyMail email provider (eu-iv-1.symplifymail.com 192.165.55
- LEARN: ACCEPTED recon @ dashboard.gamdom.com: passive GET/HEAD shows genuine scoped admin hostname on flagship Fastly Pool A, fully 403-locked at edge (Varnish Error 5
- LEARN: ACCEPTED recon @ help.gamdom.com: passive GET shows Intercom-hosted help center (x-intercom-version, /en/ 302) — standard third-party helpdesk SaaS, benign surf

## RANKED HYPOTHESES 2026-09-05 18:29:58 UTC
- [55] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [40] gamdom.com/client-api: Session cookie Domain/scope is per-host (host-only), not fleet-wide (from art/lead_bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/build/client.41b06529227c4b8b6a1d.js` — already fetched; now passively compare `Set-Cookie` headers on `POST /cl
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd
- LEARN: ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com 
- LEARN: ACCEPTED recon @ gamdom80006.com/build/client.41b06529227c4b8b6a1d.js: passive fetch of the app's own request-layer bundle (597 KB) proved auth transport is ser
- LEARN: ACCEPTED recon @ gamdommirrors.com/socket.io: GET-only EIO=4 polling handshake issues SID with websocket upgrade; no admin surface reachable without POST RPC, c
- LEARN: ACCEPTED recon @ gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle and stones the certificate of th
- LEARN: ACCEPTED recon @ gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend si
- LEARN: ACCEPTED recon @ gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory that implied an 8th API-serving mi
- LEARN: ACCEPTED recon @ gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credenti
- LEARN: ACCEPTED recon @ click.gamdom.com: passive DNS + GET resolved in-scope subdomain to third-party SymplifyMail email provider (eu-iv-1.symplifymail.com 192.165.55
- LEARN: ACCEPTED recon @ dashboard.gamdom.com: passive GET/HEAD shows genuine scoped admin hostname on flagship Fastly Pool A, fully 403-locked at edge (Varnish Error 5
- LEARN: ACCEPTED recon @ help.gamdom.com: passive GET shows Intercom-hosted help center (x-intercom-version, /en/ 302) — standard third-party helpdesk SaaS, benign surf

## RANKED HYPOTHESES 2026-09-05 20:48:41 UTC
- [60] gamdom.eu/client-api: Cross-host session replay across the now-13-host brand trust pool (Pool A + Pool B + 4 regional TLDs) (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: `curl -sS -m 12 "https://gamdommirrors.com/status/gamdom-domains"` then re-fetch `client.41b06529227c4b8b6a1d.js` diff only if hash changed — single pass
- LEARN: ACCEPTED inventory @ gamdom.eu/gamdom.io/gamdom.vip/gamdom.win: passive mining of the flagship SPA's own link list yielded 4 live official regional TLDs on Pool
- LEARN: ACCEPTED recon @ login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com: all NXDOMAIN (matches api/auth.gamdom.com 000) — the inventory's auth/admi
- LEARN: ACCEPTED recon @ gamdom.eu/gamdom.win: root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — server cookie-issuance polic

## RANKED HYPOTHESES 2026-09-05 22:44:44 UTC
- [55] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [40] gamdom.com/client-api: Session cookie Domain/scope is per-host (host-only), not fleet-wide (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: `curl -sS -m 12 "https://gamdommirrors.com/status/gamdom-domains"` then re-fetch `client.41b06529227c4b8b6a1d.js` diff only if hash changed — single pass
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/build/client.41b06529227c4b8b6a1d.js` — already fetched; now passively compare `Set-Cookie` headers on `POST /cl
- LEARN: ACCEPTED inventory @ gamdom.eu/gamdom.io/gamdom.vip/gamdom.win: passive mining of the flagship SPA's own link list yielded 4 live official regional TLDs on Pool
- LEARN: ACCEPTED recon @ login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com: all NXDOMAIN (matches api/auth.gamdom.com 000) — the inventory's auth/admi
- LEARN: ACCEPTED recon @ gamdom.eu/gamdom.win: root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — server cookie-issuance polic
- LEARN: ACCEPTED inventory @ gamdom90471.com: CNAME→gamdom4567.com (verified origin, same as 80006/80007), Fastly Pool B (151.101.67.72), self-referenced 18× in the off
- LEARN: ACCEPTED recon @ gamdom-girisi.com: official brand SEO/redirect hub (Cloudflare, Turkish, 61 KB) funneling to gamdom90471.com and discord/telegram; linked from 
- LEARN: ACCEPTED recon @ gamdomgiris.link: third-party Cloudflare landing alongside the SEO hub; no Fastly origin /client-api signature → NOT in-scope brand origin (wat
- LEARN: ACCEPTED recon @ gamdommirrors.com/status/gamdom-domains: official Uptime status page lists exactly 7 monitors = com/eu/io/vip/win/80006/80007 → independently r
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80007.com: verified as 7th in-scope mirror (same Fastly origin + POST-only /client-api + listed on the official "Gamdom Official Doma
- LEARN: ACCEPTED recon @ gamdommirrors.com: self-hosted Uptime Kuma status page (not UptimeRobot) publicly publishes full monitor config + 24h heartbeats for all 7 Gamd
- LEARN: ACCEPTED origin-boundary @ gamdom4567.com: Host-header injection blocked by Fastly TLS cert validation (421), but header fingerprinting confirms gamdom4567.com 
- LEARN: ACCEPTED recon @ gamdom80006.com/build/client.41b06529227c4b8b6a1d.js: passive fetch of the app's own request-layer bundle (597 KB) proved auth transport is ser
- LEARN: ACCEPTED recon @ gamdommirrors.com/socket.io: GET-only EIO=4 polling handshake issues SID with websocket upgrade; no admin surface reachable without POST RPC, c
- LEARN: ACCEPTED recon @ gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle and stones the certificate of th
- LEARN: ACCEPTED recon @ gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend si
- LEARN: ACCEPTED recon @ gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory that implied an 8th API-serving mi
- LEARN: ACCEPTED recon @ gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credenti
- LEARN: ACCEPTED recon @ click.gamdom.com: passive DNS + GET resolved in-scope subdomain to third-party SymplifyMail email provider (eu-iv-1.symplifymail.com 192.165.55
- LEARN: ACCEPTED recon @ dashboard.gamdom.com: passive GET/HEAD shows genuine scoped admin hostname on flagship Fastly Pool A, fully 403-locked at edge (Varnish Error 5
- LEARN: ACCEPTED recon @ help.gamdom.com: passive GET shows Intercom-hosted help center (x-intercom-version, /en/ 302) — standard third-party helpdesk SaaS, benign surf

## RANKED HYPOTHESES 2026-09-06 00:18:55 UTC
- [60] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [60] gamdom.com/client-api: Cross-host session replay across the 14-host brand trust pool — single shared nginx/Starlette origin proven fleet-wide via /health (from art/lead_bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/build/client.41b06529227c4b8b6a1d.js` — already fetched; now passively compare `Set-Cookie` headers on `POST /cl
- LEARN: ACCEPTED inventory @ gamdom90471.com: CNAME→gamdom4567.com (verified origin, same as 80006/80007), Fastly Pool B (151.101.67.72), self-referenced 18× in the off
- LEARN: ACCEPTED recon @ gamdom-girisi.com: official brand SEO/redirect hub (Cloudflare, Turkish, 61 KB) funneling to gamdom90471.com and discord/telegram; linked from 
- LEARN: ACCEPTED recon @ gamdomgiris.link: third-party Cloudflare landing alongside the SEO hub; no Fastly origin /client-api signature → NOT in-scope brand origin (wat
- LEARN: ACCEPTED inventory @ gamdom.eu/gamdom.io/gamdom.vip/gamdom.win: passive mining of the flagship SPA's own link list yielded 4 live official regional TLDs on Pool
- LEARN: ACCEPTED recon @ login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com: all NXDOMAIN (matches api/auth.gamdom.com 000) — the inventory's auth/admi
- LEARN: ACCEPTED recon @ gamdom.eu/gamdom.win: root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — server cookie-issuance polic
- LEARN: ACCEPTED recon @ gamdommirrors.com/status/gamdom-domains: official Uptime status page lists exactly 7 monitors = com/eu/io/vip/win/80006/80007 → independently r
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-06 04:50:01 UTC
- [60] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [60] gamdom.com/client-api: Cross-host session replay across the 14-host brand trust pool — single shared nginx/Starlette origin proven fleet-wide (/health 200 byte-identical) (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: rotation watch (single read-only pass): `curl -sS -m 12 "https://gamdom-girisi.com/"` and grep `gamdom[0-9]+\.com`; `curl -sS -m 12 "https://gamdommirror
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/build/client.41b06529227c4b8b6a1d.js` — already fetched; now passively compare `Set-Cookie` headers on `POST /cl
- LEARN: ACCEPTED inventory @ gamdom90471.com: CNAME→gamdom4567.com (verified origin, same as 80006/80007), Fastly Pool B (151.101.67.72), self-referenced 18× in the off
- LEARN: ACCEPTED recon @ gamdom-girisi.com: official brand SEO/redirect hub (Cloudflare, Turkish, 61 KB) funneling to gamdom90471.com and discord/telegram; linked from 
- LEARN: ACCEPTED recon @ gamdomgiris.link: third-party Cloudflare landing alongside the SEO hub; no Fastly origin /client-api signature → NOT in-scope brand origin (wat
- LEARN: ACCEPTED inventory @ gamdom.eu/gamdom.io/gamdom.vip/gamdom.win: passive mining of the flagship SPA's own link list yielded 4 live official regional TLDs on Pool
- LEARN: ACCEPTED recon @ login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com: all NXDOMAIN (matches api/auth.gamdom.com 000) — the inventory's auth/admi
- LEARN: ACCEPTED recon @ gamdom.eu/gamdom.win: root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — server cookie-issuance polic
- LEARN: ACCEPTED recon @ gamdommirrors.com/status/gamdom-domains: official Uptime status page lists exactly 7 monitors = com/eu/io/vip/win/80006/80007 → independently r
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-06 09:14:53 UTC
- [62] gamdom.com/client-api: Cross-host session replay across the 14-host brand trust pool — single shared nginx/Starlette origin now proven fleet-wide via byte-identical /health ETag (from art/lead_bigpickle.txt)
- [60] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: passive adjacent-numbered mirror inventory (read-only DNS, no HTTP): `dig +short gamdom80005.com; dig +short gamdom80008.com; dig +short gamdom80003.com;
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/build/client.41b06529227c4b8b6a1d.js` — already fetched; now passively compare `Set-Cookie` headers on `POST /cl
- LEARN: ACCEPTED inventory @ gamdom90471.com: CNAME→gamdom4567.com (verified origin, same as 80006/80007), Fastly Pool B (151.101.67.72), self-referenced 18× in the off
- LEARN: ACCEPTED recon @ gamdom-girisi.com: official brand SEO/redirect hub (Cloudflare, Turkish, 61 KB) funneling to gamdom90471.com and discord/telegram; linked from 
- LEARN: ACCEPTED recon @ gamdomgiris.link: third-party Cloudflare landing alongside the SEO hub; no Fastly origin /client-api signature → NOT in-scope brand origin (wat
- LEARN: ACCEPTED inventory @ gamdom.eu/gamdom.io/gamdom.vip/gamdom.win: passive mining of the flagship SPA's own link list yielded 4 live official regional TLDs on Pool
- LEARN: ACCEPTED recon @ login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com: all NXDOMAIN (matches api/auth.gamdom.com 000) — the inventory's auth/admi
- LEARN: ACCEPTED recon @ gamdom.eu/gamdom.win: root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — server cookie-issuance polic
- LEARN: ACCEPTED recon @ gamdommirrors.com/status/gamdom-domains: official Uptime status page lists exactly 7 monitors = com/eu/io/vip/win/80006/80007 → independently r
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-06 13:03:53 UTC
- [62] gamdom.com/client-api: Cross-host session replay across the 16-host brand trust pool — single shared nginx/Starlette origin proven fleet-wide via byte-identical /health ETag + /client-api across 9 live mirrors (from art/lead_bigpickle.txt)
- [60] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: passive fleet expansion scan (read-only DNS): `dig +short gamdom80009.com; dig +short gamdom80010.com; dig +short gamdom80011.com; dig +short gamdom90473
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response heade
- LEARN: ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8
- LEARN: ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool;
- LEARN: REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure.
- LEARN: REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure.
- LEARN: ACCEPTED inventory @ gamdom90471.com: CNAME→gamdom4567.com (verified origin, same as 80006/80007), Fastly Pool B (151.101.67.72), self-referenced 18× in the off
- LEARN: ACCEPTED recon @ gamdom-girisi.com: official brand SEO/redirect hub (Cloudflare, Turkish, 61 KB) funneling to gamdom90471.com and discord/telegram; linked from 
- LEARN: ACCEPTED recon @ gamdomgiris.link: third-party Cloudflare landing alongside the SEO hub; no Fastly origin /client-api signature → NOT in-scope brand origin (wat
- LEARN: ACCEPTED inventory @ gamdom.eu/gamdom.io/gamdom.vip/gamdom.win: passive mining of the flagship SPA's own link list yielded 4 live official regional TLDs on Pool
- LEARN: ACCEPTED recon @ login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com: all NXDOMAIN (matches api/auth.gamdom.com 000) — the inventory's auth/admi
- LEARN: ACCEPTED recon @ gamdom.eu/gamdom.win: root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — server cookie-issuance polic
- LEARN: ACCEPTED recon @ gamdommirrors.com/status/gamdom-domains: official Uptime status page lists exactly 7 monitors = com/eu/io/vip/win/80006/80007 → independently r
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-06 16:18:18 UTC
- [62] gamdom.com/client-api: Cross-host session replay across the now-17-host brand trust pool — single shared nginx/Starlette origin proven fleet-wide via byte-identical /health ETag + /client-api (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: rotation/cert-propagation watch (single read-only pass): `curl -sSk --resolve {gamdom90471,gamdom90472,gamdom90473}.com:443:151.101.67.72 https://$host/c

## RANKED HYPOTHESES 2026-09-06 18:29:05 UTC
- [62] gamdom.com/client-api: Cross-host session replay across 18-host brand trust pool — single shared nginx/Starlette origin proven fleet-wide via byte-identical /health ETag + /client-api (from art/lead_bigpickle.txt)
- [57] gamdom.com/client-api: Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: passive cert-propagation + new-alias watch (read-only, ≤1rps):
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response heade
- LEARN: ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8
- LEARN: ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool;
- LEARN: REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure
- LEARN: REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-06 20:35:40 UTC
- [62] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [62] gamdom.com/client-api: Cross-host session replay across 19-host gamdom4567 trust pool — single shared nginx/Starlette origin proven fleet-wide via byte-identical /health ETag + /client-api (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: passive teamgamdom + alias watch (read-only ≤1rps): `curl -sS -o /dev/null -w "%{http_code}" https://shreeram-dynamic-test.teamgamdom.com/` + `https://st
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response heade
- LEARN: ACCEPTED inventory @ teamgamdom.com: 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert same CA as mirror fleet) hosting staging.teamgamdom.com (liv
- LEARN: ACCEPTED recon @ gamdom90488.com: public numbered alias CNAME→shreeram-dynamic-test.teamgamdom.com pins a brand mirror onto the internal Basic-auth-gated nginx 
- LEARN: ACCEPTED inventory @ gamdom90480.com/gamdom90482.com: both CNAME→gamdom4567.com, Pool B, client-api 421 — 5th/6th provisioned 9047x-family alias.
- LEARN: ACCEPTED recon @ gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com: staging page assets public-read (200), ListObjects/root denied (403) — closed hosting,
- LEARN: REJECTED out-of-scope @ gamdom90474/90476/90477/90478/90479/90481/90483/90484/90485/90486.com: resolve to 192.64.119.x/162.255.119.x (non-Fastly) — not brand in
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8
- LEARN: ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool;
- LEARN: REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure
- LEARN: REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure

## RANKED HYPOTHESES 2026-09-06 22:23:37 UTC
- [62] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [62] gamdom.com/client-api: Cross-host session replay across gamdom4567 trust pool — single shared nginx/Starlette origin fleet-wide (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: alias-deployment watch (read-only ≤1rps): `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (TLS-NOMATCH→200/401 = alias cert live = int
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response heade
- LEARN: ACCEPTED recon @ api/admin/auth/app/dev/test/sso/login/mail/cdn/core/support.teamgamdom.com: Fastly Pool A → Go `404 page not found` (19B) + `x-dbg-vcl2: bong_k
- LEARN: ACCEPTED recon @ teamgamdom.com wildcard: Fastly VCL/backend `bong_ke` fingerprint; third origin realm (Go) distinct from Starlette pool + nginx Basic.
- LEARN: ACCEPTED watch @ gamdom90480/90482/90488 + 9047x: all still 421/TLS-NOMATCH — no alias went live.
- LEARN: ACCEPTED watch @ staging.teamgamdom.com: 503 across all probed paths — maintenance gate fixture-wide.
- LEARN: ACCEPTED watch @ gamdommirrors.com status page: still exactly 7 monitors.
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8
- LEARN: ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool;
- LEARN: REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure
- LEARN: REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure
- LEARN: ACCEPTED inventory @ teamgamdom.com: 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert same CA as mirror fleet) hosting staging.teamgamdom.com (liv
- LEARN: ACCEPTED recon @ gamdom90488.com: public numbered alias CNAME→shreeram-dynamic-test.teamgamdom.com pins a brand mirror onto the internal Basic-auth-gated nginx 
- LEARN: ACCEPTED inventory @ gamdom90480.com/gamdom90482.com: both CNAME→gamdom4567.com, Pool B, client-api 421 — 5th/6th provisioned 9047x-family alias
- LEARN: ACCEPTED recon @ gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com: staging page assets public-read (200), ListObjects/root denied (403) — closed hosting,
- LEARN: REJECTED out-of-scope @ gamdom90474/90476/90477/90478/90479/90481/90483/90484/90485/90486.com: resolve to 192.64.119.x/162.255.119.x (non-Fastly) — not brand in

## RANKED HYPOTHESES 2026-09-07 00:08:08 UTC
- [62] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [62] gamdom.com/client-api: Cross-host session replay across gamdom4567 trust pool — single shared nginx/Starlette origin fleet-wide (from art/lead_bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response heade
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8
- LEARN: ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool;
- LEARN: REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure
- LEARN: REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure
- LEARN: ACCEPTED inventory @ teamgamdom.com: 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert same CA as mirror fleet) hosting staging.teamgamdom.com (liv
- LEARN: ACCEPTED recon @ gamdom90488.com: public numbered alias CNAME→shreeram-dynamic-test.teamgamdom.com pins a brand mirror onto the internal Basic-auth-gated nginx 
- LEARN: ACCEPTED inventory @ gamdom90480.com/gamdom90482.com: both CNAME→gamdom4567.com, Pool B, client-api 421 — 5th/6th provisioned 9047x-family alias
- LEARN: ACCEPTED recon @ gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com: staging page assets public-read (200), ListObjects/root denied (403) — closed hosting,
- LEARN: REJECTED out-of-scope @ gamdom90474/90476/90477/90478/90479/90481/90483/90484/90485/90486.com: resolve to 192.64.119.x/162.255.119.x (non-Fastly) — not brand in

## RANKED HYPOTHESES 2026-09-07 04:55:08 UTC
- [62] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [52] gamdom90488.com: Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on 2nd brand TLD teamgamdom.com (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: passive deployment-watch (read-only ≤1rps): `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (TLS-NOMATCH→200/401 = alias cert deployed
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response heade
- LEARN: ACCEPTED recon @ shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate fixture-wide — /.git/config /env /server-status /status /_metrics /actuator all
- LEARN: ACCEPTED watch @ gamdom-girisi.com: SEO hub unchanged, still 17× gamdom90471.com only, no new alias advertised; provisioned fleet (90480/90482/90488/90473/90475
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8
- LEARN: ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool;
- LEARN: REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure
- LEARN: REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure
- LEARN: ACCEPTED inventory @ teamgamdom.com: 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert same CA as mirror fleet) hosting staging.teamgamdom.com (liv
- LEARN: ACCEPTED recon @ gamdom90488.com: public numbered alias CNAME→shreeram-dynamic-test.teamgamdom.com pins a brand mirror onto the internal Basic-auth-gated nginx 
- LEARN: ACCEPTED inventory @ gamdom90480.com/gamdom90482.com: both CNAME→gamdom4567.com, Pool B, client-api 421 — 5th/6th provisioned 9047x-family alias
- LEARN: ACCEPTED recon @ gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com: staging page assets public-read (200), ListObjects/root denied (403) — closed hosting,
- LEARN: REJECTED out-of-scope @ gamdom90474/90476/90477/90478/90479/90481/90483/90484/90485/90486.com: resolve to 192.64.119.x/162.255.119.x (non-Fastly) — not brand in

## RANKED HYPOTHESES 2026-09-07 09:58:15 UTC
- [62] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [52] gamdom90488.com: Public alias gamdom90488.com wired to Basic-auth-gated internal nginx on 2nd brand TLD teamgamdom.com (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: passive deployment-watch (read-only ≤1rps): `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (TLS-NOMATCH→200/401 = alias cert deployed
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response heade
- LEARN: ACCEPTED watch @ 90472/90488/90473/90475 aliases: all still 421/TLS-NOMATCH — no alias went live; provisioning-in-progress continues.
- LEARN: ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com + teamgamdom Go realm: gate holds 401 fixture-wide (7 paths), api.teamgamdom.com 404 default — no new surf
- LEARN: ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors, SEO hub still 17× 90471 only.
- LEARN: ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable.
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8
- LEARN: ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool;
- LEARN: REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure
- LEARN: REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure
- LEARN: ACCEPTED inventory @ teamgamdom.com: 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert same CA as mirror fleet) hosting staging.teamgamdom.com (liv
- LEARN: ACCEPTED recon @ gamdom90488.com: public numbered alias CNAME→shreeram-dynamic-test.teamgamdom.com pins a brand mirror onto the internal Basic-auth-gated nginx 
- LEARN: ACCEPTED inventory @ gamdom90480.com/gamdom90482.com: both CNAME→gamdom4567.com, Pool B, client-api 421 — 5th/6th provisioned 9047x-family alias
- LEARN: ACCEPTED recon @ gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com: staging page assets public-read (200), ListObjects/root denied (403) — closed hosting,
- LEARN: REJECTED out-of-scope @ gamdom90474/90476/90477/90478/90479/90481/90483/90484/90485/90486.com: resolve to 192.64.119.x/162.255.119.x (non-Fastly) — not brand in
- LEARN: ACCEPTED recon @ shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate fixture-wide — /.git/config /env /server-status /status /_metrics /actuator all
- LEARN: ACCEPTED watch @ gamdom-girisi.com: SEO hub unchanged, still 17× gamdom90471.com only, no new alias advertised; provisioned fleet (90480/90482/90488/90473/90475

## RANKED HYPOTHESES 2026-09-07 15:40:49 UTC
- [62] gamdom80006.com/client-api: Brand-wide origin trust pool: auth cookie validated host-blind at single backend behind every Gamdom domain including flagship (from art/lead_nemotron3.txt)
- [62] gamdom.com/client-api: Cross-host session replay across gamdom4567 trust pool — single shared nginx/Starlette origin fleet-wide (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: passive deployment-watch (read-only ≤1rps): `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (TLS-NOMATCH→200/401 = alias cert deployed
- NEXT(hypotheses-nemotron3.txt): PROBE: GET (read-only) `https://gamdom80006.com/client-api` with `Origin: https://gamdom80007.com` and `Cookie: <captured-session>` — compare 400 response heade
- LEARN: ACCEPTED watch @ gamdom90488.com + shreeram gate: both unchanged — alias cert not deployed, gate holds 401 fixture-wide. Provisioning-in-progress continues for 
- LEARN: ACCEPTED watch @ gamdom-girisi.com: SEO hub unchanged, still 17× 90471 only; no new alias advertised, provisioned fleet (90480/90482/90488/90473/90475) still un
- LEARN: ACCEPTED watch @ Starlette pool: /client-api 400 + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable.
- LEARN: ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors.
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8
- LEARN: ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool;
- LEARN: REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure
- LEARN: REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure
- LEARN: ACCEPTED inventory @ teamgamdom.com: 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert same CA as mirror fleet) hosting staging.teamgamdom.com (liv
- LEARN: ACCEPTED recon @ gamdom90488.com: public numbered alias CNAME→shreeram-dynamic-test.teamgamdom.com pins a brand mirror onto the internal Basic-auth-gated nginx 
- LEARN: ACCEPTED inventory @ gamdom90480.com/gamdom90482.com: both CNAME→gamdom4567.com, Pool B, client-api 421 — 5th/6th provisioned 9047x-family alias
- LEARN: ACCEPTED recon @ gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com: staging page assets public-read (200), ListObjects/root denied (403) — closed hosting,
- LEARN: REJECTED out-of-scope @ gamdom90474/90476/90477/90478/90479/90481/90483/90484/90485/90486.com: resolve to 192.64.119.x/162.255.119.x (non-Fastly) — not brand in
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ gamdom80003.com: CNAME→gamdom4567.com (verified origin), Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8
- LEARN: ACCEPTED inventory @ gamdom90472.com: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, 16th hostname in brand trust pool;
- LEARN: REJECTED out-of-scope @ gamdom80005.com: resolves to 8.8.8.8 (Google DNS IP, not Fastly) — not in-scope brand infrastructure
- LEARN: REJECTED out-of-scope @ gamdom80008.com: resolves to 192.64.119.33 (not Fastly) — not in-scope brand infrastructure
- LEARN: ACCEPTED inventory @ teamgamdom.com: 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert same CA as mirror fleet) hosting staging.teamgamdom.com (liv
- LEARN: ACCEPTED recon @ gamdom90488.com: public numbered alias CNAME→shreeram-dynamic-test.teamgamdom.com pins a brand mirror onto the internal Basic-auth-gated nginx 
- LEARN: ACCEPTED inventory @ gamdom90480.com/gamdom90482.com: both CNAME→gamdom4567.com, Pool B, client-api 421 — 5th/6th provisioned 9047x-family alias
- LEARN: ACCEPTED recon @ gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com: staging page assets public-read (200), ListObjects/root denied (403) — closed hosting,
- LEARN: REJECTED out-of-scope @ gamdom90474/90476/90477/90478/90479/90481/90483/90484/90485/90486.com: resolve to 192.64.119.x/162.255.119.x (non-Fastly) — not brand in
- LEARN: ACCEPTED recon @ shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate fixture-wide — /.git/config /env /server-status /status /_metrics /actuator all
- LEARN: ACCEPTED watch @ gamdom-girisi.com: SEO hub unchanged, still 17× gamdom90471.com only, no new alias advertised; provisioned fleet (90480/90482/90488/90473/90475
- LEARN: ACCEPTED recon @ shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate fixture-wide — /.git/config /env /server-status /status /_metrics /actuator all
- LEARN: ACCEPTED watch @ gamdom-girisi.com: SEO hub unchanged, still 17× gamdom90471.com only, no new alias advertised; provisioned fleet (90480/90482/90488/90473/90475
- LEARN: ACCEPTED watch @ 90472/90488/90473/90475 aliases: all still 421/TLS-NOMATCH — no alias went live; provisioning-in-progress continues.
- LEARN: ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com + teamgamdom Go realm: gate holds 401 fixture-wide (7 paths), api.teamgamdom.com 404 default — no new surf
- LEARN: ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors, SEO hub still 17× 90471 only.
- LEARN: ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable.
- LEARN: ACCEPTED watch @ 90472/90488/90473/90475 aliases: all still 421/TLS-NOMATCH — no alias went live; provisioning-in-progress continues
- LEARN: ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com + teamgamdom Go realm: gate holds 401 fixture-wide (7 paths), api.teamgamdom.com 404 default — no new surf
- LEARN: ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors, SEO hub still 17× 90471 only
- LEARN: ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-07 19:30:30 UTC
- [62] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [62] gamdom.com/client-api: Cross-host session replay across gamdom4567 trust pool — single shared nginx/Starlette origin fleet-wide (from art/lead_bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: passive deployment-watch (read-only ≤1 rps): `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (TLS-NOMATCH→200/401 = alias cert deploye
- LEARN: ACCEPTED watch @ 90472/90488/90473/90475 aliases: all still 421/TLS-NOMATCH — no alias went live; provisioning-in-progress continues
- LEARN: ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com + teamgamdom Go realm: gate holds 401 fixture-wide (7 paths), api.teamgamdom.com 404 default — no new surf
- LEARN: ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors, SEO hub still 17× 90471 only
- LEARN: ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-07 22:19:29 UTC
- [62] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [62] gamdom.com/client-api: Cross-host session replay across gamdom4567 trust pool — single shared nginx/Starlette origin fleet-wide (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: passive deployment-watch (read-only ≤1rps): `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (000→200/401 = alias cert deployed), `curl
- NEXT(hypotheses-nemotron3.txt): PROBE: passive deployment-watch (read-only ≤1 rps):
- LEARN: ACCEPTED watch @ gamdom90488.com + shreeram gate: both unchanged — alias cert not deployed (000/421), gate holds 401 fixture-wide; provisioning-in-progress 4th 
- LEARN: ACCEPTED watch @ gamdom-girisi.com: SEO hub unchanged, still 17× 90471 only; provisioned fleet (90472/90473/90475/90480/90482/90488) still unpublished.
- LEARN: ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical Pool A + Pool B — shared or
- LEARN: ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors (com/eu/io/vip/win/80006/80007).
- LEARN: ACCEPTED watch @ 90472/90488/90473/90475 aliases: all still 421/TLS-NOMATCH — no alias went live; provisioning-in-progress continues
- LEARN: ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com + teamgamdom Go realm: gate holds 401 fixture-wide (7 paths), api.teamgamdom.com 404 default — no new surf
- LEARN: ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors, SEO hub still 17× 90471 only
- LEARN: ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-08 00:45:01 UTC
- [62] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: passive auth flow cookie inspection — `curl -sS -I https://gamdom80006.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare 
- LEARN: ACCEPTED watch @ 90472/90488/90473/90475 aliases: all still 421/TLS-NOMATCH — no alias went live; provisioning-in-progress continues
- LEARN: ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com + teamgamdom Go realm: gate holds 401 fixture-wide (7 paths), api.teamgamdom.com 404 default — no new surf
- LEARN: ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors, SEO hub still 17× 90471 only
- LEARN: ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-08 05:22:20 UTC
- [62] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: passive auth flow cookie inspection — `curl -sS -I https://gamdom80006.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare 
- NEXT(hypotheses-nemotron3.txt): PROBE: passive auth flow cookie inspection — `curl -sS -I https://gamdom80006.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare 
- LEARN: ACCEPTED watch @ 90472/90488/90473/90475 aliases: all still 421/TLS-NOMATCH — no alias went live; provisioning-in-progress continues
- LEARN: ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com + teamgamdom Go realm: gate holds 401 fixture-wide (7 paths), api.teamgamdom.com 404 default — no new surf
- LEARN: ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors, SEO hub still 17× 90471 only
- LEARN: ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED watch @ gamdom90488.com + shreeram gate: both unchanged — alias cert not deployed (000/421), gate holds 401 fixture-wide; provisioning-in-progress 4th 
- LEARN: ACCEPTED watch @ gamdom-girisi.com: SEO hub unchanged, still 17× 90471 only; provisioned fleet (90472/90473/90475/90480/90482/90488) still unpublished.
- LEARN: ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical Pool A + Pool B — shared or
- LEARN: ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors (com/eu/io/vip/win/80006/80007).
- LEARN: ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identi
- LEARN: ACCEPTED recon @ fatbets.com + gamdom.one: `/auth/login` + `/graphql` → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not ob
- LEARN: ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; `trMirrorDomain=gamdom80004.com`.
- LEARN: ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress.
- LEARN: ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable.
- LEARN: ACCEPTED watch @ 90472/90488/90473/90475 aliases: all still 421/TLS-NOMATCH — no alias went live; provisioning-in-progress continues
- LEARN: ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com + teamgamdom Go realm: gate holds 401 fixture-wide (7 paths), api.teamgamdom.com 404 default — no new surf
- LEARN: ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors, SEO hub still 17× 90471 only
- LEARN: ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-08 09:58:50 UTC
- [62] gamdom80006.com/client-api: Cross-mirror auth cookie replay via shared /client-api origin yields ATO (from art/lead_nemotron3.txt)
- [50] gamdom90488.com: Live-alias deploy on 90488 (provisioning-in-progress gate-scope risk) (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: passive deploy+parity watch — `curl -sS -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (000→200/401), `curl -sS -o /dev/null -w "%{http_code}" 
- NEXT(hypotheses-nemotron3.txt): PROBE: passive auth flow cookie inspection — `curl -sS -I https://gamdom80006.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare 
- LEARN: ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED watch @ 90472/90488/90473/90475 aliases: all still 421/TLS-NOMATCH — no alias went live; provisioning-in-progress continues
- LEARN: ACCEPTED watch @ shreeram-dynamic-test.teamgamdom.com + teamgamdom Go realm: gate holds 401 fixture-wide (7 paths), api.teamgamdom.com 404 default — no new surf
- LEARN: ACCEPTED watch @ gamdommirrors status page: still exactly 7 monitors, SEO hub still 17× 90471 only
- LEARN: ACCEPTED watch @ Starlette pool: /client-api 400 (md5 7e3a161d) + weak-ETag byte-identical across Pool A + Pool B — shared origin confirmed stable
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing
- LEARN: ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identi
- LEARN: ACCEPTED recon @ fatbets.com + gamdom.one: `/auth/login` + `/graphql` → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not ob
- LEARN: ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; `trMirrorDomain=gamdom80004.com`
- LEARN: ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress
- LEARN: ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-08 14:26:58 UTC
- [50] gamdom90488.com: Live alias deployed on 90488 (provisioning gate-scope risk) (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: confirm 80001/80002 deliverability edge parity — `for e in 151.101.67.72 151.101.3.72 151.101.131.72 151.101.195.72; do curl -sSk -o /dev/null -w "%{http
- LEARN: ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api
- LEARN: ACCEPTED inventory @ gamdom80007.com: demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbe
- LEARN: ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live ho

## RANKED HYPOTHESES 2026-09-08 18:11:33 UTC
- [66] gamdom.com/client-api: Cross-brand cookie replay on shared identity/wallet origin (from art/lead_bigpickle.txt)
- [65] fatbets.com/client-api: Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: deploy+parity watch on pending aliases + monitor-count confirm — `curl -sSk -o /dev/null -w "%{http_code}" https://gamdom90488.com/` (000/421→200/401 = l
- NEXT(hypotheses-nemotron3.txt): PROBE: passive auth flow cookie inspection — `curl -sS -I https://fatbets.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Doma
- LEARN: ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api
- LEARN: ACCEPTED inventory @ gamdom80007.com: demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbe
- LEARN: ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live ho
- LEARN: ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identi
- LEARN: ACCEPTED recon @ fatbets.com + gamdom.one: `/auth/login` + `/graphql` → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not ob
- LEARN: ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; `trMirrorDomain=gamdom80004.com`
- LEARN: ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress
- LEARN: ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-08 20:54:36 UTC
- [65] fatbets.com/client-api: Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one (from art/lead_nemotron3.txt)
- [52] gamdom90488.com: Provisioned alias cert lands with edge/backend gate-scope regression (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: redeploy-parity watch across all 9 pending aliases — `for a in 90471 90472 90473 90475 90480 90482 90488 80001 80002; do curl -sSk -o /dev/null -w "$a:%{
- NEXT(hypotheses-nemotron3.txt): PROBE: passive auth flow cookie inspection — `curl -sS -I https://fatbets.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Doma
- LEARN: ACCEPTED watch @ 80001/80002 + 7 pending 9047x aliases: all 4 Pool B edges still 421/TLS-NOMATCH; no new alias went live; 80007 still 302→80008, 80008 live.
- LEARN: ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api
- LEARN: ACCEPTED inventory @ gamdom80007.com: demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbe
- LEARN: ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live ho
- LEARN: ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identi
- LEARN: ACCEPTED recon @ fatbets.com + gamdom.one: `/auth/login` + `/graphql` → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not ob
- LEARN: ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; `trMirrorDomain=gamdom80004.com`
- LEARN: ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress
- LEARN: ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-08 23:13:03 UTC
- [66] gamdom.com/client-api: Cross-brand cookie replay on shared identity/wallet origin (from art/lead_bigpickle.txt)
- [66] fatbets.com/client-api: Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: deploy-parity watch (cycle 8) — `for a in 90471 90472 90473 90475 90480 90482 90488 80001 80002; do curl -sSk -o /dev/null -w "$a:%{http_code} " https://
- NEXT(hypotheses-nemotron3.txt): PROBE: passive auth flow cookie inspection — `curl -sS -I https://fatbets.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Doma
- LEARN: ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api
- LEARN: ACCEPTED inventory @ gamdom80007.com: demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbe
- LEARN: ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live ho
- LEARN: ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identi
- LEARN: ACCEPTED recon @ fatbets.com + gamdom.one: `/auth/login` + `/graphql` → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not ob
- LEARN: ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; `trMirrorDomain=gamdom80004.com`
- LEARN: ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress
- LEARN: ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-09 01:34:51 UTC
- [68] fatbets.com/client-api: Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one (from art/lead_nemotron3.txt)
- [66] gamdom.com/client-api: Cross-brand cookie replay on shared identity/wallet origin (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: deploy-parity watch (cycle 8) — `for a in 90471 90472 90473 90475 90480 90482 90488 80001 80002; do curl -sSk -o /dev/null -w "$a:%{http_code} " https://
- NEXT(hypotheses-nemotron3.txt): PROBE: passive auth flow cookie inspection — `curl -sS -I https://fatbets.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Doma
- LEARN: ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api
- LEARN: ACCEPTED inventory @ gamdom80007.com: demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbe
- LEARN: ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live ho
- LEARN: ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identi
- LEARN: ACCEPTED recon @ fatbets.com + gamdom.one: `/auth/login` + `/graphql` → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not ob
- LEARN: ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; `trMirrorDomain=gamdom80004.com`
- LEARN: ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress
- LEARN: ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-09 06:09:00 UTC
- [68] gamdom.com/client-api: Cross-brand cookie replay on shared identity/wallet origin (from art/lead_bigpickle.txt)
- [68] fatbets.com/client-api: Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: deploy-parity watch (cycle 8) — `for a in 90471 90472 90473 90475 90480 90482 90488 80001 80002; do curl -sSk -o /dev/null -w "$a:%{http_code} " https://
- NEXT(hypotheses-nemotron3.txt): PROBE: passive auth flow cookie inspection — `curl -sS -I https://fatbets.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Doma
- LEARN: ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api
- LEARN: ACCEPTED inventory @ gamdom80007.com: demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbe
- LEARN: ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live ho
- LEARN: ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identi
- LEARN: ACCEPTED recon @ fatbets.com + gamdom.one: /auth/login + /graphql → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observ
- LEARN: ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; trMirrorDomain=gamdom80004.com.
- LEARN: ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress.
- LEARN: ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable.
- LEARN: ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api
- LEARN: ACCEPTED inventory @ gamdom80007.com: demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbe
- LEARN: ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live ho
- LEARN: ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identi
- LEARN: ACCEPTED recon @ fatbets.com + gamdom.one: `/auth/login` + `/graphql` → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not ob
- LEARN: ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; `trMirrorDomain=gamdom80004.com`
- LEARN: ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress
- LEARN: ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-09 11:51:06 UTC
- [68] gamdom.com/client-api: Cross-brand cookie replay on shared identity/wallet origin (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: deploy-parity watch (cycle 8) — passive HTTP probe across all 9 pending aliases + status page reconfirmation:
- LEARN: ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live → 10th mirror / 20th h
- LEARN: ACCEPTED inventory @ gamdom80007.com: demoted to 302→gamdom80008.com redirect alias — confirms brand retires numbered aliases by redirect.
- LEARN: ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live.
- LEARN: ACCEPTED inventory @ fatbets.com + gamdom.one: shared identity/wallet origin serves 2 brands / 20+ hostnames.
- LEARN: ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: 5th cycle, all still 421/TLS-NOMATCH.
- LEARN: ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: stable.
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live proxy prohibited.
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public status page is legitimate passive recon.
- LEARN: REJECTED out-of-scope @ trgamdom.com: parked on hugedomains.com.

## RANKED HYPOTHESES 2026-09-09 15:26:45 UTC
- [70] gamdom.com/client-api: Cross-brand cookie replay on shared identity/wallet origin (from art/lead_bigpickle.txt)
- [68] fatbets.com/client-api: Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: deploy-parity watch (cycle 9) — passive HTTP probe across all 9 pending aliases + status page reconfirmation:
- NEXT(hypotheses-nemotron3.txt): PROBE: passive auth flow cookie inspection — `curl -sS -I https://fatbets.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Doma
- LEARN: ACCEPTED inventory @ gamdom80009.com: newly live (11th mirror) replaces gamdom80006.com on status page monitor id:223 — second rotation event in 24h; /client-ap
- LEARN: ACCEPTED inventory @ gamdom80006.com: demoted from live mirror to 302→https://gamdom80009.com/ redirect alias (Varnish, no-store) — confirms brand retires numbe
- LEARN: ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: still CNAME→gamdom4567.com Pool B DNS, all edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live; 6th
- LEARN: ACCEPTED inventory @ fatbets.com + gamdom.one: shared identity/wallet origin serves 2 brands / 21+ hostnames (unchanged).
- LEARN: ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: 6th cycle, all still 421/TLS-NOMATCH.
- LEARN: ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag stable; status page now 7 monitors (com/eu/io/vip/win/80008/80009)
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live proxy prohibited.
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public status page is legitimate passive recon.
- LEARN: REJECTED out-of-scope @ trgamdom.com: parked on hugedomains.com.
- LEARN: ACCEPTED inventory @ gamdom80008.com: previously REJECTED out-of-scope (192.64.119.33) now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api
- LEARN: ACCEPTED inventory @ gamdom80007.com: demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbe
- LEARN: ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live ho
- LEARN: ACCEPTED inventory @ fatbets.com + gamdom.one: mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identi
- LEARN: ACCEPTED recon @ fatbets.com + gamdom.one: `/auth/login` + `/graphql` → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not ob
- LEARN: ACCEPTED recon @ flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; `trMirrorDomain=gamdom80004.com`
- LEARN: ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — 5th cycle, provisioning-in-progress
- LEARN: ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag + 7 monitors + 17× 90471 all stable
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-09 18:45:14 UTC
- [70] gamdom.com/client-api: Cross-brand cookie replay on shared identity/wallet origin (from art/lead_bigpickle.txt)
- [70] fatbets.com/client-api: Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: deploy-parity watch (cycle 9) — passive HTTP probe across all 9 pending aliases + status page reconfirmation:
- NEXT(hypotheses-nemotron3.txt): PROBE: passive auth flow cookie inspection — `curl -sS -I https://fatbets.com/auth/login` (or actual login endpoint) to capture Set-Cookie headers; compare Doma
- LEARN: ACCEPTED inventory @ gamdom80009.com: newly live (11th mirror) replaces gamdom80006.com on status page monitor id:223 — second rotation event in 24h; /client-ap
- LEARN: ACCEPTED inventory @ gamdom80006.com: demoted from live mirror to 302→https://gamdom80009.com/ redirect alias (Varnish, no-store) — confirms brand retires numbe
- LEARN: ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: still CNAME→gamdom4567.com Pool B DNS, all edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live; 6th
- LEARN: ACCEPTED inventory @ fatbets.com + gamdom.one: shared identity/wallet origin serves 2 brands / 21+ hostnames (unchanged).
- LEARN: ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: 6th cycle, all still 421/TLS-NOMATCH.
- LEARN: ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag stable; status page now 7 monitors (com/eu/io/vip/win/80008/80009)
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live proxy prohibited.
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public status page is legitimate passive recon.
- LEARN: REJECTED out-of-scope @ trgamdom.com: parked on hugedomains.com.
- LEARN: ACCEPTED inventory @ gamdom80009.com: newly live (11th mirror) replaces gamdom80006.com on status page monitor id:223 — second rotation event in 24h; /client-ap
- LEARN: ACCEPTED inventory @ gamdom80006.com: demoted from live mirror to 302→https://gamdom80009.com/ redirect alias (Varnish, no-store) — confirms brand retires numbe
- LEARN: ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: still CNAME→gamdom4567.com Pool B DNS, all edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live; 6th
- LEARN: ACCEPTED inventory @ fatbets.com + gamdom.one: shared identity/wallet origin serves 2 brands / 21+ hostnames (unchanged)
- LEARN: ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: 6th cycle, all still 421/TLS-NOMATCH
- LEARN: ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag stable; status page now 7 monitors (com/eu/io/vip/win/80008/80009)
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-09 21:40:04 UTC
- [72] fatbets.com/client-api: Cross-brand auth cookie replay via shared /client-api origin yields ATO across Gamdom + fatbets + gamdom.one (from art/lead_nemotron3.txt)
- [45] kargo.teamgamdom.com: Internal GitOps deploy-control UI publicly reachable on scoped brand domain (from art/lead_bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: passive auth flow cookie inspection — `curl -sS -I https://gamdom80009.com/auth/login` (or actual login endpoint if different) to capture Set-Cookie head
- LEARN: ACCEPTED inventory @ gamdom80009.com: newly live (11th mirror) replaces gamdom80006.com on status page monitor id:223 — second rotation event in 24h; /client-ap
- LEARN: ACCEPTED inventory @ gamdom80006.com: demoted from live mirror to 302→https://gamdom80009.com/ redirect alias (Varnish, no-store) — confirms brand retires numbe
- LEARN: ACCEPTED inventory @ gamdom80001.com/gamdom80002.com: still CNAME→gamdom4567.com Pool B DNS, all edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live; 6th
- LEARN: ACCEPTED inventory @ fatbets.com + gamdom.one: shared identity/wallet origin serves 2 brands / 21+ hostnames (unchanged)
- LEARN: ACCEPTED watch @ 90472/90473/90475/90480/90482/90488: 6th cycle, all still 421/TLS-NOMATCH
- LEARN: ACCEPTED watch @ Starlette pool + mirrors + SEO hub + status page: bundle hash + /health ETag stable; status page now 7 monitors (com/eu/io/vip/win/80008/80009)
- LEARN: REJECTED auth-bypass @ gamdom.com/client-api: blind POST to live identity/wallet proxy prohibited (no-auth-bypass/mutate-against-live-data); require valid sessi
- LEARN: ACCEPTED inventory-leak @ gamdommirrors.com: public Uptime Kuma status page of in-scope org service is legitimate passive recon resolving true operating domains
- LEARN: REJECTED out-of-scope @ trgamdom.com: domain parked for sale on hugedomains.com not operated by Gamdom; only reportable as brand-jacking/phishing

## RANKED HYPOTHESES 2026-09-09 23:34:14 UTC
- [0] ?: Cross-brand cookie replay via shared /client-api identity/wallet origin (from art/lead_bigpickle.txt)
