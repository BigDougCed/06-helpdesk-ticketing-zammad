# INC-1001 – Account locked every morning

| Field | Value |
|---|---|
| Opened | 2026-11-02 07:42 via phone |
| Requester | Jürgen Müller, Sales |
| Category | Identity › Lockout |
| Priority | **P3**: single user, workaround (unlock) possible |
| SLA | Response 4 h / resolution 2 BD: ✅ met (responded 07:45, resolved 09:10) |
| Group / agent | 1st-Level Service Desk / Cedric |
| Time spent | 35 min |

## Symptom
> „Ich bin schon wieder gesperrt! Das passiert jetzt den dritten Morgen in Folge, seit ich mein Passwort geändert habe."

## Triage
Single user, can't work, but an unlock helps immediately → P3. Pattern "since the password change" → old password cached somewhere.

## Troubleshooting
1. Verified identity (callback on the desk phone number from the directory).
2. `.\Reset-HelpdeskPassword.ps1 -SamAccountName juergen.mueller -UnlockOnly` → `LockedOut: True`, `BadLogonCount: 5`, last bad password at 07:31.
3. Looked for the **lockout source** on the PDC emulator (DC01): Security log, **event ID 4740**:
   ```powershell
   Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4740} -MaxEvents 20 |
     Where-Object { $_.Properties[0].Value -eq 'juergen.mueller' } |
     Select-Object TimeCreated, @{n='Source';e={$_.Properties[1].Value}}
   ```
   → *Caller Computer Name*: **CL01**, the shared PC at the Sales counter, not his laptop.
4. On CL01: *Credential Manager* had a saved Windows credential `\\DC01\Sales` with his **old** password (he once mapped the share there with "remember credentials"). Every morning a scheduled sync on that PC retried it 5× → lockout.

## Root cause
Stale saved credential on another computer kept authenticating with the old password.

## Resolution
- Removed the credential on CL01 (`cmdkey /list`, `cmdkey /delete:DC01`), unlocked the account.
- Next morning checked again: no new 4740 events. ✅

## Customer communication
> Guten Tag Herr Müller, die Ursache war ein alter, gespeicherter Zugang mit Ihrem früheren Kennwort am Thekenrechner im Vertrieb. Dieser hat jeden Morgen automatisch versucht, sich anzumelden, und Ihr Konto dadurch gesperrt. Wir haben den Eintrag entfernt. Sie müssen nichts weiter tun. Viele Grüße, IT-Service-Desk

## Prevention / follow-up
- KB article [KB-EN-01 Finding the source of an account lockout](https://github.com/BigDougCed/07-it-knowledge-base/blob/main/en/KB-EN-01-account-lockout-source.md)
- Shared PCs: GPO "Network access: Do not allow storage of passwords and credentials for network authentication" → change request CHG-0007

## Lab reproduction
Mapped `\\DC01\Sales` on CL01 as `juergen.mueller` with "remember credentials", changed his password on CL02, then ran `Get-ChildItem \\DC01\Sales` 5× on CL01 → account locked, event 4740 with source CL01.
