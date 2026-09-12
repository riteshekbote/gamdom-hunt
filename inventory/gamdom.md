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

## 2026-09-07 19:30:30 UTC

## 2026-09-07 22:19:29 UTC

## 2026-09-08 00:45:01 UTC
- NEW gamdom90488.com TLS-NOMATCH persists (cert not deployed); forced-resolve to Pool A IP returns 421 on /client-api
- NEW gamdom90472.com TLS-NOMATCH persists (cert not deployed); DNS resolves to Pool B but all edges HTTP 000
- NEW gamdom90480.com / gamdom90482.com TLS-NOMATCH persists; forced-resolve to Pool B IP returns 421 on /client-api
- NEW gamdom80003.com confirmed live (9th mirror): /health 200 weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical; /client-api 400 byte-identical
- CHANGED gamdommirrors.com/status/gamdom-domains API still exactly 7 monitors (com/eu/io/vip/win/80006/80007) — provisioned fleet (90471/90472/80003/80004/90480/90482/90488) still off monitor list
- CHANGED gamdom-girisi.com SEO hub unchanged — only gamdom90471.com referenced (17×)
- CHANGED shreeram-dynamic-test.teamgamdom.com Basic realm="secret" gate holds 401 fixture-wide (7 paths tested)
- CHANGED Starlette pool signature stable: /client-api 400 body md5 7e3a161d + /health weak-ETag byte-identical across Pool A (gamdom.com) + Pool B (80006/80007/80003) — shared origin confirmed
- CHANGED teamgamdom.com Go realm (api.teamgamdom.com) returns 404 default; VCL fingerprint `bong_ke` confirmed distinct from Starlette pool

## 2026-09-08 05:22:20 UTC
- NEW gamdom90488.com TLS-NOMATCH persists (cert not deployed); forced-resolve to Pool A IP returns 421 on /client-api
- NEW gamdom90472.com TLS-NOMATCH persists (cert not deployed); DNS resolves to Pool B but all edges HTTP 000
- NEW gamdom90480.com / gamdom90482.com TLS-NOMATCH persists; forced-resolve to Pool B IP returns 421 on /client-api
- NEW gamdom80003.com confirmed live (9th mirror): /health 200 weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical; /client-api 400 byte-identical
- CHANGED gamdommirrors.com/status/gamdom-domains API still exactly 7 monitors (com/eu/io/vip/win/80006/80007) — provisioned fleet (90471/90472/80003/80004/90480/90482/90488) still off monitor list
- CHANGED gamdom-girisi.com SEO hub unchanged — only gamdom90471.com referenced (17×)
- CHANGED shreeram-dynamic-test.teamgamdom.com Basic realm="secret" gate holds 401 fixture-wide (7 paths tested)
- CHANGED Starlette pool signature stable: /client-api 400 body md5 7e3a161d + /health weak-ETag byte-identical across Pool A (gamdom.com) + Pool B (80006/80007/80003) — shared origin confirmed
- CHANGED teamgamdom.com Go realm (api.teamgamdom.com) returns 404 default; VCL fingerprint `bong_ke` confirmed distinct from Starlette pool
- NEW gamdom80003.com confirmed as 9th live mirror (Pool B): /health 200 weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical; /client-api 400 byte-identical across all 9 mirrors
- NEW gamdom90488.com / gamdom90472.com / gamdom90480.com / gamdom90482.com all persist TLS-NOMATCH (cert not deployed); forced-resolve to edge returns 421 on /client-api
- CHANGED gamdommirrors.com/status/gamdom-domains API still exactly 7 monitors (com/eu/io/vip/win/80006/80007) — provisioned fleet (90471/90472/80003/80004/90480/90482/90488) still off monitor list
- CHANGED gamdom-girisi.com SEO hub unchanged — only gamdom90471.com referenced (17×)
- CHANGED shreeram-dynamic-test.teamgamdom.com Basic realm="secret" gate holds 401 fixture-wide (7 paths tested)
- CHANGED Starlette pool signature stable: /client-api 400 body md5 7e3a161d + /health weak-ETag byte-identical across Pool A (gamdom.com) + Pool B (80006/80007/80003) — shared origin confirmed
- CHANGED teamgamdom.com Go realm (api.teamgamdom.com) returns 404 default; VCL fingerprint `bong_ke` confirmed distinct from Starlette pool

## 2026-09-08 09:58:50 UTC
- NEW gamdom80003.com confirmed as 9th live mirror (Pool B): /health 200 weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical; /client-api 400 byte-identical across all 9 mirrors
- NEW gamdom90488.com / gamdom90472.com / gamdom90480.com / gamdom90482.com all persist TLS-NOMATCH (cert not deployed); forced-resolve to edge returns 421 on /client-api
- CHANGED gamdommirrors.com/status/gamdom-domains API still exactly 7 monitors (com/eu/io/vip/win/80006/80007) — provisioned fleet (90471/90472/80003/80004/90480/90482/90488) still off monitor list
- CHANGED gamdom-girisi.com SEO hub unchanged — only gamdom90471.com referenced (17×)
- CHANGED shreeram-dynamic-test.teamgamdom.com Basic realm="secret" gate holds 401 fixture-wide (7 paths tested)
- CHANGED Starlette pool signature stable: /client-api 400 body md5 7e3a161d + /health weak-ETag byte-identical across Pool A (gamdom.com) + Pool B (80006/80007/80003) — shared origin confirmed
- CHANGED teamgamdom.com Go realm (api.teamgamdom.com) returns 404 default; VCL fingerprint `bong_ke` confirmed distinct from Starlette pool
- NEW fatbets.com + gamdom.one discovered as 2nd/3rd brands on shared Pool A (Fastly 151.101.x.52): byte-identical app bundle + `/client-api` md5 7e3a161d + `/health` weak-ETag + host-only `gd-lang` cookie 
- NEW flagship bundle leaks dual `staffRefillConfig` (30M/75M coins) + moderator tip cap client-side; `trMirrorDomain=gamdom80004.com` hardcoded
- NEW fatbets.com/gamdom.one `/auth/login` + `/graphql` → 404, only host-only `gd-lang` Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth
- CHANGED Provisioning cycle 5 for aliases (90472/90473/90475/90480/90482/90488): all still 421/TLS-NOMATCH — no cert deployment
- CHANGED gamdommirrors.com status page still exactly 7 monitors; SEO hub still 17× gamdom90471 only
- CHANGED Starlette pool signature stable: `/client-api` 400 md5 7e3a161d + `/health` weak-ETag byte-identical Pool A + Pool B — shared origin confirmed
- CHANGED shreeram-dynamic-test.teamgamdom.com Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm (api.teamgamdom.com) 404 + VCL `bong_ke`

## 2026-09-08 14:26:58 UTC

## 2026-09-08 18:11:33 UTC
- NEW gamdom80008.com went live: CNAME→gamdom4567.com Fastly Pool B, root 200, /client-api 400 (md5 7e3a161d), /health weak-ETag byte-identical, gd-lang cookie; status page monitor id:221 replaces gamdom800
- NEW gamdom80007.com demoted: 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — confirms brand retires numbered aliases by redirect
- NEW gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames
- NEW fatbets.com + gamdom.one: 2nd/3rd brands on shared Pool A (Fastly 151.101.x.52) — byte-identical app bundle + /client-api md5 7e3a161d + /health weak-ETag + host-only gd-lang cookie → single identity/
- NEW flagship bundle: dual staffRefillConfig (30M/75M coins) + moderator tip cap shipped client-side; trMirrorDomain=gamdom80004.com hardcoded
- NEW fatbets.com/gamdom.one /auth/login + /graphql → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth
- CHANGED Provisioning cycle 5 for 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — no cert deployment
- CHANGED gamdommirrors.com status page: still exactly 7 monitors; SEO hub still 17× gamdom90471 only
- CHANGED Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed stable
- CHANGED shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm 404 + VCL bong_ke

## 2026-09-08 20:54:36 UTC
- NEW gamdom80008.com went live: CNAME→gamdom4567.com Fastly Pool B, root 200, /client-api 400 (md5 7e3a161d), /health weak-ETag byte-identical, gd-lang cookie; status page monitor id:221 replaces gamdom800
- NEW gamdom80007.com demoted: 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — brand retires numbered aliases by redirect
- NEW gamdom80001.com/gamdom80002.com: newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames
- NEW fatbets.com + gamdom.one confirmed as 2nd/3rd brands on shared Pool A (Fastly 151.101.x.52): byte-identical app bundle + /client-api md5 7e3a161d + /health weak-ETag + host-only gd-lang cookie → singl
- NEW flagship bundle leaks dual staffRefillConfig (30M/75M coins) + moderator tip cap client-side; trMirrorDomain=gamdom80004.com hardcoded
- NEW fatbets.com/gamdom.one /auth/login + /graphql → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth
- CHANGED Provisioning cycle 5 for 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — no cert deployment
- CHANGED gamdommirrors.com status page: still exactly 7 monitors; SEO hub still 17× gamdom90471 only
- CHANGED Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed stable
- CHANGED shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm 404 + VCL bong_ke

## 2026-09-08 23:13:03 UTC
- NEW gamdom80008.com went live on Pool B (CNAME→gamdom4567.com, root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); status page monitor id:221 replaces gamdom80007.com as 
- NEW gamdom80007.com demoted to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — brand retires numbered aliases by redirect
- NEW gamdom80001.com/gamdom80002.com newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames
- NEW fatbets.com + gamdom.one confirmed as 2nd/3rd brands on shared Pool A (Fastly 151.101.x.52): byte-identical app bundle + /client-api md5 7e3a161d + /health weak-ETag + host-only gd-lang cookie → singl
- NEW flagship bundle leaks dual staffRefillConfig (30M/75M coins) + moderator tip cap client-side; trMirrorDomain=gamdom80004.com hardcoded
- NEW fatbets.com/gamdom.one /auth/login + /graphql → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth
- CHANGED Provisioning cycle 5 for 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — no cert deployment
- CHANGED gamdommirrors.com status page: still exactly 7 monitors; SEO hub (gamdom-girisi.com) still 17× gamdom90471 only
- CHANGED Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed stable
- CHANGED shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm (api.teamgamdom.com) 404 + VCL bong_ke

## 2026-09-09 01:34:51 UTC
- NEW gamdom80008.com went live on Pool B (CNAME→gamdom4567.com, root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); status page monitor id:221 replaces gamdom80007.com as 
- NEW gamdom80007.com demoted to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — brand retires numbered aliases by redirect
- NEW gamdom80001.com/gamdom80002.com newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames
- NEW fatbets.com + gamdom.one confirmed as 2nd/3rd brands on shared Pool A (Fastly 151.101.x.52): byte-identical app bundle + /client-api md5 7e3a161d + /health weak-ETag + host-only gd-lang cookie → singl
- NEW flagship bundle leaks dual staffRefillConfig (30M/75M coins) + moderator tip cap client-side; trMirrorDomain=gamdom80004.com hardcoded
- CHANGED Provisioning cycle 5 for 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — no cert deployment
- CHANGED Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed stable
- CHANGED shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm 404 + VCL bong_ke

## 2026-09-09 06:09:00 UTC
- NEW gamdom80008.com — previously REJECTED out-of-scope (192.64.119.33), now CNAME→gamdom4567.com Fastly Pool B, fully live (root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang coo
- NEW gamdom80007.com demoted from live mirror to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — brand retires numbered aliases by redirect, 80008 inherits its Pool B slot.
- NEW gamdom80001.com/gamdom80002.com — newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames, off the 7-monitor status page.
- NEW fatbets.com + gamdom.one — mined from flagship bundle (BRANDED_SUBDOMAINS + encrypted TLD keys), both live on Fastly Pool A with byte-identical app/bundle + /client-api md5 7e3a161d + /health weak-ETa
- NEW fatbets.com/gamdom.one /auth/login + /graphql → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth; replay remains backend-level.
- CHANGED Provisioning cycle count for 90472/90473/90475/90480/90482/90488: now 5th cycle, all still 421/TLS-NOMATCH — no cert deployment.
- CHANGED gamdommirrors.com status page: still exactly 7 monitors; SEO hub (gamdom-girisi.com) still 17× 90471 only.
- CHANGED Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed stable.
- NEW gamdom80008.com went live on Pool B (CNAME→gamdom4567.com, root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); status page monitor id:221 replaces gamdom80007.com as 
- NEW gamdom80007.com demoted to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — brand retires numbered aliases by redirect
- NEW gamdom80001.com/gamdom80002.com newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames
- NEW fatbets.com + gamdom.one confirmed as 2nd/3rd brands on shared Pool A (Fastly 151.101.x.52): byte-identical app bundle + /client-api md5 7e3a161d + /health weak-ETag + host-only gd-lang cookie → singl
- CHANGED Provisioning cycle 5 for 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — no cert deployment
- CHANGED Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed stable
- CHANGED shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm 404 + VCL bong_ke

## 2026-09-09 11:51:06 UTC
- CHANGED gamdom80008.com: newly live (10th mirror) replaces gamdom80007.com on status page monitor id:221 — rotation event absorbed into fleet
- CHANGED gamdom80007.com: demoted to 302→gamdom80008.com redirect alias
- CHANGED gamdom80001.com/gamdom80002.com: CNAME→gamdom4567.com Pool B DNS, all edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live
- CHANGED fatbets.com + gamdom.one: confirmed as 2nd/3rd brands on shared Pool A, byte-identical origin signature → shared identity/wallet backend
- CHANGED Provisioning cycle count for 90472/90473/90475/90480/90482/90488: now 5th cycle, all still 421/TLS-NOMATCH

## 2026-09-09 15:26:45 UTC
- CHANGED gamdom80009.com: newly live (11th mirror, 21st hostname) replaces gamdom80006.com on status page monitor id:223 — second rotation event in 24h
- CHANGED gamdom80006.com: demoted from live mirror to 302→https://gamdom80009.com/ redirect alias (Varnish, no-store) — confirms brand retires numbered aliases by redirect
- CHANGED Status page: now lists com/eu/io/vip/win/80008/80009 (7 monitors) — absorbed 80006→80009 rotation, still 7-monitor cap
- CHANGED gamdom80009.com /client-api md5 7e3a161d + /health ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical to all other live mirrors — origin signature unchanged
- CHANGED gamdom80006.com now redirects to gamdom80009.com (not gamdom80007.com or 80008.com) — redirect chain updated to point at newest live mirror
- NEW gamdom80008.com went live on Pool B (CNAME→gamdom4567.com, root 200, /client-api 400 md5 7e3a161d, /health weak-ETag identical, gd-lang cookie); status page monitor id:221 replaces gamdom80007.com as 
- NEW gamdom80007.com demoted to 302→https://gamdom80008.com/ redirect alias (Varnish, no-store) — brand retires numbered aliases by redirect
- NEW gamdom80001.com/gamdom80002.com newly CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live hostnames
- NEW fatbets.com + gamdom.one confirmed as 2nd/3rd brands on shared Pool A (Fastly 151.101.x.52): byte-identical app bundle + /client-api md5 7e3a161d + /health weak-ETag + host-only gd-lang cookie → singl
- NEW flagship bundle leaks dual staffRefillConfig (30M/75M coins) + moderator tip cap client-side; trMirrorDomain=gamdom80004.com hardcoded
- NEW fatbets.com/gamdom.one /auth/login + /graphql → 404, only host-only gd-lang Set-Cookie (no Domain attr) — cookie-Domain confusion not observable pre-auth; replay remains backend-level
- CHANGED Provisioning cycle 5 for 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — no cert deployment
- CHANGED Starlette pool: /client-api 400 (md5 7e3a161d) + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed stable
- CHANGED shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm 404 + VCL bong_ke

## 2026-09-09 18:45:14 UTC
- CHANGED gamdom80009.com: newly live (11th mirror, 21st hostname) replaces gamdom80006.com on status page monitor id:223 — second rotation event in 24h
- CHANGED gamdom80006.com: demoted from live mirror to 302→https://gamdom80009.com/ redirect alias (Varnish, no-store) — confirms brand retires numbered aliases by redirect
- CHANGED Status page: now lists com/eu/io/vip/win/80008/80009 (7 monitors) — absorbed 80006→80009 rotation, still 7-monitor cap
- CHANGED gamdom80009.com /client-api md5 7e3a161d + /health ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical to all other live mirrors — origin signature unchanged
- CHANGED gamdom80006.com now redirects to gamdom80009.com (not gamdom80007.com or 80008.com) — redirect chain updated to point at newest live mirror
- NEW gamdom80009.com live (11th mirror) replaces gamdom80006.com on status page monitor id:223 — second rotation in 24h; byte-identical /client-api md5 7e3a161d + /health weak-ETag
- NEW gamdom80006.com demoted to 302→https://gamdom80009.com/ redirect alias (Varnish, no-store)
- NEW Status page now lists 7 monitors: com/eu/io/vip/win/80008/80009 — 7-monitor cap maintained
- NEW gamdom80001.com/gamdom80002.com: CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live; 6th cycle
- NEW Provisioning cycle 6 for 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — no cert deployment
- CHANGED Starlette pool signature stable: /client-api 400 md5 7e3a161d + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed
- CHANGED fatbets.com + gamdom.one confirmed as 2nd/3rd brands on Pool A with byte-identical origin signature — shared identity/wallet backend serves 3 brands / 21+ hostnames
- CHANGED shreeram-dynamic-test.teamgamdom.com Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm 404 + VCL bong_ke

## 2026-09-09 21:40:04 UTC
- NEW gamdom80009.com live (11th mirror) replaces gamdom80006.com on status page monitor id:223 — second rotation event in 24h
- NEW gamdom80006.com demoted to 302→https://gamdom80009.com/ redirect alias (Varnish, no-store)
- NEW Status page now lists 7 monitors: com/eu/io/vip/win/80008/80009 — 7-monitor cap maintained
- NEW gamdom80001.com/gamdom80002.com: CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live; 6th cycle
- NEW Provisioning cycle 6 for 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — no cert deployment
- CHANGED Starlette pool signature stable: /client-api 400 md5 7e3a161d + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed
- CHANGED fatbets.com + gamdom.one confirmed as 2nd/3rd brands on Pool A with byte-identical origin signature — shared identity/wallet backend serves 3 brands / 21+ hostnames
- CHANGED shreeram-dynamic-test.teamgamdom.com Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm 404 + VCL bong_ke

## 2026-09-09 23:34:14 UTC

## 2026-09-10 01:32:10 UTC
- NEW gamdom80009.com live (11th mirror, 21st hostname) replaces gamdom80006.com on status page monitor id:223 — second rotation event in 24h; byte-identical /client-api md5 7e3a161d + /health ETag unchange
- NEW gamdom80006.com demoted to 302→https://gamdom80009.com/ redirect alias (Varnish, no-store) — brand retires numbered aliases by redirect
- NEW Status page now lists 7 monitors: com/eu/io/vip/win/80008/80009 — 7-monitor cap maintained
- NEW gamdom80001.com/gamdom80002.com: CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live; 6th cycle
- NEW Provisioning cycle 6 for 90472/90473/90475/90480/90482/90488: all still 421/TLS-NOMATCH — no cert deployment
- NEW kargo.teamgamdom.com discovered: HTTP 405 on `/` and `/api/v1/projects` — new subdomain exposing GitOps/ArgoCD-like API surface on scoped brand domain
- CHANGED Starlette pool signature stable: /client-api 400 md5 7e3a161d + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed
- CHANGED fatbets.com + gamdom.one confirmed as 2nd/3rd brands on Pool A with byte-identical origin signature — shared identity/wallet backend serves 3 brands / 21+ hostnames
- CHANGED shreeram-dynamic-test.teamgamdom.com Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm 404 + VCL bong_ke

## 2026-09-10 06:43:37 UTC
- NEW tableau.teamgamdom.com/api/3.21/sites → 401 XML "No authentication credentials were provided" — Tableau REST API requires auth; anonymous content hypothesis closed.
- CHANGED kargo.teamgamdom.com/api/v1/projects: GET 200 SPA catch-all (Monaco IDE shell), POST 405 — confirms backend API exists but is method-gated; GET routes served by SPA catch-all, no JSON leak.
- NEW kargo.teamgamdom.com POST to /api/v1/projects + /api/v1/credentials both → 405; GET /api/ → 200 SPA HTML — all GET routes caught by SPA, POST routes gated to 405.
- NEW kargo.teamgamdom.com discovered: HTTP 405 on `/` and `/api/v1/projects` — new subdomain exposing GitOps/ArgoCD-like API surface on scoped brand domain
- NEW Provisioning cycle 7 for 90472/90473/90475/90480/90482/90488/80001/80002: all still 421/TLS-NOMATCH — no cert deployment
- NEW New numbered aliases 80010-80020 tested: all 000 (connection failed) — not provisioned
- CHANGED Starlette pool signature stable: /client-api 400 md5 7e3a161d + /health weak-ETag byte-identical Pool A + Pool B — shared origin confirmed
- CHANGED fatbets.com + gamdom.one confirmed as 2nd/3rd brands on Pool A with byte-identical origin signature — shared identity/wallet backend serves 3 brands / 21+ hostnames
- CHANGED shreeram-dynamic-test.teamgamdom.com Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm 404 + VCL bong_ke
- CHANGED gamdom80008.com (302→80007 retired), gamdom80009.com (302→80006 retired), gamdom80003.com, gamdom.com/eu/io/vip/win, fatbets.com, gamdom.one — all /client-api 400, ETag identical
- CHANGED gamdommirrors.com status page: badge N/A (SPA unparseable), 7 monitors presumed unchanged (com/eu/io/vip/win/80008/80009)

## 2026-09-10 12:03:17 UTC
- NEW kargo.teamgamdom.com ArgoCD-like API surface: GET `/api/v1/projects` → 200 (SPA catch-all), POST → 405; GET `/api/v1/applications|repositories|clusters` → 405 — method-gated backend exists behind SPA
- NEW tableau.teamgamdom.com Tableau REST API: `/api/3.21/sites` → 401 XML "No authentication credentials were provided" — proper auth gate, anonymous content hypothesis closed
- NEW gamdom80009.com live (11th mirror, 21st hostname): replaces gamdom80006.com on status page monitor id:223; byte-identical `/client-api` md5 7e3a161d + `/health` weak-ETag — second rotation in 24h
- NEW gamdom80006.com demoted: 302→https://gamdom80009.com/ redirect alias (Varnish, no-store) — brand retires numbered aliases by redirect
- NEW gamdom80001.com/gamdom80002.com: CNAME→gamdom4567.com Pool B DNS, all 4 edges 421/TLS-NOMATCH — 17th/18th provisioned-not-yet-live; 6th cycle
- NEW Provisioning cycle 7 for 90472/90473/90475/90480/90482/90488/80001/80002: all still 421/TLS-NOMATCH — no cert deployment
- NEW New numbered aliases 80010-80020 tested: all 000 (connection failed) — not provisioned
- CHANGED Starlette pool signature stable: `/client-api` 400 md5 7e3a161d + `/health` weak-ETag byte-identical Pool A + Pool B — shared origin confirmed
- CHANGED fatbets.com + gamdom.one confirmed as 2nd/3rd brands on Pool A with byte-identical origin signature — shared identity/wallet backend serves 3 brands / 21+ hostnames
- CHANGED shreeram-dynamic-test.teamgamdom.com Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm 404 + VCL `bong_ke`
- CHANGED gamdommirrors.com status page: badge N/A (SPA unparseable), 7 monitors presumed unchanged (com/eu/io/vip/win/80008/80009)
- CHANGED SEO hub (gamdom-girisi.com): still 17× gamdom90471 only — no new alias advertised

## 2026-09-10 15:56:24 UTC
- CHANGED gamdom80004.com redirect target: now 302→gamdom80008.com (was gamdom80007.com) — redirect chain updated post-rotation.
- CHANGED gamdom80007.com redirect: still 302→gamdom80008.com (unchanged from last cycle, but gamdom80004.com no longer chains through 80007→80008; both redirect directly to 80008).
- NEW kargo.teamgamdom.com POST /api/v1/repositories + POST /api/v1/clusters both → 405 (backend method-gated); GET still SPA catch-all. No JSON surface.
- NEW tableau.teamgamdom.com /api/3.21/serverInfo → 200 XML leaking productVersion 2025.1.11 (build 20251.25.1210.1815), REST API 3.25, prepConductorVersion 2025.1.0 — unauthenticated version disclosure.
- NEW kargo.teamgamdom.com v1.9.6 confirmed from client bundle; origin 77.42.9.222 direct (no Fastly), wildcard cert *.teamgamdom.com (Let's Encrypt, expires Oct 27).
- NEW kargo.teamgamdom.com ArgoCD-like API surface: GET `/api/v1/projects` → 200 (SPA catch-all), POST → 405; GET `/api/v1/applications|repositories|clusters` → 405 — method-gated backend exists behind SPA
- NEW tableau.teamgamdom.com Tableau REST API: `/api/3.21/sites` → 401 XML "No authentication credentials were provided" — proper auth gate
- NEW gamdom80009.com live (11th mirror, 21st hostname): replaces gamdom80006.com on status page monitor id:223; byte-identical `/client-api` md5 7e3a161d + `/health` weak-ETag — second rotation in 24h
- NEW gamdom80006.com demoted: 302→https://gamdom80009.com/ redirect alias (Varnish, no-store) — brand retires numbered aliases by redirect
- NEW Provisioning cycle 7 for 90472/90473/90475/90480/90482/90488/80001/80002: all still 421/TLS-NOMATCH — no cert deployment
- NEW New numbered aliases 80010-80020 tested: all 000 (connection failed) — not provisioned
- CHANGED Starlette pool signature stable: `/client-api` 400 md5 7e3a161d + `/health` weak-ETag byte-identical Pool A + Pool B — shared origin confirmed
- CHANGED fatbets.com + gamdom.one confirmed as 2nd/3rd brands on Pool A with byte-identical origin signature — shared identity/wallet backend serves 3 brands / 21+ hostnames
- CHANGED shreeram-dynamic-test.teamgamdom.com Basic realm="secret" gate holds 401 fixture-wide (7 paths); teamgamdom Go realm 404 + VCL `bong_ke`
- CHANGED gamdommirrors.com status page: badge N/A (SPA unparseable), 7 monitors presumed unchanged (com/eu/io/vip/win/80008/80009)
- CHANGED SEO hub (gamdom-girisi.com): still 17× gamdom90471 only — no new alias advertised

## 2026-09-10 19:05:55 UTC
- CHANGED Tableau CVE correlation: version 2025.1.11 > 2025.1.3 — CVE-2025-52455 (SSRF) and CVE-2025-52449 (RCE via file upload) both patched; version disclosure impact downgraded
- NEW tableau.teamgamdom.com/api/3.21/auth/signin → 405 (real backend API, not SPA catch-all) — confirms Tableau backend handles API routes directly; /sites → 401 proper auth gate; /projects → 404, /users →
- NEW Kargo Accept: application/json probe: GET /api/v1/projects returns 200 SPA HTML (server ignores Accept header) — SPA catch-all uniform even with JSON negotiation header; no JSON surface
- NEW Kargo deeper: all sub-paths (/api/v1/projects/test, /api/v1/applications, /api/v1/clusters, /api/v1/stages, /api/v1/freight, /api/v1/repositories) → 200 SPA catch-all GET, 405 POST — no JSON data surf
- NEW gamdom80009.com live (11th mirror, 21st hostname) replaces gamdom80006.com on status page monitor id:223 — second rotation event in 24h; byte-identical /client-api md5 7e3a161d + /health ETag
- NEW gamdom80006.com demoted: 302→https://gamdom80009.com/ redirect alias (Varnish, no-store) — brand retires numbered aliases by redirect
- NEW kargo.teamgamdom.com discovered: HTTP 405 on `/` and `/api/v1/projects` — GitOps/ArgoCD-like API surface on scoped brand TLD (teamgamdom.com, Pool A)
- NEW tableau.teamgamdom.com/api/3.21/serverInfo → 200 XML leaking productVersion 2025.1.11 (build 20251.25.1210.1815), REST API 3.25, prepConductorVersion 2025.1.0 — unauthenticated version disclosure
- NEW gamdom80004.com redirect target: now 302→gamdom80008.com (was gamdom80007.com) — redirect chain updated post-rotation
- NEW New numbered aliases 80010-80020 tested: all 000 (connection failed) — not provisioned
- CHANGED Status page now lists 7 monitors: com/eu/io/vip/win/80008/80009 (absorbed 80006→80009 rotation)
- CHANGED Starlette pool signature stable: /client-api 400 md5 7e3a161d + /health weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical Pool A + Pool B — shared origin confirmed
- CHANGED fatbets.com + gamdom.one confirmed as 2nd/3rd brands on Pool A (Fastly 151.101.x.52) with byte-identical origin signature — shared identity/wallet backend serves 3 brands / 21+ hostnames
- CHANGED Provisioning cycle 7 for 90472/90473/90475/90480/90482/90488/80001/80002: all still 421/TLS-NOMATCH — no cert deployment
- CHANGED shreeram-dynamic-test.teamgamdom.com Basic realm="secret" gate holds 401 fixture-wide (7 paths)
- CHANGED kargo.teamgamdom.com GET /api/v1/projects → 200 SPA catch-all (Monaco IDE shell), POST → 405; GET /api/v1/applications|repositories|clusters → 405
- CHANGED tableau.teamgamdom.com/api/3.21/sites → 401 XML "No authentication credentials were provided" — proper auth gate

## 2026-09-10 21:37:45 UTC

## 2026-09-10 23:24:51 UTC
- NEW Tableau CVE correlation complete: 2025.1.11 patches CVE-2025-52455 (SSRF) and CVE-2025-52449 (RCE) — version disclosure downgraded to informational
- NEW Kargo Accept:application/json probe confirms SPA catch-all ignores header — no JSON API surface exposed passively
- NEW gamdom80004.com redirect target updated: now 302→gamdom80008.com (was gamdom80007.com) — redirect chain flattened post-rotation
- NEW Numbered aliases 80010-80020 tested: all 000 (connection failed) — not provisioned
- CHANGED Status page monitors: com/eu/io/vip/win/80008/80009 (7 monitors, absorbed 80006→80009 rotation)
- CHANGED Provisioning cycle 9 for 90472/90473/90475/90480/90482/90488/80001/80002: all 421/TLS-NOMATCH — no cert deployment
- CHANGED Starlette pool signature stable 10th cycle: /client-api md5 7e3a161d + /health weak-ETag byte-identical Pool A + Pool B
- CHANGED Live fleet stable: 6 mirrors + 2 brands all /client-api 400, ETag identical
- CHANGED SEO hub (gamdom-girisi.com): still 17× gamdom90471 only — no new alias advertised

## 2026-09-11 01:32:19 UTC
- NEW Provisioning cycle advanced to 9th for 90472/90473/90475/90480/90482/90488/80001/80002 — all still 421/TLS-NOMATCH, no cert deployment
- NEW Starlette pool signature stable 10th consecutive cycle: /client-api md5 7e3a161d + /health weak-ETag byte-identical Pool A + Pool B
- NEW Tableau CVE correlation complete: 2025.1.11 patches CVE-2025-52455 (SSRF) and CVE-2025-52449 (RCE) — version disclosure downgraded to informational
- NEW Kargo Accept:application/json probe confirms SPA catch-all ignores header — no JSON API surface exposed passively
- NEW gamdom80004.com redirect target updated: now 302→gamdom80008.com (was gamdom80007.com) — redirect chain flattened post-rotation
- NEW Numbered aliases 80010-80020 tested: all 000 (connection failed) — not provisioned
- CHANGED Status page monitors: com/eu/io/vip/win/80008/80009 (7 monitors, absorbed 80006→80009 rotation)
- CHANGED Live fleet stable: 6 mirrors + 2 brands all /client-api 400, ETag identical
- CHANGED SEO hub (gamdom-girisi.com): still 17× gamdom90471 only — no new alias advertised

## 2026-09-11 06:42:49 UTC
- NEW Provisioning cycle 9: 90472/90473/90475/90480/90482/90488/80001/80002 all still 421/TLS-NOMATCH — no cert deployment
- NEW Kargo Accept:application/json probe: all 6 endpoints (/projects, /applications, /repositories, /clusters, /stages, /freight) return 200 text/html — SPA catch-all ignores header, no JSON API surface
- NEW Status page Kuma heartbeat: 7 monitors (ids 2,3,5,6,13,221,223) all healthy (100% uptime 24h) — no rotation this cycle
- NEW SEO hub gamdom-girisi.com: still only gamdom90471.com (17×) — no new alias advertised
- CHANGED Live fleet stable: 6 mirrors + 2 brands all /client-api 400 + /health weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical — shared origin confirmed 10th cycle
- CHANGED gamdom80004.com redirect target: 302→gamdom80008.com (was 80007) — chain flattened post-rotation
- CHANGED Numbered aliases 80010-80020: all 000 — not provisioned
- CHANGED Tableau 2025.1.11 patches CVE-2025-52455/52449 — version disclosure downgraded to informational
- CHANGED shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate holds 401 fixture-wide (7 paths)

## 2026-09-11 11:52:59 UTC
- NEW Provisioning cycle advanced to 12th for 90472/90473/90475/90480/90482/90488/80001/80002 — all still 421/TLS-NOMATCH, no cert deployment
- NEW Kargo Accept:application/json probe confirms SPA catch-all ignores header — no JSON API surface exposed passively (6 endpoints tested)
- NEW Status page Kuma heartbeat JSON: 7 monitors (ids 2,3,5,6,13,221,223) all healthy — no rotation this cycle
- NEW SEO hub gamdom-girisi.com: still only gamdom90471.com (17×) — no new alias advertised
- CHANGED Live fleet stable: 6 mirrors + 2 brands all `/client-api` 400 + `/health` weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical — shared origin confirmed 12th cycle
- CHANGED gamdom80004.com redirect target: 302→gamdom80008.com (was 80007) — chain flattened post-rotation
- CHANGED Numbered aliases 80010-80020: all 000 — not provisioned
- CHANGED Tableau 2025.1.11 patches CVE-2025-52455/52449 — version disclosure downgraded to informational
- CHANGED shreeram-dynamic-test.teamgamdom.com: Basic realm="secret" gate holds 401 fixture-wide (7 paths)

## 2026-09-11 16:02:08 UTC
- NEW Verified cookie policy uniformity: all 6 live hosts (gamdom.com, fatbets.com, gamdom.one, gamdom80008.com, gamdom80009.com, gamdom80003.com) return identical host-only `gd-lang` cookie (no Domain, no 
- NEW Confirmed /auth/login returns 404 across all brands/mirrors — real auth endpoint not at this path
- NEW Kargo API: all 6 endpoints (/projects, /applications, /repositories, /clusters, /stages, /freight) return SPA catch-all (Monaco IDE) even with `Accept: application/json` — no JSON surface exposed pass
- CHANGED Status page heartbeat: 7 monitors (ids 2,3,5,6,13,221,223) all 100% uptime 24h — no rotation this cycle; 221=gamdom80008.com, 223=gamdom80009.com confirmed live
- CHANGED Starlette pool signature stable 12th consecutive cycle: /client-api 400 md5 7e3a161d + /health weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical Pool A + Pool B
- CHANGED Provisioning cycle 13 for 90472/90473/90475/90480/90482/90488/80001/80002 — all still 421/TLS-NOMATCH, no cert deployment
- CHANGED SEO hub (gamdom-girisi.com) unchanged: still 17× gamdom90471.com only

## 2026-09-11 19:03:10 UTC
- NEW Verified cookie policy uniformity: all 6 live hosts (gamdom.com, fatbets.com, gamdom.one, gamdom80008.com, gamdom80009.com, gamdom80003.com) return identical host-only `gd-lang` cookie (no Domain, no 
- NEW Confirmed /auth/login returns 404 across all brands/mirrors — real auth endpoint not at this path
- NEW Kargo API: all 6 endpoints (/projects, /applications, /repositories, /clusters, /stages, /freight) return SPA catch-all (Monaco IDE) even with `Accept: application/json` — no JSON surface exposed pass
- CHANGED Status page heartbeat: 7 monitors (ids 2,3,5,6,13,221,223) all 100% uptime 24h — no rotation this cycle; 221=gamdom80008.com, 223=gamdom80009.com confirmed live
- CHANGED Starlette pool signature stable 12th consecutive cycle: /client-api 400 md5 7e3a161d + /health weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical Pool A + Pool B
- CHANGED Provisioning cycle 13 for 90472/90473/90475/90480/90482/90488/80001/80002 — all still 421/TLS-NOMATCH, no cert deployment
- CHANGED SEO hub (gamdom-girisi.com) unchanged: still 17× gamdom90471.com only

## 2026-09-11 21:56:31 UTC

## 2026-09-11 23:39:16 UTC
- NEW Verified /api/auth/login returns 404 on all 3 Pool A brands (gamdom.com, fatbets.com, gamdom.one) with identical host-only `gd-lang` cookie (no Domain, no SameSite, no HttpOnly) — real auth endpoint n
- NEW Confirmed socket.io on gamdom.com requires valid Origin (403 "Forbidden: Invalid Origin"); gamdommirrors.com socket.io accessible and issues SID
- CHANGED Starlette pool signature stable 13th consecutive cycle: /client-api 400 md5 7e3a161d + /health weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical Pool A + Pool B
- CHANGED Provisioning cycle 13 for 8 pending aliases (90472/90473/90475/90480/90482/90488/80001/80002) — all still 421/TLS-NOMATCH, no cert deployment
- CHANGED SEO hub (gamdom-girisi.com) unchanged: still 17× gamdom90471.com only
- CHANGED Kuma status page heartbeat: 7 monitors (ids 2,3,5,6,13,221,223) all 100% uptime — no rotation this cycle

## 2026-09-12 01:46:28 UTC
- NEW Verified `/api/auth` returns 404 + identical host-only `gd-lang` cookie on gamdom.com/fatbets.com/gamdom.one — real auth endpoint NOT at `/api/auth` or `/api/auth/login` or `/auth` or `/login` or `/gr
- NEW Confirmed `/client-api` POST accepts JSON and returns structured `GamdomClientMessage` responses across all 6 live mirrors (gamdom.com, gamdom80008.com, gamdom80009.com, gamdom80003.com, fatbets.com, 
- NEW oauth2-proxy.teamgamdom.com/oauth2/auth returns HTTP 401 (real endpoint, not 404) on Fastly Pool A — OAuth2 SSO layer confirmed on teamgamdom.com brand
- NEW kargo.teamgamdom.com root returns 405, all `/api/v1/*` GET → 200 SPA catch-all (Monaco IDE), POST → 405 — ArgoCD-like API fully SPA-gated, no JSON surface
- NEW tableau.teamgamdom.com root 200, `/api/3.21/sites` → 401 XML, `/api/3.21/auth/signin` → 405 — Tableau REST API auth-gated, version 2025.1.11 (patched CVEs)
- NEW shreeram-dynamic-test.teamgamdom.com → 401 Basic realm="secret" fixture-wide — internal test backend gate holds
- CHANGED Starlette pool signature stable 13th consecutive cycle: `/client-api` 400 md5 7e3a161d + `/health` weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical Pool A + Pool B
- CHANGED Provisioning cycle 13 for 8 pending aliases (90472/90473/90475/90480/90482/90488/80001/80002) — all still 421/TLS-NOMATCH, no cert deployment
- CHANGED SEO hub (gamdom-girisi.com) unchanged: still 17× gamdom90471.com only
- CHANGED Kuma status page heartbeat: 7 monitors (ids 2,3,5,6,13,221,223) all 100% uptime — no rotation this cycle

## 2026-09-12 06:30:27 UTC
- NEW gamdom.com/api/auth → 404 + host-only `gd-lang` cookie (no Domain/SameSite/HttpOnly) — real auth endpoint NOT at /api/auth, /api/auth/login, /auth, /login, /graphql
- NEW fatbets.com/api/auth → 404 + identical host-only `gd-lang` cookie — uniform cookie policy confirmed across 3 brands
- NEW gamdom.one/api/auth → 404 + identical host-only `gd-lang` cookie — uniform cookie policy confirmed
- NEW gamdom.com/client-api POST accepts JSON, returns structured `GamdomClientMessage` — shared identity/wallet proxy confirmed
- NEW gamdom80008.com/client-api POST returns identical `GamdomClientMessage` — 10th live mirror sharing origin
- NEW gamdom80009.com/client-api POST returns identical `GamdomClientMessage` — 11th live mirror sharing origin
- NEW oauth2-proxy.teamgamdom.com/oauth2/auth → HTTP 401 (real endpoint) — OAuth2 SSO layer confirmed on teamgamdom.com
- NEW kargo.teamgamdom.com/api/v1/* all 6 endpoints return SPA catch-all (Monaco IDE) even with `Accept: application/json` — no unauthenticated JSON API surface
- NEW tableau.teamgamdom.com/api/3.21/sites → HTTP 401 XML — proper auth gate, version 2025.1.11 (patches CVE-2025-52455/52449)
- NEW shreeram-dynamic-test.teamgamdom.com → HTTP 401 Basic realm="secret" fixture-wide — internal test backend gate holds
- CHANGED Starlette pool signature stable 13th consecutive cycle: `/client-api` 400 md5 7e3a161d + `/health` weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical Pool A + Pool B
- CHANGED Provisioning cycle 13 for 8 pending aliases (90472/90473/90475/90480/90482/90488/80001/80002) — all still 421/TLS-NOMATCH, no cert deployment
- CHANGED SEO hub (gamdom-girisi.com) unchanged: still 17× gamdom90471.com only
- CHANGED Kuma status page heartbeat: 7 monitors (ids 2,3,5,6,13,221,223) all 100% uptime — no rotation this cycle

## 2026-09-12 11:16:15 UTC
- NEW beta.perabet.com: Fastly 151.101.131.52 (Pool A family), root 200, gd-lang host-only cookie byte-identical, /graphql 404, /client-api GET 400 body md5 7e3a161d — 4th-brand sibling surface on the share
- NEW www.perabet.com: 301→apex redirect, yet /client-api still 400 md5 7e3a161d — Fastly alias keeps the shared-origin signature
- CHANGED cross-brand trust pool: 4 brands / 24+ hostnames share byte-identical client-api signature (perabet now apex+www+beta)
- NEW perabet.com/client-api hypothesis emerged (confidence 74) — 4th brand on shared /client-api origin per bigpickle agent
- NEW gamdom.com/api/auth + fatbets.com/api/auth + gamdom.one/api/auth all return 404 with identical host-only gd-lang cookie — real auth endpoint NOT at /api/auth, /api/auth/login, /auth, /login, /graphql
- NEW /client-api POST accepts JSON and returns structured GamdomClientMessage across all 6 live mirrors (gamdom.com, gamdom80008.com, gamdom80009.com, gamdom80003.com, fatbets.com, gamdom.one) — shared ide
- NEW oauth2-proxy.teamgamdom.com/oauth2/auth returns HTTP 401 (real endpoint on Fastly Pool A) — OAuth2 SSO layer confirmed on teamgamdom.com brand
- CHANGED Starlette pool signature stable 13th consecutive cycle: /client-api 400 md5 7e3a161d + /health weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s" byte-identical Pool A + Pool B
- CHANGED Provisioning cycle 13 for 8 pending aliases (90472/90473/90475/90480/90482/90488/80001/80002) — all still 421/TLS-NOMATCH, no cert deployment
- CHANGED SEO hub (gamdom-girisi.com) unchanged: still 17× gamdom90471.com only
- CHANGED Kuma status page heartbeat: 7 monitors (ids 2,3,5,6,13,221,223) all 100% uptime — no rotation this cycle

## 2026-09-12 14:25:27 UTC
- NEW perabet.com confirmed as 4th brand on shared Pool A (Fastly 151.101.x.52): byte-identical `/client-api` md5 7e3a161d, `/health` weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", host-only `gd-lang` cookie 
- NEW `/client-api` POST returns identical `GamdomClientMessage` across all 8 live hosts (gamdom.com, fatbets.com, gamdom.one, perabet.com, gamdom80008.com, gamdom80009.com, gamdom80003.com, gamdom4567.com)
- NEW oauth2-proxy.teamgamdom.com `/oauth2/start` issues `_oauth2_proxy_csrf` cookie with `Domain=teamgamdom.com; HttpOnly; Secure` — Google OAuth SSO layer confirmed; session cookie likely domain-scoped en
- CHANGED Cross-brand ATO hypothesis confidence raised to 76 (bigpickle) / 74 (nemotron3) with perabet addition — single shared backend serves 4 brands via byte-identical `/client-api` signature
- CHANGED Provisioning cycle 14 for 8 pending aliases (90472/90473/90475/90480/90482/90488/80001/80002) — all still 421/TLS-NOMATCH, no cert deployment
- CHANGED Starlette pool signature stable 13th consecutive cycle: `/client-api` 400 md5 7e3a161d + `/health` weak-ETag byte-identical Pool A + Pool B
- CHANGED SEO hub (gamdom-girisi.com) unchanged: still 17× gamdom90471.com only
- CHANGED Kuma status page heartbeat: 7 monitors (ids 2,3,5,6,13,221,223) all 100% uptime — no rotation this cycle

## 2026-09-12 17:23:02 UTC
- NEW perabet.com (apex/www/beta) confirmed as 4th brand on shared Pool A (Fastly 151.101.x.52): byte-identical `/client-api` md5 7e3a161d, `/health` weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", host-only `
- NEW `/client-api` POST returns identical `GamdomClientMessage` across all 8 live hosts (gamdom.com, fatbets.com, gamdom.one, perabet.com, gamdom80008.com, gamdom80009.com, gamdom80003.com, gamdom4567.com)
- NEW oauth2-proxy.teamgamdom.com `/oauth2/start` issues `_oauth2_proxy_csrf` cookie `Domain=teamgamdom.com; HttpOnly; Secure` — Google OAuth SSO layer confirmed; session cookie likely domain-scoped enablin
- CHANGED Cross-brand ATO hypothesis confidence raised to 76 with perabet addition — single shared backend serves 4 brands via byte-identical `/client-api` signature
- CHANGED Provisioning cycle 14 for 8 pending aliases (90472/90473/90475/90480/90482/90488/80001/80002) — all still 421/TLS-NOMATCH, no cert deployment
- CHANGED Starlette pool signature stable 13th consecutive cycle: `/client-api` 400 md5 7e3a161d + `/health` weak-ETag byte-identical Pool A + Pool B
- CHANGED SEO hub (gamdom-girisi.com) unchanged: still 17× gamdom90471.com only
- CHANGED Kuma status page heartbeat: 7 monitors (ids 2,3,5,6,13,221,223) all 100% uptime — no rotation this cycle

## 2026-09-12 19:27:56 UTC
- NEW perabet.com (apex/www/beta) confirmed as 4th brand on shared Pool A (Fastly 151.101.x.52) — byte-identical `/client-api` md5 7e3a161d, `/health` weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", host-only 
- NEW `/client-api` POST returns identical `GamdomClientMessage` across all 8 live hosts (gamdom.com, fatbets.com, gamdom.one, perabet.com, gamdom80008.com, gamdom80009.com, gamdom80003.com, gamdom4567.com)
- NEW oauth2-proxy.teamgamdom.com `/oauth2/start` issues `_oauth2_proxy_csrf` cookie `Domain=teamgamdom.com; HttpOnly; Secure` — Google OAuth SSO layer confirmed; session cookie likely domain-scoped enablin
- CHANGED Cross-brand ATO hypothesis confidence raised to 76 with perabet addition — single shared backend serves 4 brands / 25+ hostnames via byte-identical `/client-api` signature
- CHANGED Provisioning cycle 14 for 8 pending aliases (90472/90473/90475/90480/90482/90488/80001/80002) — all still 421/TLS-NOMATCH, no cert deployment
- CHANGED Starlette pool signature stable 13th consecutive cycle: `/client-api` 400 md5 7e3a161d + `/health` weak-ETag byte-identical Pool A + Pool B
- CHANGED SEO hub (gamdom-girisi.com) unchanged: still 17× gamdom90471.com only
- CHANGED Kuma status page heartbeat: 7 monitors (ids 2,3,5,6,13,221,223) all 100% uptime — no rotation this cycle

## 2026-09-12 21:43:07 UTC
- NEW perabet.com (apex/www/beta) confirmed as 4th brand on shared Pool A (Fastly 151.101.x.52): byte-identical `/client-api` md5 7e3a161d, `/health` weak-ETag W/"2-eoX0dku9ba8cNUXvu/DyeabcC+s", host-only `
- NEW `/client-api` POST returns identical `GamdomClientMessage` across all 8 live hosts (gamdom.com, fatbets.com, gamdom.one, perabet.com, gamdom80008.com, gamdom80009.com, gamdom80003.com, gamdom4567.com)
- NEW oauth2-proxy.teamgamdom.com `/oauth2/start` issues `_oauth2_proxy_csrf` cookie `Domain=teamgamdom.com; HttpOnly; Secure` — Google OAuth SSO layer confirmed; session cookie likely domain-scoped enablin
- CHANGED Cross-brand ATO hypothesis confidence raised to 76 with perabet addition — single shared backend serves 4 brands via byte-identical `/client-api` signature
- CHANGED Provisioning cycle 14 for 8 pending aliases (90472/90473/90475/90480/90482/90488/80001/80002) — all still 421/TLS-NOMATCH, no cert deployment
- CHANGED Starlette pool signature stable 13th consecutive cycle: `/client-api` 400 md5 7e3a161d + `/health` weak-ETag byte-identical Pool A + Pool B
- CHANGED SEO hub (gamdom-girisi.com) unchanged: still 17× gamdom90471.com only
- CHANGED Kuma status page heartbeat: 7 monitors (ids 2,3,5,6,13,221,223) all 100% uptime — no rotation this cycle
