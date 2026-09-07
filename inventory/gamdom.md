# Gamdom inventory (discovery seed 2026-09-02)
# NOTE: hosts below are discovery candidates from passive DNS/CT; confirm in-scope vs program scope before active testing.
82195821-ba02-4276-96f2-8e0d05d74bdf.ggamdom.com
account.gamdom.com
admin.gamdom.com
api.gamdom.com
app.gamdom.com
auth.gamdom.com
azure.gamdom.com
billing.gamdom.com
careers.gamdom.com
click.gamdom.com
dashboard.gamdom.com
dev.gamdom.com
gamdom.com
gcp.gamdom.com
go.gamdom.com
help.gamdom.com
img.gamdom.com
inteligance.gamdom.com
login.gamdom.com
m.gamdom.com
mail.gamdom.com
my.gamdom.com
portal.gamdom.com
secure.gamdom.com
sso.gamdom.com
staging.gamdom.com
support.gamdom.com
t.gamdom.com
test.gamdom.com
u003ewww.gamdom.com
unsubscribe.gamdom.com
web.gamdom.com
www.gamdom.com

## PASSIVE RECON 2026-09-02 (read-only, non-intrusive)

> Recon observations only. These are NOT confirmed vulnerabilities; ownership/in-scope of each host must be confirmed against the program scope before any active testing. Hosts resolve + serve HTTP — investigation requires scoped authorization.

**Probed:** 33 hosts | **Live HTTP:** 0

| Host | Status | Server/Tech |
|---|---|---|

## 2026-09-02 21:57:15 UTC

## 2026-09-02 23:52:37 UTC

## 2026-09-03 02:53:37 UTC

## 2026-09-03 07:46:26 UTC

## 2026-09-03 12:35:37 UTC

## 2026-09-03 17:00:20 UTC

## 2026-09-03 19:41:38 UTC
- NEW gamdom80007.com (7th mirror): same Fastly origin, byte-identical app + POST-only /client-api (verified 200 root / 400 GET /client-api / 400 POST /client-api).
- NEW gamdom4567.com identified as the real CNAME origin behind both gamdom8000x mirrors (root 404, /client-api 400) — exposes the true upstream behind the clone aliases.
- CHANGED gamdommirrors.com is Uptime **Kuma** (self-hosted, behind Fastly/Varnish), NOT UptimeRobot; status page slug `gamdom-domains` publishes 7 monitors + 24h heartbeats; incidents REST route in Kuma is han

## 2026-09-03 21:58:07 UTC
- NEW gamdommirrors.com is confirmed self-hosted Uptime Kuma (not UptimeRobot) publishing 7 monitors + 24h heartbeats for all Gamdom domains; `/api/badge/*` and `/socket.io/` endpoints exist but return SPA 
- NEW Two distinct Fastly origin pools confirmed: Pool A (151.101.x.52) for gamdom.com/eu/io/vip/win; Pool B (151.101.x.72) for gamdom80006.com/gamdom80007.com/gamdom4567.com — both serve byte-identical app
- NEW gamdom4567.com is the CNAME origin root behind 8000x mirrors (root 404, `/client-api` 400), exposing true upstream
- CHANGED api.gamdom.com and auth.gamdom.com return 000 (connection failed) — not publicly reachable on standard ports
- CHANGED Uptime Kuma API routes (`/api/status-page/*`, `/api/monitor`, `/api/heartbeat/*`, `/api/badge/*`, `/api/doc`, `/api/push/*`) all serve SPA HTML; no unauthenticated JSON API surface confirmed

## 2026-09-03 23:50:18 UTC

## 2026-09-04 02:53:41 UTC

## 2026-09-04 07:47:47 UTC

## 2026-09-04 12:45:23 UTC
- NEW Origin trust boundary confirmed: gamdom4567.com is the verified shared backend for all 8000x mirrors (byte-identical /client-api headers, Fastly 421 on Host-header injection proves cert binding, heade
- CHANGED Hypothesis "gamdom4567.com is shadow origin with shared /client-api trust boundary" confidence raised from 50 → 70 (bigpickle) / 70 (nemotron3); evidence_needed now "None — origin trust boundary confi
- CHANGED Next actionable probe shifted: nemotron3 still queues Host-header probe on gamdom4567.com; bigpickle marks it DONE and queues socket.io handshake on gamdommirrors.com instead

## 2026-09-04 16:44:47 UTC
- NEW Origin trust boundary **confirmed**: gamdom4567.com is the verified shared backend for all 8000x mirrors (byte-identical /client-api headers across gamdom4567.com/gamdom80006.com/gamdom80007.com; Fast
- CHANGED Next actionable probe shifted: nemotron3 queues socket.io handshake on gamdommirrors.com; bigpickle marks Host-header probe DONE and also queues socket.io handshake
- CHANGED Risk score raised: nemotron3 62→65, bigpickle 55→58 — confirmed 7 mirrors sharing single POST-only /client-api with verified shared origin backend

## 2026-09-04 19:16:10 UTC

## 2026-09-04 21:37:03 UTC

## 2026-09-04 23:19:40 UTC
- CHANGED Cross-mirror session replay hypothesis (gamdom80006) stayed at confidence 55; must now be shifted toward client-side token-storage analysis (localStorage vs HttpOnly cookie) to resolve purely passivel
- CHANGED gamdom80004.com 302-chain confirmed to gamdom80007.com; joins Pool B trust boundary as an 8th reachable hostname absent from the official status page (new surface).
- CHANGED socket.io handshake probe on gamdommirrors.com remains the lone passive survivor — queued by both agents, still confidence 38 (PARKED), no unauthenticated JSON surface confirmed.
- NEW socket.io handshake probe executed (passive): `GET /socket.io/?EIO=4&transport=polling` on gamdommirrors.com → HTTP 200, `{"sid":"...","upgrades":["websocket"],"pingInterval":25000,...}`, behind Fastl
- NEW Token transport RESOLVED passively from gamdom80006.com `client.41b06529227c4b8b6a1d.js` (597 KB, server's own bundle): request layer uses `credentials:"same-origin"`, zero `Authorization`/`Bearer` st
- CHANGED Cross-mirror ATO hypothesis narrowed: cookie-host-agnostic-acceptance on the shared backend is the only replay vector (no localStorage replay branch); document.cookie writes are feature-config junk on
- NEW gamdom80004.com discovered (302 → gamdom0007.com, Fastly Pool B) — 8th alias widening shared trust boundary
- NEW gamdommirrors.com socket.io handshake confirmed (sid issued, upgrades: websocket) — but Fastly pool breaks session persistence
- CHANGED gamdom4567.com shadow-origin hypothesis CONFIRMED (confidence 70 → DONE) — byte-identical /client-api headers across gamdom4567/gamdom80006/gamdom80007; Fastly 421 on Host-header injection proves cert
- CHANGED Cross-mirror session sharing hypothesis now strengthened by verified shared origin (gamdom4567.com = backend for 8000x) — evidence_needed reduced to single cookie replay test
- CHANGED nemotron3 risk score 62→65, bigpickle 55→58 — confirmed 7 mirrors + origin boundary verified

## 2026-09-05 01:09:54 UTC
- NEW gamdom80004.com discovered (302 → gamdom80007.com, Fastly Pool B) — 8th alias widening shared trust boundary
- NEW Token transport RESOLVED passively from gamdom80006.com `client.41b06529227c4b8b6a1d.js`: request layer uses `credentials:"same-origin"`, zero `Authorization`/`Bearer` headers, no localStorage token —
- NEW socket.io handshake on gamdommirrors.com executed (passive): `GET /socket.io/?EIO=4&transport=polling` → HTTP 200, `{"sid":"...","upgrades":["websocket"],"pingInterval":25000,...}`, behind Fastly Pool
- CHANGED gamdom4567.com shadow-origin hypothesis CONFIRMED (confidence 70 → DONE) — byte-identical /client-api headers across gamdom4567/gamdom80006/gamdom80007; Fastly 421 on Host-header injection proves cert
- CHANGED Cross-mirror session sharing hypothesis strengthened by verified shared origin (gamdom4567.com = backend for 8000x) — evidence_needed reduced to single cookie replay test
- CHANGED Cross-mirror ATO hypothesis narrowed: cookie-host-agnostic-acceptance on shared backend is the only replay vector (no localStorage replay branch)

## 2026-09-05 05:52:38 UTC
- NEW gamdom80004.com discovered (302 → gamdom80007.com, Fastly Pool B) — 8th alias widening shared trust boundary
- NEW Token transport RESOLVED passively from gamdom80006.com `client.41b06529227c4b8b6a1d.js`: request layer uses `credentials:"same-origin"`, zero `Authorization`/`Bearer` headers, no localStorage token —
- NEW socket.io handshake on gamdommirrors.com executed (passive): `GET /socket.io/?EIO=4&transport=polling` → HTTP 200, `{"sid":"...","upgrades":["websocket"],"pingInterval":25000,...}`, behind Fastly Pool
- CHANGED gamdom4567.com shadow-origin hypothesis CONFIRMED (confidence 70 → DONE) — byte-identical /client-api headers across gamdom4567/gamdom80006/gamdom80007; Fastly 421 on Host-header injection proves cert
- CHANGED Cross-mirror session sharing hypothesis strengthened by verified shared origin (gamdom4567.com = backend for 8000x) — evidence_needed reduced to single cookie replay test
- CHANGED Cross-mirror ATO hypothesis narrowed: cookie-host-agnostic-acceptance on shared backend is the only replay vector (no localStorage replay branch)
- CHANGED gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle

## 2026-09-05 10:01:28 UTC
- NEW gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend spans entire brand, not just mirrors
- NEW gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory implying 8th API-serving mirror
- NEW gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credentialed replay blocked
- CHANGED gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle

## 2026-09-05 13:24:15 UTC
- NEW BREADTH-SCAN flagship/subdomain surface (previously under-probed): dashboard.gamdom.com resolves on flagship Fastly Pool A but is 403-locked at edge everywhere (/ /login /api /graphql /health /static 
- NEW click.gamdom.com → CNAME eu-proxy-1.symplifymail.com → eu-iv-1.symplifymail.com (192.165.55.11, third-party SymplifyMail email provider) serving stock nginx default page (200, X-Robots noindex, Last-M
- NEW help.gamdom.com → Intercom-hosted help center (x-intercom-version, /en/ 302, Intercom CSP) — standard third-party helpdesk SaaS, benign.
- NEW gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend spans entire brand, not just mirrors
- NEW gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory implying 8th API-serving mirror
- NEW gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credentialed replay blocked
- CHANGED gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle

## 2026-09-05 16:21:43 UTC
- NEW BREADTH-SCAN flagship/subdomain surface (previously under-probed): dashboard.gamdom.com resolves on flagship Fastly Pool A but is 403-locked at edge everywhere (/ /login /api /graphql /health /static 
- NEW click.gamdom.com → CNAME eu-proxy-1.symplifymail.com → eu-iv-1.symplifymail.com (192.165.55.11, third-party SymplifyMail email provider) serving stock nginx default page (200, X-Robots noindex, Last-M
- NEW help.gamdom.com → Intercom-hosted help center (x-intercom-version, /en/ 302, Intercom CSP) — standard third-party helpdesk SaaS, benign.
- NEW gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend spans entire brand, not just mirrors
- NEW gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory implying 8th API-serving mirror
- NEW gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credentialed replay blocked
- CHANGED gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle
- NEW gamdom.com/io/eu/vip/win/client-api: GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend spans entire brand, not just mirrors
- NEW gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrects prior inventory implying 8th API-serving mirror
- NEW gamdom80007.com/client-api: OPTIONS preflight and Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credentialed replay blocked
- NEW dashboard.gamdom.com: resolves on flagship Fastly Pool A, 403-locked at edge (Varnish Error 54113) across all probed paths (/ /login /api /graphql /health /static) — genuine scoped admin hostname, no 
- NEW click.gamdom.com: CNAME eu-proxy-1.symplifymail.com → eu-iv-1.symplifymail.com (192.165.55.11, third-party SymplifyMail) serving stock nginx default page — not dangling; subdomain-takeover watchlist i
- NEW help.gamdom.com: Intercom-hosted help center (x-intercom-version, /en/ 302, Intercom CSP) — standard third-party SaaS, benign
- CHANGED gamdom80007.com port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle

## 2026-09-05 18:29:58 UTC
- NEW Brand-wide origin trust pool confirmed: `gamdom.com/io/eu/vip/win/client-api` GET 400 body `Invalid request, only POST` byte-identical across Pool A + Pool B — shared origin backend spans entire brand
- NEW `gamdom80004.com/client-api`: 302 → `gamdom80007.com/client-api` (redirect alias only); corrects prior inventory implying 8th API-serving mirror
- NEW `gamdom80007.com/client-api`: OPTIONS preflight and Origin-header GET → 400 with no `Access-Control-Allow-*` headers; browser cross-origin credentialed replay blocked
- NEW `dashboard.gamdom.com`: resolves on flagship Fastly Pool A, 403-locked at edge (Varnish Error 54113) across all paths — genuine scoped admin hostname, no bypass
- NEW `click.gamdom.com`: CNAME `eu-proxy-1.symplifymail.com` → `eu-iv-1.symplifymail.com` (192.165.55.11) serving stock nginx default page — not dangling; subdomain-takeover watchlist item
- NEW `help.gamdom.com`: Intercom-hosted help center (`x-intercom-version`, `/en/` 302) — standard third-party SaaS, benign
- NEW `gamdom80007.com` port-80: 301→HTTPS confirms HTTPS-only mirror surface; closes cleartext-cookie/downgrade angle
- CHANGED Origin trust boundary now spans flagship `gamdom.com` (Pool A) + all 7 mirrors + `gamdom4567.com` (Pool B) via byte-identical `/client-api` signature

## 2026-09-05 20:48:41 UTC

## 2026-09-05 22:44:44 UTC
- NEW 4 regional TLDs confirmed on Pool A: gamdom.eu, gamdom.io, gamdom.vip, gamdom.win (151.101.x.52) — each serves full SPA + byte-identical `/client-api` 400 `Invalid request, only POST`; widens trust po
- NEW Auth/admin-flavored subdomains (login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com) all NXDOMAIN — live scoped surface fully enumerated, no hidden auth endpoints
- NEW Cookie issuance policy uniform: gamdom.eu/gamdom.win root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — per-host across entire 13-host fleet
- CHANGED Origin trust boundary now spans flagship `gamdom.com` (Pool A) + 4 regional TLDs + all 7 mirrors + `gamdom4567.com` (Pool B) via byte-identical `/client-api` signature
- CHANGED gamdom80004.com/client-api: 302 → gamdom80007.com/client-api (redirect alias only); corrected prior inventory implying 8th API-serving mirror
- CHANGED gamdom80007.com/client-api: OPTIONS preflight + Origin-header GET → 400 with no Access-Control-Allow-* headers; browser cross-origin credentialed replay blocked
- CHANGED dashboard.gamdom.com: genuine scoped admin hostname on Pool A, fully 403-locked at edge (Varnish Error 54113) across all paths — inventory knowledge only
- CHANGED click.gamdom.com: CNAME to SymplifyMail (eu-iv-1.symplifymail.com 192.165.55.11) serving stock nginx default — not dangling; subdomain-takeover watchlist item
- CHANGED help.gamdom.com: Intercom-hosted help center — benign third-party SaaS

## 2026-09-06 00:18:55 UTC

## 2026-09-06 04:50:01 UTC
- NEW gamdom90471.com confirmed as 8th mirror alias / 14th hostname in brand trust pool (CNAME→gamdom4567.com, Fastly Pool B, self-referenced 18× in flagship HomePage.js via gamdom-girisi.com SEO hub, DigiC
- NEW gamdom-girisi.com identified as official brand SEO/redirect hub (Cloudflare, Turkish, 61 KB) funneling to gamdom90471.com + discord/telegram; linked from flagship HomePage.js — legitimate discovery se
- NEW gamdomgiris.link confirmed as third-party Cloudflare landing (no Fastly origin /client-api signature) — NOT in-scope brand origin (watchlist only)
- NEW 4 regional TLDs (gamdom.eu/io/vip/win) confirmed on Pool A (151.101.x.52) — each serves full SPA + byte-identical `/client-api` 400 `Invalid request, only POST`; widens trust pool to 13 live hosts + 1
- NEW Auth/admin-flavored subdomains (login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com) all NXDOMAIN — live scoped surface fully enumerated, no hidden auth endpoints
- NEW Cookie issuance policy uniform: gamdom.eu/gamdom.win root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — per-host across entire 13-host fleet
- NEW gamdommirrors.com/status/gamdom-domains official Uptime status page lists exactly 7 monitors = com/eu/io/vip/win/80006/80007 → independently ratifies 4 regional TLDs as official and confirms gamdom904
- CHANGED Origin trust boundary now spans flagship gamdom.com (Pool A) + 4 regional TLDs + all 7 mirrors + gamdom4567.com (Pool B) + gamdom90471.com (Pool B alias) + gamdom80004.com (redirect alias) = 14-host f
- CHANGED Risk score increased from 65 → 68: confirmed 14-host fleet sharing single POST-only `/client-api` identity/wallet proxy; top hypothesis (cross-domain ATO via session sharing) has passive verification 

## 2026-09-06 09:14:53 UTC
- NEW gamdom90471.com confirmed as 8th mirror alias / 14th hostname in brand trust pool (CNAME→gamdom4567.com, Fastly Pool B, self-referenced 18× in flagship HomePage.js via gamdom-girisi.com SEO hub, DigiC
- NEW gamdom-girisi.com identified as official brand SEO/redirect hub (Cloudflare, Turkish, 61 KB) funneling to gamdom90471.com + discord/telegram; linked from flagship HomePage.js — legitimate discovery se
- NEW gamdomgiris.link confirmed as third-party Cloudflare landing (no Fastly origin /client-api signature) — NOT in-scope brand origin (watchlist only)
- NEW 4 regional TLDs (gamdom.eu/io/vip/win) confirmed on Pool A (151.101.x.52) — each serves full SPA + byte-identical `/client-api` 400 `Invalid request, only POST`; widens trust pool to 13 live hosts + 1
- NEW Auth/admin-flavored subdomains (login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com) all NXDOMAIN — live scoped surface fully enumerated, no hidden auth endpoints
- NEW Cookie issuance policy uniform: gamdom.eu/gamdom.win root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — per-host across entire 13-host fleet
- NEW gamdommirrors.com/status/gamdom-domains official Uptime status page lists exactly 7 monitors = com/eu/io/vip/win/80006/80007 → independently ratifies 4 regional TLDs as official and confirms gamdom904
- CHANGED Origin trust boundary now spans flagship gamdom.com (Pool A) + 4 regional TLDs + all 7 mirrors + gamdom4567.com (Pool B) + gamdom90471.com (Pool B alias) + gamdom80004.com (redirect alias) = 14-host f
- CHANGED Risk score increased from 65 → 68: confirmed 14-host fleet sharing single POST-only `/client-api` identity/wallet proxy; top hypothesis (cross-domain ATO via session sharing) has passive verification 

## 2026-09-06 13:03:53 UTC
- NEW gamdom90471.com confirmed as 8th mirror alias / 14th hostname in brand trust pool (CNAME→gamdom4567.com, Fastly Pool B, self-referenced 18× in flagship HomePage.js via gamdom-girisi.com SEO hub, DigiC
- NEW gamdom-girisi.com identified as official brand SEO/redirect hub (Cloudflare, Turkish, 61 KB) funneling to gamdom90471.com + discord/telegram; linked from flagship HomePage.js — legitimate discovery se
- NEW gamdomgiris.link confirmed as third-party Cloudflare landing (no Fastly origin /client-api signature) — NOT in-scope brand origin (watchlist only)
- NEW 4 regional TLDs (gamdom.eu/io/vip/win) confirmed on Pool A (151.101.x.52) — each serves full SPA + byte-identical `/client-api` 400 `Invalid request, only POST`; widens trust pool to 13 live hosts + 1
- NEW Auth/admin-flavored subdomains (login/sso/my/account/secure/admin/m/portal/support/web/t.gamdom.com) all NXDOMAIN — live scoped surface fully enumerated, no hidden auth endpoints
- NEW Cookie issuance policy uniform: gamdom.eu/gamdom.win root GET sets identical `gd-lang=en-gb` host-only cookie (no Domain, no SameSite/HttpOnly) — per-host across entire 13-host fleet
- NEW gamdommirrors.com/status/gamdom-domains official Uptime status page lists exactly 7 monitors = com/eu/io/vip/win/80006/80007 → independently ratifies 4 regional TLDs as official and confirms gamdom904
- CHANGED Origin trust boundary now spans flagship gamdom.com (Pool A) + 4 regional TLDs + all 7 mirrors + gamdom4567.com (Pool B) + gamdom90471.com (Pool B alias) + gamdom80004.com (redirect alias) = 14-host f
- CHANGED Risk score increased from 65 → 68: confirmed 14-host fleet sharing single POST-only `/client-api` identity/wallet proxy; top hypothesis (cross-domain ATO via session sharing) has passive verification 

## 2026-09-06 16:18:18 UTC

## 2026-09-06 18:29:05 UTC
- NEW gamdom80003.com confirmed as 9th live mirror: CNAME→gamdom4567.com, Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", /client-api 400 (26B) byte-iden
- NEW gamdom90472.com confirmed as 16th hostname: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, off 7-monitor status page
- NEW gamdom80005.com resolves to 8.8.8.8 (Google DNS IP, not Fastly) — REJECTED out-of-scope
- NEW gamdom80008.com resolves to 192.64.119.33 (not Fastly) — REJECTED out-of-scope
- CHANGED Brand trust pool expanded from 14 → 17 hosts (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect + gamdom90472.com provisioned
- CHANGED Origin trust boundary now spans 17 hosts across Pool A (gamdom.com/eu/io/vip/win) and Pool B (80003/80004/80006/80007/90471/90472/4567) via byte-identical /client-api signature and shared weak-ETag on

## 2026-09-06 20:35:40 UTC
- NEW gamdom80003.com confirmed as 9th live mirror: CNAME→gamdom4567.com, Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", /client-api 400 (26B) byte-iden
- NEW gamdom90472.com confirmed as 16th hostname: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, off 7-monitor status page
- NEW gamdom80005.com resolves to 8.8.8.8 (Google DNS IP, not Fastly) — REJECTED out-of-scope
- NEW gamdom80008.com resolves to 192.64.119.33 (not Fastly) — REJECTED out-of-scope
- CHANGED Brand trust pool expanded from 14 → 17 hosts (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provi
- CHANGED Origin trust boundary now spans 17 hosts across Pool A (gamdom.com/eu/io/vip/win) and Pool B (80003/80004/80006/80007/90471/90472/4567) via byte-identical /client-api signature and shared weak-ETag on

## 2026-09-06 22:23:37 UTC
- NEW gamdom80003.com confirmed as 9th live mirror: CNAME→gamdom4567.com, Fastly Pool B (all 4 edges), /health 200 with identical weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", /client-api 400 (26B) byte-iden
- NEW gamdom90472.com confirmed as 16th hostname: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live, off 7-monitor status page
- NEW gamdom80005.com resolves to 8.8.8.8 (Google DNS IP, not Fastly) — REJECTED out-of-scope
- NEW gamdom80008.com resolves to 192.64.119.33 (not Fastly) — REJECTED out-of-scope
- NEW teamgamdom.com confirmed as 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert same CA) hosting staging.teamgamdom.com (503 Maintenance) + shreeram-dynamic-test.teamgamdom.com (nginx Basic
- NEW gamdom90488.com public numbered alias CNAME→shreeram-dynamic-test.teamgamdom.com pins brand mirror onto internal Basic-auth-gated nginx test backend; alias edge SNI not yet live
- NEW gamdom90480.com/gamdom90482.com both CNAME→gamdom4567.com, Pool B, client-api 421 — 5th/6th provisioned 9047x-family alias
- NEW gamdom-prod-maintenance-page.s3.eu-west-2.amazonaws.com staging page assets public-read (200), ListObjects/root denied (403) — closed hosting, inventory only
- NEW 10x gamdom9047x.com (90474/90476/90477/90478/90479/90481/90483/90484/90485/90486) resolve to 192.64.119.x/162.255.119.x (non-Fastly) — REJECTED out-of-scope
- CHANGED Brand trust pool expanded from 14 → 17 hosts (flagship + 4 regional TLDs + 9 live mirrors + gamdom4567.com origin + gamdom90471.com provisioned + gamdom80004.com redirect alias + gamdom90472.com provi
- CHANGED Origin trust boundary now spans 17 hosts across Pool A (gamdom.com/eu/io/vip/win) and Pool B (80003/80004/80006/80007/90471/90472/4567) via byte-identical /client-api signature and shared weak-ETag on
- CHANGED New distinct origin realm discovered: teamgamdom.com (Pool A) with Basic-auth-gated test backend shreeram-dynamic-test.teamgamdom.com — separate from gamdom4567.com Starlette pool

## 2026-09-07 00:08:08 UTC
- NEW gamdom80003.com confirmed 9th live mirror: CNAME→gamdom4567.com, Fastly Pool B (4 edges), /health 200 weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", /client-api 400 26B byte-identical
- NEW gamdom90472.com confirmed 16th hostname: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live
- NEW teamgamdom.com confirmed 2nd in-scope brand TLD: Route53, Fastly Pool A, Certainly cert; hosts staging.teamgamdom.com (503 Maintenance) + shreeram-dynamic-test.teamgamdom.com (nginx Basic realm="secre
- NEW gamdom90488.com public alias CNAME→shreeram-dynamic-test.teamgamdom.com pins brand mirror onto internal Basic-auth test backend; alias edge SNI not live (TLS-NOMATCH)
- NEW gamdom90480.com/gamdom90482.com both CNAME→gamdom4567.com, Pool B, /client-api 421 — 5th/6th provisioned 9047x-family aliases
- CHANGED Brand trust pool expanded 14→17 hosts across Pool A (gamdom.com/eu/io/vip/win) and Pool B (80003/80004/80006/80007/90471/90472/4567) via byte-identical /client-api + shared weak-ETag
- CHANGED New distinct origin realm discovered: teamgamdom.com (Pool A) with Basic-auth-gated test backend shreeram-dynamic-test.teamgamdom.com — separate from gamdom4567.com Starlette pool
- CHANGED gamdommirrors.com status page still exactly 7 monitors (com/eu/io/vip/win/80006/80007) — 90471/90472/80003/80004/90480/90482/90488 off monitor list

## 2026-09-07 04:55:08 UTC
- NEW teamgamdom.com confirmed as 2nd in-scope brand TLD (Route53, Fastly Pool A, Certainly cert) hosting staging.teamgamdom.com (503 Maintenance) + shreeram-dynamic-test.teamgamdom.com (nginx Basic realm="
- NEW gamdom90488.com public alias CNAME→shreeram-dynamic-test.teamgamdom.com pins brand mirror onto internal Basic-auth-gated nginx test backend; alias edge SNI not yet live (TLS-NOMATCH)
- NEW gamdom90480.com/gamdom90482.com both CNAME→gamdom4567.com, Pool B, /client-api 421 — 5th/6th provisioned 9047x-family aliases
- NEW gamdom80003.com confirmed 9th live mirror: CNAME→gamdom4567.com, Fastly Pool B (4 edges), /health 200 weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", /client-api 400 26B byte-identical
- NEW gamdom90472.com confirmed 16th hostname: CNAME→gamdom4567.com, Fastly Pool B DNS, all edges HTTP 000 — provisioned-not-yet-live
- CHANGED Brand trust pool expanded 14→17 hosts across Pool A (gamdom.com/eu/io/vip/win) and Pool B (80003/80004/80006/80007/90471/90472/4567) via byte-identical /client-api + shared weak-ETag
- CHANGED gamdommirrors.com status page still exactly 7 monitors (com/eu/io/vip/win/80006/80007) — 90471/90472/80003/80004/90480/90482/90488 off monitor list

## 2026-09-07 09:58:15 UTC

## 2026-09-07 15:40:49 UTC
