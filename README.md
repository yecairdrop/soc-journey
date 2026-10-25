# SOC Journey 🛡️

Hi, I'm **Yakup Efe Çelebi**, a university student training to become a **SOC Analyst (Blue Team)**.
This repository documents my learning **day by day**: what I studied, what I practiced hands-on, and the detections I built.

## Focus Areas
- Log analysis (Windows Event Logs, Sysmon, Linux auth logs)
- SIEM & Splunk (SPL)
- Network fundamentals & traffic analysis (Wireshark)
- Detection mapped to MITRE ATT&CK

## Lab
| Component | Details |
| --- | --- |
| Host | MacBook Air (Apple M1, 8 GB RAM) |
| SIEM | Splunk Enterprise 10.4.1 (Free license), index: `homelab` |
| Data | Real-world sample logs (Loghub OpenSSH), Splunk `attack_data` datasets, my own macOS logs |
| Tools | Wireshark, VS Code, Terminal |

## Progress – Phase 1 (14 days) ✅ Complete
| Day | Topic | Status |
| --- | --- | --- |
| 01 | Networking I – OSI, TCP/IP, Wireshark | ✅ |
| 02 | Networking II – DNS, HTTP, ports | ✅ |
| 03 | Linux & log analysis with grep/awk | ✅ |
| 04 | Windows Event Logs | ✅ |
| 05 | Sysmon | ✅ |
| 06 | SOC fundamentals, Pyramid of Pain, Cyber Kill Chain | ✅ |
| 07 | MITRE ATT&CK + week 1 review | ✅ |
| 08 | SIEM basics, first data in Splunk | ✅ |
| 09 | Ingesting Windows & Sysmon logs | ✅ |
| 10 | Core SPL: stats, eval, where, rex | ✅ |
| 11 | Alert triage & reporting | ✅ |
| 12 | First investigation (BOTSv1) | ✅ |
| 13 | Mini project: brute-force & discovery detection | ✅ |
| 14 | Portfolio & review | ✅ |

## Repository Structure
```
daily/        Daily learning notes (day-01 … day-14)
cheatsheets/  Quick references: ports, Sysmon event IDs, SPL
projects/     Hands-on detection projects (01: brute-force & discovery detection)
templates/    Daily note and alert report templates
```

## Start here
- [Day 01 – Networking I](daily/day-01.md) · [Day 10 – Core SPL](daily/day-10.md) · [Day 13 – Detection project](daily/day-13.md)
- [Alert report template](templates/alert-report.md)

## Contact
GitHub: [@yecairdrop](https://github.com/yecairdrop)
