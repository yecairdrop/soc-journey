# Day 12 – First Investigation: a Web Server Compromise (BOTSv1 approach)

**Date:** 2026-10 · **Status:** ✅ Done

## Sources
- CyberDefenders – Boss Of The SOC v1 · TryHackMe – Incident handling with Splunk

## What I learned

### Investigation methodology
1. **Scope:** which asset, which time window, which data sources exist (`| stats count by sourcetype`).
2. **Anchor:** find the first clearly malicious event and work **backwards** (how did they get here?) and **forwards** (what did they do next?).
3. **Pivot:** every new indicator (IP, user, file, hash) becomes a new search.
4. **Timeline:** write every finding with its timestamp as you go.
5. **Map:** place each step on the Kill Chain / ATT&CK.

### The classic web attack chain and where it shows in logs
| Stage | Attacker action | Log evidence | Search idea |
| --- | --- | --- | --- |
| Recon / scanning | Vulnerability scanner hits hundreds of URLs | Web access log, IDS (Suricata) alerts | `stats count by src_ip` on HTTP, look at `User-Agent` and `status` spread |
| Weaponization / exploitation | CMS admin brute force | Many `POST /login` from one IP | `stats count by src_ip, status` on login URI |
| Installation | Upload of a web shell or script | `POST` to upload path, new file on server | HTTP `POST` with file extensions `.php/.jsp`, Sysmon 11 on the server |
| C2 / actions | Defacement, data access | `GET` of unusual files, outbound connections from the web server | Sysmon 3 from `w3wp.exe`/`httpd` |

### Useful data sources in BOTS-style datasets
- `stream:http` – reconstructed HTTP traffic (method, URI, user agent, form data)
- `suricata` – IDS alerts with signature names
- `iis` / `apache` – web server access logs
- `WinEventLog:Security`, `XmlWinEventLog:Microsoft-Windows-Sysmon/Operational` – what happened on the host
- `fgt_utm` / firewall – allowed/denied connections

### Questions an investigation must answer
- What was the attacker's IP (or IPs)? Which scanner / tool did they use (User-Agent)?
- Which account and password did they get in with? How many attempts did it take?
- What did they upload and where did it land?
- What did they change (defacement) and did they move anywhere else?
- Which **IOCs** go into the report, and which **TTPs** go into new detections?

## Hands-on (lab exercise)
- Work through BOTSv1 on CyberDefenders (or the TryHackMe room). For each question, record: *the search I ran → what I found → which stage it belongs to*. Build the timeline and finish with the alert report template.

## SOC takeaway
- A real investigation is a chain of small, reproducible searches; the notebook of queries **is** the evidence.
- Separate what the logs *show* from what you *infer*, and say which is which in the report.

## Questions / next
- Build and document my own detections (Day 13).
