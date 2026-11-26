# INC-1013 – BitLocker recovery screen

| Field | Value |
|---|---|
| Opened | 2026-11-18 07:35 via phone |
| Requester | Katrin Hoffmann, Managing Director |
| Category | Endpoint › BitLocker |
| Priority | **P2**: VIP, laptop unusable, board meeting at 09:00 |
| SLA | ✅ response 07:36, resolved 07:50 |
| Group / agent | 1st-Level Service Desk / Cedric |
| Time spent | 15 min |

## Symptom
> „Mein Laptop zeigt einen blauen Bildschirm und will einen BitLocker-Wiederherstellungsschlüssel. Was ist das? Ich habe in einer Stunde eine wichtige Sitzung."

## Triage
VIP + time pressure → P2. Calm explanation: the data is safe, this is a security feature, it takes 5 minutes.

## Steps
1. **Identity check** even for the managing director (a recovery key unlocks the whole disk): callback on her mobile number from the HR system.
2. She read me the **Key ID** (first 8 characters) shown on the recovery screen.
3. Intune › *Devices › NL-5CD2… › Recovery keys* (or Entra › Devices › BitLocker keys) → matched the Key ID → read her the **48-digit recovery key** in blocks of 6 digits.
4. Laptop booted normally. ✅
5. Asked what happened before: she accepted a **BIOS/firmware update** from the vendor's update tool yesterday evening → TPM measurements changed → BitLocker asked for recovery. Expected behaviour.

## Follow-up on the device (same day, remote)
- `manage-bde -protectors -get C:` → protectors OK
- Because the recovery key was disclosed by phone, **rotated the key**: Intune › device › *BitLocker key rotation* (new key escrowed automatically)

## Customer communication
> Guten Tag Frau Hoffmann, die Abfrage kam durch das BIOS-Update von gestern Abend. Das ist eine normale Schutzfunktion, Ihre Daten waren jederzeit sicher. Wir haben den Schlüssel anschließend erneuert. Künftig verteilen wir BIOS-Updates zentral und setzen BitLocker dabei vorher automatisch kurz aus, dann erscheint diese Abfrage nicht mehr.

## Prevention / follow-up
- Problem ticket PRB-0005: firmware updates via Intune/Windows Update drivers only, with BitLocker suspend (`Suspend-BitLocker -RebootCount 1`) before the update; block the vendor's self-service update tool for standard users
- KB article [KB-EN-03 BitLocker recovery key lookup](https://github.com/BigDougCed/07-it-knowledge-base/blob/main/en/KB-EN-03-bitlocker-recovery.md)

## Lab reproduction
On the Intune VM: changed the Secure Boot setting in Hyper-V (VM settings › Security) while the VM was off → next boot showed the BitLocker recovery screen. Looked up the key in Intune by Key ID and unlocked.
