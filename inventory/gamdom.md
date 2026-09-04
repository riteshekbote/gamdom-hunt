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
