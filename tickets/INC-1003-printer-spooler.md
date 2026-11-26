# INC-1003 – Warehouse printer queue stuck

| Field | Value |
|---|---|
| Opened | 2026-11-04 10:05 via phone |
| Requester | Marek Nowak, Warehouse lead |
| Category | Printing |
| Priority | **P2**: whole department can't print delivery notes, trucks are waiting |
| SLA | Response 1 h / resolution 8 h: ✅ response 10:07, resolved 10:40 |
| Group / agent | 1st-Level → 2nd-Level Systems (print server rights) / Cedric |
| Time spent | 35 min |

## Symptom
> „Im Lager druckt gar nichts mehr. Die Lieferscheine hängen alle in der Warteschlange, der LKW wartet!"

## Triage
Department-wide, business impact (deliveries) → **P2**. First question: is the printer itself OK? Marek checked the display: "Ready", paper and toner OK, test page from the printer menu works → hardware fine, problem is the queue.

## Troubleshooting
1. Print Management on DC01 (print server): queue `LAGER-MFP01` shows 23 jobs, first job "Error – Printing", 0 of 48 pages, 180 MB.
2. Pinged the printer IP: OK. Opened the web interface: OK → network fine.
3. Deleting the first job from the GUI hung in "Deleting".
4. Restarted the spooler and cleared the stuck spool files:
   ```powershell
   Stop-Service Spooler -Force
   Get-ChildItem C:\Windows\System32\spool\PRINTERS\* | Remove-Item -Force
   Start-Service Spooler
   ```
5. Asked the warehouse to resend delivery notes → printed within seconds.

## Root cause
A 48-page PDF with high-resolution scans (180 MB spool file) failed in the driver and blocked the queue. All jobs behind it waited.

## Resolution
Spooler restart + spool folder cleanup; the user re-printed the large PDF with *Print as image* off and fewer pages at a time.

## Customer communication
Phone call to Marek at 10:40 ("you can print again"), followed by:
> Guten Tag Herr Nowak, die Druckwarteschlange war durch eine sehr große PDF-Datei (180 MB) blockiert. Wir haben die Warteschlange bereinigt. Alle anderen Aufträge müssen bitte einmal neu gedruckt werden. Viele Grüße, IT-Service-Desk

## Prevention / follow-up
- Problem ticket PRB-0003: update the printer driver to the vendor's current PCL6 version; enable *"Print directly to printer"* off / *"Start printing after last page is spooled"* on.
- KB article [KB-EN-02 Clearing a stuck print queue](https://github.com/BigDougCed/07-it-knowledge-base/blob/main/en/KB-EN-02-stuck-print-queue.md)

## Lab reproduction
Installed a "Generic / Text Only" printer on DC01 pointing to a non-existent IP, shared it, printed a large PDF from CL01 → job stuck "Error – Printing", more jobs queued behind it. Cleared with the steps above.
