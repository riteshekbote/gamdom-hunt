# Validated findings (running count 0)

- 1 lead(s) marked VALID at 2026-09-05 21:49:56 UTC
  - | Q5 Novel/unreported? | Likely yes (novel architecture, no prior report in valid-bugs.md) |

- 1 lead(s) marked VALID at 2026-09-10 06:37:07 UTC
  - I'll deliver verdicts with one-line reasons, and for VALID leads: minimal read-only proof steps, impact assessment, CVSS 3.1 score, and reporting channel.

- 6 lead(s) marked VALID at 2026-09-12 13:18:14 UTC
  - **VERDICT: VALID**
  - **VERDICT: VALID**
  - **VERDICT: VALID**
  - | 1 | oauth2-proxy.teamgamdom.com Google OAuth client_id leak | **VALID** | 5.4 Med | Exposed OAuth flow under trusted origin |
  - | 2 | tableau-admin.teamgamdom.com admin panel | **VALID** | 5.3 Med | Admin analytics panel existence + auth gate |
  - | 3 | kargo.teamgamdom.com GitOps API | **VALID** | 8.1 High | Public deployment pipeline API (405 confirms surface) |

- 3 lead(s) marked VALID at 2026-09-14 20:45:01 UTC
  - [ ] Output triage verdict with proof steps, impact, CVSS, reporting channel for VALID leads
  - [✓] Output triage verdict with proof steps, impact, CVSS, reporting channel for VALID leads
  - | Q4 Provable | NO — requires authenticated POST with valid session to test action routing against upstream wallet/auth. Bundle action strings are obfuscated, no endpoint map extractable passively. |

- 6 lead(s) marked VALID at 2026-09-16 01:00:12 UTC
  - | **Q6 Not always-rejected** | **Yes** — cross-domain ATO is a valid class; not on always-rejected list |
  - | **Q6 Not always-rejected** | **Yes** — misconfig of monitoring infra is valid, but mutation against 3rd-party SaaS is REJECTED |
  - | **Q6 Not always-rejected** | **Yes** — subdomain takeover is valid, but currently not exploitable |
  - | **Q2 Reachable** | **Partially** — endpoint exists and returns 400 on GET, but POST requires valid session |
  - | **Q4 Provable** | **No** — requires POST with valid session to test; blind POST to live identity/wallet proxy is REJECTED class |
  - | **Q6 Not always-rejected** | **Yes** — SSRF/mass-assignment is valid |

- 3 lead(s) marked VALID at 2026-09-16 05:33:36 UTC
  - | Q5 Novel | UNCERTAIN | Need to verify: (a) is token still valid? (b) was it reported? |
  - **Verdict: HOLD** — Requires verification: (1) Is the token active/valid? (2) Does it grant real API access? (3) Has it been previously reported? If valid and novel → VALID.
  - **Verdict: HOLD** — Requires verification: (1) Is the token valid/active? (2) What access does it grant? (3) Was it previously reported? If valid and novel → VALID.

- 15 lead(s) marked VALID at 2026-09-18 07:38:55 UTC
  - | Q2 Reachable? | YES — /client-api publicly reachable, requires valid session cookie to exploit |
  - | Q6 Not always-rejected? | YES — cross-domain session replay is valid class |
  - **Verdict: HOLD** — Needs authenticated cookie-replay test (gamdom80006→gamdom80007/client-api). If cross-host acceptance confirmed → VALID CVSS 8.1 High. Report via bugs.olivermaicher.eu.
  - | Q5 Novel? | LIKELY YES — not previously reported in valid-bugs.md for kargo specifically |
  - | Q6 Not always-rejected? | YES — internal API surface on scoped brand domain is valid |
  - | Q7 Reasonable triager accept? | BORDERLINE — 405 alone is weak; needs GET/POST with valid auth to confirm data exposure |
  - **Verdict: HOLD** — 405 confirms surface but no data leak passively. Needs auth probe to confirm actual data exposure. If authenticated endpoint leaks deployment data → VALID CVSS 5.3 Medium. Report v
  - | Q5 Novel? | UNCERTAIN — previously marked VALID in valid-bugs.md (2026-09-12) |
  - | Q6 Not always-rejected? | YES — OAuth misconfig is valid |
  - | Q7 Reasonable triager accept? | YES — confirmed valid previously |
  - | Q5 Novel? | UNCERTAIN — previously marked VALID in valid-bugs.md (2026-09-12) |
  - | Q5 Novel? | LIKELY YES — not previously triaged in valid-bugs.md |
  - | Q6 Not always-rejected? | YES — internal service exposure is valid |
  - **Verdict: VALID**
  - | 5 | Internal monitoring stack (grafana/prometheus/vault/clickhouse) | **VALID** | 7.5 | Report now |
