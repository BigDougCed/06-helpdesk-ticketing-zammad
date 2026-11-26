# REQ-1010 – Software request: PDF editor

| Field | Value |
|---|---|
| Opened | 2026-11-13 14:10 via portal |
| Requester | Sabine Weiß, Accounting |
| Category | Request › Software |
| Priority | **P4**: request, no outage |
| SLA | ✅ resolution 5 BD: done 2026-11-17 |
| Group / agent | Requests / Cedric |
| Time spent | 30 min |

## Request
> „Ich muss PDF-Rechnungen zusammenfügen und Seiten drehen. Können Sie mir ein PDF-Programm installieren? Eine Kollegin nutzt eine kostenlose Software aus dem Internet."

## Steps
1. **Clarified the need**: merge, rotate, delete pages; no editing of text, no e-signatures.
2. Checked existing tools: Adobe Acrobat **Reader** (already available in Company Portal) can't merge. "Free software from the internet" = not approved, licence and security unclear (many free PDF tools contain adware). Checked the colleague's PC: an unapproved tool installed → separate ticket INC-1016 to remove it.
3. Options presented to the manager:
   | Option | Cost | Note |
   |---|---|---|
   | Adobe Acrobat Standard | Licence per user/year | Full feature set |
   | **PDF24 Creator** (free, also for businesses) | 0 € | Merge/rotate/compress, packaged as Win32 app |
4. Manager approved PDF24 for Accounting.
5. Packaged it as a Win32 app (same method as 7-Zip in project 05), assigned **Available** to `GG_Accounting` → she installed it from **Company Portal** herself.

## Customer communication
> Guten Tag Frau Weiß, das Programm „PDF24 Creator" steht für die Buchhaltung jetzt im Unternehmensportal bereit (Startmenü → „Unternehmensportal" → PDF24 → Installieren). Damit können Sie PDFs zusammenfügen und Seiten drehen. Bitte installieren Sie keine Software aus dem Internet selbst. Wir prüfen und stellen sie gern bereit.

## Prevention / follow-up
- Added PDF24 to the software catalogue (approved software list)
- Removed the unapproved tool from the colleague's PC (INC-1016)
- Idea: Defender for Endpoint / Intune report of discovered apps to find shadow IT

## Lab reproduction
Packaged a second Win32 app and deployed it as *Available*; installed it as a standard user from Company Portal on the lab VM.
