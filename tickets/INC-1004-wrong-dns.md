# INC-1004 – Internet works, shares and intranet don't

| Field | Value |
|---|---|
| Opened | 2026-11-05 13:20 via portal |
| Requester | Kwame Mensah, Accounting |
| Category | Network |
| Priority | **P3**: single user, colleagues' PCs work |
| SLA | ✅ response 13:31, resolved 13:55 |
| Group / agent | 1st-Level Service Desk / Cedric |
| Time spent | 25 min |

## Symptom
> „Google und YouTube gehen, aber Laufwerk A: ist rot durchgestrichen und das Intranet lädt nicht. Bei der Kollegin geht alles."

## Triage
Internet OK but internal names fail → classic **DNS** pattern. Only one PC → P3.

## Troubleshooting
1. Quick Assist session.
2. `ipconfig /all`: IP `10.10.10.134` (DHCP, OK), gateway OK, but **DNS servers: 8.8.8.8, 8.8.4.4** instead of `10.10.10.10`.
3. `nslookup dc01.corp.nordlicht.internal` → `Non-existent domain` (Google DNS doesn't know our internal domain).
4. `ping 10.10.10.10` → OK, so the network path is fine and only name resolution fails.
5. Adapter properties: DNS set **manually**. He said a "YouTube tutorial to make the internet faster" told him to do it.

## Root cause
Manually configured public DNS servers override the DHCP option 006 → internal names (`dc01`, `corp.nordlicht.internal`, intranet) can't be resolved.

## Resolution
Set the adapter back to *Obtain DNS server address automatically*, then:
```cmd
ipconfig /flushdns
ipconfig /renew
ipconfig /registerdns
gpupdate /force
```
Drive A: reconnects, intranet loads. ✅

## Customer communication
> Guten Tag Herr Mensah, an Ihrem PC war ein externer DNS-Server (Google) eingetragen. Dadurch hat Ihr PC unsere internen Server nicht mehr gefunden. Wir haben die Einstellung auf „automatisch" zurückgesetzt. Bitte ändern Sie Netzwerkeinstellungen nicht selbst. Wenn das Internet langsam ist, melden Sie sich gerne bei uns, wir schauen es uns an.

## Prevention / follow-up
- Standard users shouldn't be able to change adapter settings → GPO `U_Control_Panel_Restrict` + "Prohibit access to properties of a LAN connection"; check that this user isn't a local admin (he was: removed, see `C_Local_Admins`).
- KB article [KB-DE-03 Netzlaufwerk nicht verbunden](https://github.com/BigDougCed/07-it-knowledge-base/blob/main/de/KB-DE-03-netzlaufwerk.md)

## Lab reproduction
On CL02: `Set-DnsClientServerAddress -InterfaceAlias Ethernet -ServerAddresses 8.8.8.8,8.8.4.4` → internet works, `\\DC01\Accounting` and `nslookup dc01` fail. Fixed with `-ResetServerAddresses`.
