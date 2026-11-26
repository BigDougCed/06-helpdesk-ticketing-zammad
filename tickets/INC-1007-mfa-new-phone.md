# INC-1007 – New phone, Authenticator gone

| Field | Value |
|---|---|
| Opened | 2026-11-10 07:55 via phone |
| Requester | Thomas Becker, IT (yes, IT has tickets too) |
| Category | Identity › MFA |
| Priority | **P3**: single user; urgent for him, but webmail on his old phone still works |
| SLA | ✅ response 07:58, resolved 08:20 |
| Group / agent | 2nd-Level M365 & Endpoint / Cedric |
| Time spent | 20 min |

## Symptom
> "I set up my new iPhone over the weekend. The Authenticator app is empty and I can't approve sign-ins anymore."

## Triage
Admin account affected → **identity verification is mandatory**, even though I know him: MFA resets are a favourite social-engineering target.

## Troubleshooting / steps
1. **Verified identity**: called him back on the number in the HR system, and he confirmed a detail only he knows (asset tag of his laptop).
2. Entra admin center › *Users › Thomas Becker › Authentication methods*: old *Microsoft Authenticator (iPhone 12)* still registered, no other method.
3. Deleted the old Authenticator registration and clicked *Revoke multifactor authentication sessions*.
4. Created a **Temporary Access Pass**: one-time use, valid 60 min. Told him the code by phone (not by e-mail or chat).
5. He signed in at **aka.ms/mysecurityinfo** with the TAP → added Authenticator on the new iPhone → test sign-in with number matching ✅
6. Advised him to also register a **FIDO2 key / passkey** as a second method (admins: CA003 requires phishing-resistant MFA).

## Root cause
Authenticator accounts are bound to the device; the iCloud restore didn't move the MFA keys. Expected behaviour, not a fault.

## Resolution
Old method removed, TAP issued, new device registered.

## Customer communication
> Hi Thomas, your new iPhone is registered for MFA. The old registration is deleted, so the old phone can't approve sign-ins anymore. Please add a passkey/FIDO2 key as a backup method this week, so a lost phone doesn't lock you out.

## Prevention / follow-up
- KB article [KB-DE-04 Neues Handy: Authenticator umziehen](https://github.com/BigDougCed/07-it-knowledge-base/blob/main/de/KB-DE-04-authenticator-neues-handy.md): explains that users should move the app *before* wiping the old phone, or enable cloud backup in Authenticator.
- Every user should have **two** MFA methods.

## Lab reproduction
Test user with Authenticator on an Android emulator; deleted the app → user can't sign in. Followed the steps above with a TAP.
