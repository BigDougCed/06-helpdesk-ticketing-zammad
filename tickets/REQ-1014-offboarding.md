# REQ-1014 – Leaver: René Lefèvre (Warehouse)

| Field | Value |
|---|---|
| Opened | 2026-11-19 10:00 via HR web form |
| Requester | HR, approved by Marek Nowak (Warehouse lead) |
| Category | Request › Leaver |
| Priority | **P4**: planned; last working day 2026-11-20, account off at 17:00 |
| SLA | ✅ done 2026-11-20 17:05 |
| Group / agent | Requests / Cedric |
| Time spent | 35 min |

## Request
> Austritt René Lefèvre, letzter Arbeitstag 20.11.2026. Postfach bitte an Marek Nowak übergeben. Laptop und Handscanner werden am letzten Tag abgegeben.

## Checklist (executed 2026-11-20, 17:00)
| # | Task | How | Done |
|---|---|---|---|
| 1 | Written HR confirmation + manager approval | Attached to ticket | ✅ |
| 2 | AD: disable, scramble password, group backup, remove groups, move to `OU=Disabled` | `Invoke-UserOffboarding.ps1 -SamAccountName rene.lefevre -TicketNumber REQ-1014` | ✅ |
| 3 | M365: block sign-in, revoke sessions, mailbox → shared + FullAccess Marek, DE/EN auto-reply, remove groups/licence | `Invoke-M365Offboarding.ps1` ([project 04](https://github.com/BigDougCed/04-m365-entra-id)) | ✅ |
| 4 | OneDrive: access for the manager (retention 30 days) | M365 admin › user › OneDrive › *Create link to files* | ✅ |
| 5 | Laptop returned → Intune **Wipe**, then Autopilot re-assignment | Intune › device › Wipe | ✅ |
| 6 | Hand scanner + badge returned | Asset list updated | ✅ |
| 7 | Shared passwords he knew (warehouse Wi-Fi PSK, scanner admin PIN) | Changed; documented in the password manager | ✅ |
| 8 | Delete account after 30 days | Follow-up ticket REQ-1020 scheduled for 2026-12-21 | ✅ |

## Customer communication
> Guten Tag Herr Nowak, der Austritt von Herrn Lefèvre ist abgeschlossen. Sein Postfach erscheint ab morgen automatisch in Ihrem Outlook. Kunden erhalten eine Abwesenheitsnachricht mit Ihren Kontaktdaten. Auf seine OneDrive-Dateien können Sie 30 Tage lang zugreifen (Link per separater E-Mail). Bitte verschieben Sie benötigte Dateien bis zum 21.12. in die Teams-Ablage „Lager".

## Lessons learned
- Timing: accounts are disabled **at the end** of the last day, not in the morning (he still had to hand over his work).
- Shared secrets (Wi-Fi PSK, device PINs) are easily forgotten → now a fixed checklist item.

## Lab reproduction
Ran both offboarding scripts against the lab user `rene.lefevre` with `-WhatIf` first, then for real; wiped the Intune test VM.
