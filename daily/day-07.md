# Day 07 – MITRE ATT&CK & Week 1 Review

**Date:** 2026-10 · **Status:** ✅ Done

## Sources
- TryHackMe – MITRE · attack.mitre.org

## What I learned

### What ATT&CK is
- **MITRE ATT&CK** is a public knowledge base of **real-world adversary behavior**, organized as a matrix. It is the common language of SOCs: alerts, reports and threat intel all reference its IDs.
- Three matrices: **Enterprise** (Windows, Linux, macOS, cloud – the one I use), Mobile, ICS.

### Structure
| Layer | Meaning | Example |
| --- | --- | --- |
| **Tactic** | The attacker's *goal* (the "why"); columns of the matrix | TA0006 Credential Access |
| **Technique** | *How* the goal is achieved | T1110 Brute Force |
| **Sub-technique** | A more specific variant | T1110.003 Password Spraying |
| **Procedure** | A concrete real-world example by a group or malware | "APT28 used password spraying against Office 365" |

Each technique page lists: description, **procedure examples**, **mitigations** (M-IDs), **detection** guidance and **data sources** (which logs reveal it), plus the threat **Groups** (G-IDs) and **Software** (S-IDs) that use it.

### The 14 Enterprise tactics in attack order
Reconnaissance → Resource Development → **Initial Access** → **Execution** → **Persistence** → **Privilege Escalation** → **Defense Evasion** → **Credential Access** → **Discovery** → **Lateral Movement** → **Collection** → **Command and Control** → **Exfiltration** → **Impact**

### Techniques I will use in this program
| ID | Technique | Tactic | Evidence |
| --- | --- | --- | --- |
| T1566 | Phishing | Initial Access | Email gateway, user reports |
| T1059.001 | PowerShell | Execution | Sysmon 1 with `powershell.exe`, Event 4104 |
| T1053.005 | Scheduled Task | Persistence | Event 4698, Sysmon 1 `schtasks.exe` |
| T1547.001 | Registry Run Keys | Persistence | Sysmon 13 on `...\CurrentVersion\Run` |
| T1110 | Brute Force | Credential Access | Many 4625 / SSH `Failed password` |
| T1003.001 | LSASS Memory | Credential Access | Sysmon 10 targeting `lsass.exe` |
| T1087 | Account Discovery | Discovery | `net user`, `net localgroup`, `whoami` |
| T1018 | Remote System Discovery | Discovery | Ping sweeps, `net view`, `arp -a` |
| T1021.001 | Remote Desktop Protocol | Lateral Movement | 4624 Logon Type 10 |
| T1071 | Application Layer Protocol (C2) | Command and Control | Sysmon 3/22, proxy logs, beaconing |

### How a SOC uses ATT&CK
- **Alert enrichment:** every detection rule carries a technique ID, so the analyst instantly knows the stage and the goal.
- **Coverage mapping:** with the **ATT&CK Navigator** a team colors which techniques it can detect and finds the blind spots.
- **Threat intel:** reports describe attackers by their techniques, letting defenders prioritize what to detect.
- **Kill Chain vs. ATT&CK:** the Kill Chain is a high-level story in 7 steps; ATT&CK is the detailed catalog of what happens inside each step.

## Week 1 review – I should be able to explain
- [ ] TCP 3-way handshake; IP vs MAC; default gateway and subnet mask; DNS record types; HTTP structure, status codes and cookies.
- [ ] Linux log locations and the `grep | sort | uniq -c` pipeline.
- [ ] The key Windows Security event IDs and Logon Types.
- [ ] Sysmon IDs 1, 3, 11, 13, 22 and why parent-child relationships matter.
- [ ] SOC tiers, alert lifecycle, TP/FP/benign TP.
- [ ] Pyramid of Pain and Cyber Kill Chain, and how to map an alert to ATT&CK.

## SOC takeaway
- Write every finding with its technique ID ("Brute force, T1110, from 10.0.0.5"). It makes reports precise and comparable.
- Detection ideas come from the **Detection** and **Data Sources** sections of a technique page.

## Questions / next
- SIEM and Splunk (Day 08).
