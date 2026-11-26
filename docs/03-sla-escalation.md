# 3 – Prioritisation & escalation

## Impact × urgency matrix
| | **Urgency high** (work stopped, no workaround, deadline) | **Urgency medium** (workaround possible) | **Urgency low** (question, planned) |
|---|---|---|---|
| **Impact: company / critical service** | P1 | P2 | P3 |
| **Impact: department / VIP / security** | P2 | P2 | P3 |
| **Impact: single user** | P2 | P3 | P4 |

Security incidents (phishing clicked, suspicious sign-in, lost device) are **at least P2**, whatever the number of users.

## Escalation path
```
User ──► 1st Level (intake, triage, standard fixes, max. 30 min before escalating)
              │ not solvable / needs admin rights / P1
              ▼
         2nd Level (Systems | M365 & Endpoint)
              │ vendor bug / hardware warranty / provider outage
              ▼
         3rd Level / vendor (Microsoft support, printer service partner, ISP)
```

**Functional escalation** = to the team with the right skills (above).
**Hierarchical escalation** = to the IT lead when an SLA is about to be breached, a P1 starts, or a customer complains.

## What a good escalation contains
1. Clear symptom + since when + how many users
2. What was already checked (with results), not just "doesn't work"
3. Logs / screenshots / error codes attached
4. Business impact and any deadline
5. How to reach the user

## Major incident (P1)
- One **incident manager** (coordinates, communicates) and separate **technical leads** (fix)
- Status updates to all staff every 30 minutes, even if nothing changed
- After resolution: post-incident review within 5 business days → problem ticket for the root cause
- Full worked example: [project 10 – Major incident capstone](https://github.com/BigDougCed/10-major-incident-capstone)
