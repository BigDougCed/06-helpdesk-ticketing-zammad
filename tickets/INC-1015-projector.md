# INC-1015 – Projector: no signal, meeting in 10 minutes

| Field | Value |
|---|---|
| Opened | 2026-11-24 09:50 via phone |
| Requester | Anna Schmidt, Sales lead |
| Category | Meeting room |
| Priority | **P2**: customer presentation at 10:00, external visitors waiting |
| SLA | ✅ response immediately, resolved 09:57 (on-site) |
| Group / agent | 1st-Level Service Desk / Cedric (on-site) |
| Time spent | 15 min |

## Symptom
> „Der Beamer im Besprechungsraum zeigt ‚Kein Signal'. Der Kunde kommt in zehn Minuten!"

## Triage
Time-critical external meeting → went **on site** immediately instead of troubleshooting by phone.

## Steps (in this order, fastest checks first)
1. Projector input source → was on *HDMI 2*, cable in *HDMI 1* → switched → still no signal.
2. **Win + P** on the laptop → was set to *PC screen only* (after last use at home with a monitor) → *Duplicate* → image appears, but flickering.
3. Flickering → cable goes through a USB-C-to-HDMI adapter. Swapped to the spare adapter from the meeting-room kit → stable image. ✅
4. Resolution: set the laptop to 1920×1080 (projector native), *Duplicate*; audio through HDMI checked for the customer video.
5. Done at 09:57, before the visitors arrived.

## Root cause
Wrong display mode on the laptop + a faulty USB-C adapter.

## Resolution
Display mode Duplicate, adapter replaced, faulty adapter labelled "defekt" and removed.

## Customer communication
On site + short note in the ticket:
> Guten Tag Frau Schmidt, der Laptop war auf „Nur PC-Bildschirm" eingestellt, und der USB-C-Adapter im Raum war defekt. Mit der Tastenkombination **Windows + P** können Sie die Anzeige jederzeit selbst auf „Duplizieren" stellen. Ein Ersatzadapter liegt jetzt im Raum.

## Prevention / follow-up
- Meeting-room kit check every Monday (adapters, batteries of the presenter, HDMI cable)
- Laminated quick guide on the table: "Präsentieren in 3 Schritten" → KB article [KB-DE-07 Beamer / Bildschirm verbinden](https://github.com/BigDougCed/07-it-knowledge-base/blob/main/de/KB-DE-07-beamer-verbinden.md)
- Background: I did this regularly for events with up to 150 participants at Hochschule Schmalkalden

## Lab reproduction
Not reproducible in a VM. Practised with a laptop, an external monitor and Win + P; the adapter fault was simulated by a loose cable.
