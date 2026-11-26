# INC-1005 – Drive S: missing for new colleague

| Field | Value |
|---|---|
| Opened | 2026-11-09 08:30 via e-mail |
| Requester | Anna Schmidt, Sales lead (for new hire Leyla Yılmaz) |
| Category | Access › File share |
| Priority | **P3**: single user, can get files from colleagues meanwhile |
| SLA | ✅ response 08:41, resolved 09:15 |
| Group / agent | 1st-Level Service Desk / Cedric |
| Time spent | 20 min |

## Symptom
> „Frau Yılmaz hat heute ihren ersten Tag, aber sie sieht das Vertriebslaufwerk S: nicht. Alle anderen im Team haben es."

## Triage
Single user, new account → likely group membership or a GPO/logon issue.

## Troubleshooting
1. `Get-ADPrincipalGroupMembership leyla.yilmaz | Select Name` → only `Domain Users`. **Not in `GG_Sales`.**
2. Onboarding log (`logs/2026-11-06.log`) showed: `FAILED ... Add-ADGroupMember: The specified group does not exist` → in the CSV the department was typed `Vertrieb` instead of `Sales` → user was created in the right OU manually by a colleague, but the group step was skipped.
3. Added her: `Add-ADGroupMember GG_Sales -Members leyla.yilmaz`.
4. On her PC: `gpupdate /force` → S: still missing. Reason: **group memberships are part of the Kerberos ticket, which is only created at logon**. `whoami /groups` didn't list `GG_Sales` yet.
5. Sign out / sign in → `whoami /groups` shows `GG_Sales`, S: mapped. ✅

## Root cause
Wrong department value in the HR CSV → onboarding skipped the group membership. No AGDLP chain → no drive mapping (item-level targeting) and no NTFS access.

## Resolution
Added to `GG_Sales`; user signed out and in again.

## Customer communication
> Guten Tag Frau Schmidt, Frau Yılmaz fehlte die Berechtigung für das Vertriebslaufwerk. Wir haben sie ergänzt. Nach einer Ab- und Anmeldung ist Laufwerk S: jetzt verfügbar. Herzlich willkommen an Frau Yılmaz!

## Prevention / follow-up
- Improvement for `New-UserOnboarding.ps1`: validate the `Department` column against the list of OUs **before** creating anything → **implemented** as `Test-NLNewHireRow` in [project 03](https://github.com/BigDougCed/03-powershell-ad-automation) (the whole CSV is checked first; nothing is created if one row is wrong).
- KB: "Why do I have to sign out after getting new permissions?"

## Lab reproduction
Created a test user without `GG_Sales`, logged in on CL01 → no S:. Added the group, `gpupdate` only → still no S:. Sign-out/in → S: appears.
