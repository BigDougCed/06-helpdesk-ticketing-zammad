# 2 – Zammad configuration

## Groups and roles
| Group | Who | Handles |
|---|---|---|
| `1st-Level Service Desk` | All agents | Every new ticket lands here: intake, triage, standard fixes |
| `2nd-Level Systems` | Server/AD admins | AD, servers, network, printing infrastructure |
| `2nd-Level M365 & Endpoint` | Cloud/endpoint admins | Entra ID, Exchange Online, Intune |
| `Requests` | 1st level | Onboarding, offboarding, software and hardware requests |

Roles: `Agent` (1st level: full on 1st-Level + Requests, read on 2nd-level groups), `Agent 2nd Level`, `Admin`.

## Ticket attributes (Admin › Objects › Ticket)
| Attribute | Type | Values |
|---|---|---|
| `Category` | Tree select | Identity › Lockout / Password / MFA · Client › Windows / Outlook / Teams · Network · Printing · Endpoint › Intune / BitLocker · Security › Phishing · Request › Joiner / Leaver / Software · Meeting room |
| `Priority level` | Select | P1 Critical · P2 High · P3 Normal · P4 Low (used by SLAs; Zammad's built-in priority 1–3 is kept in sync with a trigger) |
| `Impact` | Select | Company · Department · Single user |

## SLAs (Admin › SLAs)
One SLA per priority level, calendar "NordLicht business hours" (Mon–Fri 07–17, Thuringia holidays):

| SLA | Condition | First response | Update | Solution |
|---|---|---|---|---|
| `SLA-P1` | Priority level = P1 | 15 min | 30 min | 4 h |
| `SLA-P2` | Priority level = P2 | 1 h | 2 h | 8 h |
| `SLA-P3` | Priority level = P3 | 4 h | 8 h | 20 h (2 business days) |
| `SLA-P4` | Priority level = P4 | 8 h | – | 50 h (5 business days) |

## Triggers
| Trigger | Condition | Action |
|---|---|---|
| Auto-reply on new ticket | New ticket via e-mail/web | E-mail to customer (template "First response", DE/EN) with ticket number |
| Phishing fast lane | Subject or body contains "phishing", "verdächtig", "Link geklickt" | Priority level P2, Category Security › Phishing, notify 2nd-Level Systems |
| P1 alert | Priority level changes to P1 | Notify all agents + IT lead; tag `major-incident` |
| Sync built-in priority | Priority level P1/P2 → priority 3 high; P3 → 2 normal; P4 → 1 low | Set priority |

## Escalations / SLA alerts
Admin › *Time accounting* on (minutes per ticket). *Escalation* notifications to the group owner at 80 % of the SLA time.

## Macros
| Macro | Does |
|---|---|
| `Waiting for customer` | State *pending reminder* +2 business days, text "Ask for information" |
| `Resolved – waiting for confirmation` | Internal note "Resolution documented", customer text "Solved", state *pending close* +3 days |
| `Escalate to 2nd level` | Group change, internal note template (what was checked, logs, screenshots), owner empty |
| `Password reset done` | Category Identity › Password, customer text (no password in the e-mail!), close |

## Text modules (`::` shortcuts)
`::hallo`, `::rueckfrage`, `::geloest`, `::fernwartung`, `::passwort`; contents in [templates/communication-de-en.md](../templates/communication-de-en.md).

📸 `screenshots/groups.png`, `screenshots/sla.png`, `screenshots/triggers.png`, `screenshots/macro.png`
