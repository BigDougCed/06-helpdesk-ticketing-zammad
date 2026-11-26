# REQ-1006 – New hire: Leyla Yılmaz (Sales)

| Field | Value |
|---|---|
| Opened | 2026-11-02 11:00 via HR web form |
| Requester | HR (on behalf of Anna Schmidt, Sales lead) |
| Category | Request › Joiner |
| Priority | **P4**: planned, start date 2026-11-09 |
| SLA | ✅ resolution 5 BD: ready on 2026-11-06 |
| Group / agent | Requests / Cedric |
| Time spent | 50 min |

## Request
> Neue Mitarbeiterin: Leyla Yılmaz, Vertriebsassistentin, Start 09.11.2026, Vorgesetzte: Anna Schmidt. Benötigt: Laptop, E-Mail, Zugriff Vertriebslaufwerk, Teams-Kanal Vertrieb.

## Checklist
| # | Task | How | Done |
|---|---|---|---|
| 1 | Manager approval for access | Reply from Anna Schmidt in ticket | ✅ |
| 2 | AD account + group `GG_Sales` | `New-UserOnboarding.ps1` ([project 03](https://github.com/BigDougCed/03-powershell-ad-automation)) → user `leyla.yilmaz` | ✅ (group step failed, see INC-1005) |
| 3 | M365 account + licence | `New-M365User.ps1` / Entra Connect sync → `LIC_M365_BusinessPremium` | ✅ |
| 4 | Laptop | Autopilot device `NL-5CD3…` assigned to her ([project 05](https://github.com/BigDougCed/05-intune-endpoint-management)), ESP completed in test | ✅ |
| 5 | Teams | Added to team *Vertrieb* | ✅ |
| 6 | Shared mailbox `info@` | FullAccess + SendAs | ✅ |
| 7 | Credentials | Temporary password given **by phone** to Anna Schmidt; MFA registration on day 1 with a Temporary Access Pass | ✅ |
| 8 | Welcome sheet | KB-DE-01 "Erste Schritte" printed and placed on the laptop | ✅ |

## Customer communication
> Guten Tag Frau Schmidt, der Arbeitsplatz für Frau Yılmaz ist vorbereitet: Benutzerkonto, E-Mail, Teams-Kanal Vertrieb und Zugriff auf das Vertriebslaufwerk. Das vorläufige Kennwort habe ich Ihnen telefonisch mitgeteilt. Am ersten Tag richtet Frau Yılmaz bei der Anmeldung die Microsoft-Authenticator-App ein, gerne mit unserer Unterstützung (Durchwahl -200).

## Lessons learned
The automatic group assignment failed silently from the user's point of view (see INC-1005). A **day-1 check** ("can the user open S:, Outlook, Teams?") is now part of the checklist.

## Lab reproduction
Ran the full onboarding flow for this user in the lab: CSV → script → M365 → Autopilot VM reset → first sign-in.
