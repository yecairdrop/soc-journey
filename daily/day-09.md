# Day 09 – Ingesting Windows & Sysmon Logs into Splunk

**Date:** 2026-10 · **Status:** ✅ Done

## Sources
- Splunk Education – Using Fields · Splunk `attack_data` README · Splunk Add-on for Microsoft Windows / Sysmon

## What I learned

### Why add-ons matter
- Raw Windows events arrive as XML. Splunk needs to know **how to extract fields** (`EventCode`, `TargetUserName`, `IpAddress`, `Image`, `CommandLine`).
- **Technology Add-ons (TA)** contain the parsing rules for a log format:
  - *Splunk Add-on for Microsoft Windows* → `WinEventLog:Security`, `XmlWinEventLog:Security`
  - *Splunk Add-on for Sysmon* → `XmlWinEventLog:Microsoft-Windows-Sysmon/Operational`
- Installed from Splunkbase (Apps → Find More Apps) with a free splunk.com account.
- Choosing the **right sourcetype at upload time** is what makes the add-on work.

### Common Windows fields in Splunk
| Raw Windows name | Splunk field (with TA) | Meaning |
| --- | --- | --- |
| EventID | `EventCode` | Event ID (4624, 4625…) |
| TargetUserName | `user` / `TargetUserName` | Account the action was about |
| IpAddress | `src` / `IpAddress` | Source IP of the logon |
| LogonType | `Logon_Type` / `LogonType` | How the logon happened |
| Image | `process` / `Image` | Executable path (Sysmon) |
| CommandLine | `process` command line / `CommandLine` | Full command (Sysmon ID 1) |
| ParentImage | `parent_process` / `ParentImage` | Parent executable |

Field names vary by add-on version; always check the left panel instead of guessing.

### Splunk `attack_data`
- A GitHub repository of **real attack logs** organized by ATT&CK technique: `datasets/attack_techniques/T1110.001/…`.
- Each dataset has a `.yml` file stating the **sourcetype** and **source** to use when uploading.
- Lets you practice detection without running malware yourself.

### Common Information Model (CIM)
- A set of standard field names (`src`, `dest`, `user`, `action`) so one search works across vendors. Add-ons map raw fields to CIM.
- Searches written against CIM fields are **portable** between Windows, Linux and firewall data.

### macOS own logs (bonus)
- `log show --last 1h --predicate 'process == "sudo"' > ~/sudo_log.txt` exports unified-log entries; upload to Splunk and search `"incorrect password"`.

## Hands-on (lab exercise)
1. Install both add-ons from Splunkbase.
2. Upload the T1110 Security log and the T1059.001 Sysmon log from `attack_data` with the sourcetype given in their `.yml` files, into `homelab`.
3. Verify: `index=homelab | stats count by sourcetype, EventCode | sort -count`.
4. Confirm `TargetUserName`, `IpAddress`, `Image` and `CommandLine` appear in the field panel.
5. Document the steps with screenshots as the first lab write-up.

## SOC takeaway
- If a search returns nothing, check in order: **time range → index → sourcetype → field names**.
- Bad parsing silently breaks detections; verifying fields after ingestion is a real SOC task (detection engineers do it constantly).

## Questions / next
- Core SPL (Day 10).
