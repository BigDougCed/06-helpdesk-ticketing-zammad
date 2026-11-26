# INC-1008 – Phishing e-mail, link clicked

| Field | Value |
|---|---|
| Opened | 2026-11-11 09:12 via phone |
| Requester | Sophie Krüger, Warehouse |
| Category | Security › Phishing |
| Priority | **P2**: security incident, a password may be compromised |
| SLA | Response 1 h / resolution 8 h: ✅ response 09:13, contained 09:35, closed 11:30 |
| Group / agent | 1st-Level (intake) → 2nd-Level M365 & Endpoint / Cedric |
| Time spent | 75 min |

## Symptom
> „Ich habe eine Mail von ‚DHL' bekommen, dass ein Paket nicht zugestellt werden kann. Ich habe auf den Link geklickt und mein Passwort eingegeben. Danach kam nur eine weiße Seite. War das schlimm?"

## Triage
Credentials entered on a fake page = **assume compromise**. Thanked her for calling immediately (people who report quickly must never be blamed).

## Containment (first 20 minutes)
1. **Reset password** + `Revoke-MgUserSignInSession` (kills all existing tokens).
2. Checked **sign-in logs** (last 24 h): one sign-in attempt from an IP in another country at 09:06 → **blocked by CA004** (outside EU) and would also have needed MFA (CA001). ✅ No successful foreign sign-in.
3. Checked her mailbox for attacker persistence: **inbox rules** (`Get-InboxRule -Mailbox sophie.krueger`), forwarding (`Get-Mailbox | fl ForwardingSmtpAddress`), new MFA methods → nothing suspicious.

## Investigation & cleanup
4. **Message trace**: the same e-mail went to 14 NordLicht users.
5. Microsoft Defender › *Explorer* (Threat Explorer; or Content Search + `New-ComplianceSearchAction -Purge` without Defender P2) → **soft-deleted** the message from all 14 mailboxes.
6. Blocked the sender domain and the URL (*Tenant Allow/Block List*), submitted the mail to Microsoft as phishing.
7. Asked the other 13 recipients by short e-mail: "Did you click?" → nobody else did.

## Root cause
Convincing parcel-delivery phishing; the user entered her password. **MFA + Conditional Access prevented the account takeover.**

## Customer communication
> Guten Tag Frau Krüger, vielen Dank, dass Sie sich sofort gemeldet haben. Genau richtig! Ihr Kennwort ist zurückgesetzt, und wir haben geprüft: Der Angreifer konnte sich nicht anmelden, die Mehrfaktor-Anmeldung hat ihn gestoppt. Die E-Mail haben wir bei allen Kolleginnen und Kollegen entfernt. Erkennungsmerkmal für das nächste Mal: Der Link führte nicht zu dhl.de, sondern zu einer fremden Adresse. Mit der Maus darüberfahren zeigt das Ziel.

## Prevention / follow-up
- Short phishing awareness reminder to all staff (with this example, anonymised)
- "Report phishing" button in Outlook for all users
- Problem ticket: why did the mail pass the filter? → stricter anti-phishing policy (impersonation protection)
- KB article [KB-DE-05 Verdächtige E-Mail: was tun?](https://github.com/BigDougCed/07-it-knowledge-base/blob/main/de/KB-DE-05-verdaechtige-email.md)

## Lab reproduction
Sent a harmless "fake DHL" test e-mail from an external address to two lab users; practised message trace, inbox-rule check, session revocation and purge on it. Simulated the foreign sign-in with a VPN connection to a non-EU location → blocked by CA004 in the sign-in log.
