# Day 04 – Windows Event Logs

**Date:** 2026-10 · **Status:** ✅ Done

## Sources
- TryHackMe – Windows Event Logs · Ultimate Windows Security Event ID encyclopedia
- Splunk `attack_data` sample logs (T1110)

## What I learned

### Where Windows logs live
- **Event Viewer** (`eventvwr.msc`) → *Windows Logs*: **Security**, **System**, **Application**, plus *Applications and Services Logs* (Sysmon, PowerShell, Task Scheduler…).
- Stored as `.evtx` files in `C:\Windows\System32\winevt\Logs\`.
- Each event has: **Event ID**, **Time**, **Computer**, **User/SID**, **Level**, **Source (Provider)**, and an **XML/Details** view with the real fields.

### Security log – the Event IDs that matter
| ID | Meaning | Why it matters |
| --- | --- | --- |
| **4624** | Successful logon | Who logged in, from where, how (Logon Type) |
| **4625** | Failed logon | Brute-force, password spraying |
| **4634 / 4647** | Logoff | Session length |
| **4648** | Logon with explicit credentials (`runas`) | Lateral movement, credential use |
| **4672** | Special privileges assigned (admin logon) | Admin activity |
| **4688** | New process created | Command execution (needs *command line auditing* enabled) |
| **4697 / 7045** | Service installed | Persistence (7045 is in the *System* log) |
| **4698 / 4702** | Scheduled task created / updated | Persistence |
| **4720** | User account created | Backdoor account |
| **4722 / 4725 / 4726** | Account enabled / disabled / deleted | Account manipulation |
| **4724** | Password reset attempt | Account takeover |
| **4728 / 4732 / 4756** | User added to a (global / local / universal) group | Privilege escalation, esp. *Administrators* |
| **4768 / 4769 / 4771** | Kerberos TGT request / service ticket / pre-auth failed | Domain authentication, Kerberoasting |
| **4776** | NTLM authentication | Legacy auth, pass-the-hash |
| **1102** | **Audit log cleared** | Attacker covering tracks – always investigate |
| **4104** (PowerShell/Operational) | Script block logging | The actual PowerShell code that ran |

### Logon Types (field in 4624/4625)
| Type | Name | Meaning |
| --- | --- | --- |
| 2 | Interactive | Physically at the keyboard |
| 10 | RemoteInteractive | Remote Desktop (RDP) |

Full list of logon types and event IDs: [Ultimate Windows Security encyclopedia](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/).

## Hands-on (lab exercise)
1. From Splunk `attack_data`, open the Windows Security log in the **T1110** (brute force) folder in VS Code.
2. Find the 4625 events; note the target user, Logon Type and source IP. Then find whether a 4624 follows from the same source.
3. On any Windows machine: Event Viewer → Security → filter by Event ID 4624, check the Logon Type of your own session.

## SOC takeaway
- Successful logon right after a burst of failures from the same source = the most important pattern to catch.
- Logon Type tells **how** someone got in (console, network, RDP); an admin account with Type 10 from an unknown IP is a priority alert.
- Account creation, group membership changes and audit-log clearing are rare in normal operation; each deserves a look.

## Questions / next
- Sysmon (Day 05).
