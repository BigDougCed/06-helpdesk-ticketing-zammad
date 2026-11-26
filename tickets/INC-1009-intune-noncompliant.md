# INC-1009 – "Device not compliant", no Outlook

| Field | Value |
|---|---|
| Opened | 2026-11-12 08:05 via phone |
| Requester | Kwame Mensah, Accounting |
| Category | Endpoint › Intune |
| Priority | **P3**: single user, webmail from another device works |
| SLA | ✅ response 08:09, resolved 09:30 |
| Group / agent | 2nd-Level M365 & Endpoint / Cedric |
| Time spent | 30 min (+ waiting for encryption) |

## Symptom
> „Outlook und Teams sagen: ‚Sie können von hier aus nicht darauf zugreifen. Ihr Gerät erfüllt nicht die Anforderungen.' Gestern ging noch alles."

## Triage
Error text = Conditional Access **CA005 (require compliant device)**. Single user → P3.

## Troubleshooting
1. Intune › *Devices › NL-5CD31… › Device compliance* → `WIN-Compliance-Baseline`: **Require BitLocker: Not compliant**, everything else compliant.
2. Asked why: he got a **replacement SSD** from the vendor's warranty service yesterday, so the drive is new and unencrypted.
3. On the device: `manage-bde -status C:` → *Protection Off, Fully Decrypted*. BitLocker policy was pending because the device had not rebooted since.
4. Company Portal › *Check status* → sync; restarted → BitLocker started silently, recovery key escrowed to Entra (checked: new key ID under the device).
5. After encryption (~40 min), compliance re-evaluated → **Compliant** → Outlook and Teams work. ✅

## Root cause
New unencrypted disk after a hardware repair → compliance failed → Conditional Access blocked M365 access, as designed.

## Resolution
Triggered sync + reboot so the BitLocker policy applied; waited for compliance re-evaluation.

## Customer communication
> Guten Tag Herr Mensah, nach dem Festplattentausch war Ihr Laptop noch nicht verschlüsselt. Das ist eine Sicherheitsvorgabe für den Zugriff auf Firmendaten. Die Verschlüsselung ist jetzt aktiv und Outlook funktioniert wieder. Bei künftigen Reparaturen melden Sie sich bitte kurz vorher bei uns, dann bereiten wir das vor.

## Prevention / follow-up
- Hardware-repair checklist: after a disk replacement → reset/Autopilot or force BitLocker + sync before handing back
- Compliance grace period (1 day) is working as intended; documented in [project 05](https://github.com/BigDougCed/05-intune-endpoint-management)

## Lab reproduction
On the enrolled VM: `Disable-BitLocker C:` (as admin) → after the next check-in the device became non-compliant → Outlook on the web from that device was blocked by CA005 (in On mode for the test user). Re-enabled via policy + sync.
