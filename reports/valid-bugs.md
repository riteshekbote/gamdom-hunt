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
