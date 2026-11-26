# INC-1012 – C: drive full

| Field | Value |
|---|---|
| Opened | 2026-11-17 11:30 via portal |
| Requester | Marek Nowak, Warehouse |
| Category | Client › Windows |
| Priority | **P3**: single user, PC slow but usable |
| SLA | ✅ response 11:40, resolved 12:30 |
| Group / agent | 1st-Level Service Desk / Cedric |
| Time spent | 45 min |

## Symptom
> „Mein PC ist extrem langsam und Windows meldet ‚Wenig Speicherplatz'. Ich kann Excel-Dateien nicht mehr speichern."

## Troubleshooting
1. Quick Assist: C: 237 GB, **1.2 GB free**.
2. Found the large folders with `Get-ChildItem` sizes (or TreeSize Free from Company Portal):
   ```powershell
   Get-ChildItem C:\Users\marek.nowak -Directory | ForEach-Object {
     [pscustomobject]@{ Folder = $_.Name; GB = [math]::Round((Get-ChildItem $_.FullName -Recurse -File -ErrorAction SilentlyContinue | Measure-Object Length -Sum).Sum / 1GB, 1) }
   } | Sort-Object GB -Descending
   ```
   → `Downloads` 96 GB (camera video exports of warehouse inspections), `OneDrive` 58 GB fully downloaded.
3. **OneDrive Files On-Demand** was off on this PC (installed before the Intune policy) → all OneDrive files stored locally.

## Resolution (with the user's agreement)
1. Turned on Files On-Demand, *Free up space* on the OneDrive root → +54 GB
2. Moved inspection videos to the Warehouse SharePoint/Teams library (team agreed), deleted local copies → +90 GB
3. Disk Cleanup incl. *Windows Update Cleanup* → +6 GB
4. Enabled **Storage Sense** (deletes Recycle Bin/temp after 30 days, makes unused OneDrive files online-only)
5. Result: **152 GB free**, PC responsive again. ✅

## Root cause
Local storage of large videos + OneDrive without Files On-Demand.

## Customer communication
> Guten Tag Herr Nowak, Ihre Festplatte war fast voll: vor allem durch Videodateien im Download-Ordner und OneDrive-Dateien, die komplett lokal gespeichert waren. Die Videos liegen jetzt in der Teams-Ablage „Lager" und sind für das ganze Team erreichbar. OneDrive lädt Dateien jetzt nur noch bei Bedarf herunter. Sie haben wieder über 150 GB frei.

## Prevention / follow-up
- Intune Settings Catalog: Files On-Demand + Storage Sense for all devices (was only for new Autopilot devices) → change CHG-0011
- Proactive: compliance/report on devices with < 10 % free space

## Lab reproduction
On CL01: created large dummy files (`fsutil file createnew C:\Users\test\Downloads\video.mp4 20000000000`) until the disk was nearly full; practised analysis and cleanup.
