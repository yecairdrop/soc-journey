# Day 05 – Sysmon

**Date:** 2026-10 · **Status:** ✅ Done

## Sources
- TryHackMe – Sysmon · TrustedSec Sysmon Community Guide · SwiftOnSecurity sysmon-config

## What I learned

### What Sysmon is and why it exists
- **Sysmon** (System Monitor) is a free Microsoft Sysinternals tool that installs as a Windows service and driver.
- Default Windows logging tells you *that* a process started; Sysmon tells you **what it ran, who its parent was, which file it wrote, which IP it connected to and which domain it looked up**.
- It writes to *Applications and Services Logs → Microsoft → Windows → Sysmon → Operational*.
- It needs a **configuration file** that decides what to log. Community configs (SwiftOnSecurity, sysmon-modular) filter out noise and keep security-relevant events.
- Install: `sysmon64.exe -accepteula -i sysmonconfig.xml` · Update config: `-c` · Uninstall: `-u`.

### The event IDs an analyst uses most
| ID | Event | Key fields | Typical use |
| --- | --- | --- | --- |
| **1** | Process creation | `Image`, `CommandLine`, `ParentImage`, `User`, `Hashes` | What ran and who launched it |
| **3** | Network connection | `Image`, `DestinationIp`, `DestinationPort` | Which program talked to which IP |
| **5** | Process terminated | `Image` | Lifetime of a process |
| **7** | Image (DLL) loaded | `ImageLoaded`, `Signed` | DLL hijacking, unsigned libraries |
| **8** | CreateRemoteThread | `SourceImage`, `TargetImage` | Code injection into another process |
| **10** | Process access | `SourceImage`, `TargetImage`, `GrantedAccess` | Credential dumping attempts on `lsass.exe` |
| **11** | File created | `Image`, `TargetFilename` | Dropped payloads, downloads |
| **12 / 13 / 14** | Registry key / value / rename | `TargetObject` | Persistence via Run keys |
| **15** | File stream created | `TargetFilename` | Downloaded files (Mark-of-the-Web) |
| **17 / 18** | Named pipe created / connected | `PipeName` | Cobalt Strike-style C2, lateral movement |
| **22** | DNS query | `QueryName`, `Image` | Which process resolved which domain |
| **23 / 26** | File delete (archived / logged) | `TargetFilename` | Anti-forensics |
| **25** | Process tampering | `Image` | Process hollowing |

### Reading a Sysmon event: the parent-child relationship
- Every process has a **parent**. Normal: `explorer.exe → chrome.exe`.
- Suspicious: an Office document spawning a shell: `winword.exe → cmd.exe → powershell.exe`. Word has no reason to start a shell; this is the classic macro-malware chain.
- Other red flags: `powershell.exe` with `-enc` (Base64-encoded command), `-w hidden`, `-nop`; processes running from `C:\Users\<user>\AppData\Local\Temp\`; system binaries like `svchost.exe` with a non-system parent.
- **LOLBins** (Living Off the Land Binaries): legitimate Windows tools abused by attackers – `certutil` (download), `mshta`, `rundll32`, `regsvr32`, `wmic`, `bitsadmin`. Sysmon ID 1 + 3 together show a LOLBin making a network connection.

## Hands-on (lab exercise)
1. From `attack_data`, open `windows-sysmon.log` in the **T1059.001** (PowerShell) folder.
2. Count process creations: `grep -c "<EventID>1<" windows-sysmon.log`.
3. Read a few `CommandLine` values. Find at least one encoded or hidden PowerShell command and note its `ParentImage`.
4. Filter ID 22 events and list the domains that were resolved.

## SOC takeaway
- Sysmon ID 1 with the **full command line** is the single most valuable Windows log for detection.
- Always ask: *Is this parent-child pair normal? Is the path normal? Is the binary signed?*
- ID 3 + ID 22 let you tie a network connection and a DNS lookup back to the exact process, which native Windows logs cannot do.

## Questions / next
- SOC fundamentals and defense frameworks (Day 06).
