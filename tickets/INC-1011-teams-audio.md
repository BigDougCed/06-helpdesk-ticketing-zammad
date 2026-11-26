# INC-1011 – No sound in Teams meetings

| Field | Value |
|---|---|
| Opened | 2026-11-16 09:50 via Teams chat |
| Requester | Anna Schmidt, Sales lead |
| Category | Collaboration › Teams |
| Priority | **P3**: single user; customer call at 11:00, joined by phone as workaround |
| SLA | ✅ response 09:52, resolved 10:15 |
| Group / agent | 1st-Level Service Desk / Cedric |
| Time spent | 25 min |

## Symptom
> „In Teams-Besprechungen höre ich niemanden und man hört mich nicht. YouTube hat Ton."

## Troubleshooting
1. YouTube works → Windows audio fine; problem is Teams' device selection.
2. Quick Assist: Teams › *Settings › Devices*: speaker = **"Dock Audio"** (new docking station, no speakers), microphone = the same dock. Her USB headset was connected but not selected.
3. Selected the headset for speaker + microphone → *Make a test call* → OK.
4. Still no microphone in a real meeting → Windows *Settings › Privacy & security › Microphone* → "Let desktop apps access your microphone" was **off** (she turned it off after a privacy article).
5. Switched it on → test call and meeting OK. ✅

## Root cause
Two settings: wrong default device after connecting a new dock, plus microphone access blocked for desktop apps in Windows privacy settings.

## Resolution
Correct devices in Teams, microphone permission enabled.

## Customer communication
> Guten Tag Frau Schmidt, Teams hatte nach dem Anschluss der neuen Dockingstation die falschen Audiogeräte gewählt, und der Mikrofonzugriff war in den Windows-Datenschutzeinstellungen deaktiviert. Beides ist korrigiert. Tipp: Vor wichtigen Terminen unter *Einstellungen › Geräte › Testanruf* kurz prüfen.

## Prevention / follow-up
- KB article [KB-DE-06 Kein Ton in Teams](https://github.com/BigDougCed/07-it-knowledge-base/blob/main/de/KB-DE-06-teams-ton.md)
- If it happens to several users: Teams cache reset (`%APPDATA%\Microsoft\Teams` classic / new Teams: *Settings › Apps › Teams › Reset*)

## Lab reproduction
On the M365 lab VM: disabled "Let desktop apps access your microphone", selected a virtual audio device in Teams → test call failed. Restored both settings.
