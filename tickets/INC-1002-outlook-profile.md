# INC-1002 – Outlook crashes on start

| Field | Value |
|---|---|
| Opened | 2026-11-03 08:15 via e-mail (from phone) |
| Requester | Sabine Weiß, Accounting |
| Category | Client › Outlook |
| Priority | **P3**: single user, Outlook on the web works as a workaround |
| SLA | ✅ response 08:22, resolved 09:05 |
| Group / agent | 1st-Level Service Desk / Cedric |
| Time spent | 40 min |

## Symptom
> „Outlook öffnet sich kurz und schließt sich sofort wieder. Ich brauche dringend meine E-Mails für den Monatsabschluss."

## Triage
Gave her the workaround first: **outlook.office.com** in the browser, so she could keep working right away. Then remote session via Quick Assist.

## Troubleshooting
1. `outlook.exe /safe` → **starts** in safe mode → suspicion: add-in or profile.
2. *File › Options › Add-ins › COM add-ins*: disabled the PDF add-in installed last week → normal start still crashes → not the add-in.
3. Event Viewer › Application: event 1000, `OUTLOOK.EXE`, faulting module `mso*.dll`, during profile load.
4. Created a **new profile**: *Control Panel › Mail (Microsoft Outlook) › Show Profiles › Add* → "Sabine-neu", set as default.
5. Outlook starts, Autodiscover configured Exchange Online automatically, OST synced in ~10 min (Cached Exchange Mode, 12 months).

## Root cause
Corrupted Outlook profile / OST after a forced shutdown (she held the power button on Friday when the laptop froze).

## Resolution
New Outlook profile; old profile deleted after she confirmed that all mail, calendar and shared mailbox `buchhaltung@` were back. Signatures copied from `%APPDATA%\Microsoft\Signatures`.

## Customer communication
> Guten Tag Frau Weiß, Ihr Outlook-Profil war beschädigt, vermutlich durch das erzwungene Ausschalten am Freitag. Wir haben ein neues Profil eingerichtet. Ihre E-Mails liegen sicher in der Cloud und sind vollständig wieder da. Ihre Signatur haben wir übernommen. Tipp: Bitte den Laptop nach Möglichkeit über *Start › Herunterfahren* ausschalten.

## Prevention / follow-up
- KB article [KB-DE-02 Outlook startet nicht](https://github.com/BigDougCed/07-it-knowledge-base/blob/main/de/KB-DE-02-outlook-startet-nicht.md)

## Lab reproduction
On CL01 with an M365 user: closed Outlook during the first OST sync by killing the process (`taskkill /im outlook.exe /f`) several times, then renamed/corrupted the `.ost` in `%LOCALAPPDATA%\Microsoft\Outlook`. Outlook failed to load the profile; a new profile fixed it.
