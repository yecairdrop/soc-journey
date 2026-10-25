# Day 13 – Mini Project: Brute-Force & Discovery Detection

**Date:** 2026-10 · **Status:** ✅ Done

## Sources
- MITRE ATT&CK T1110, T1087, T1033 · Splunk `attack_data` · Splunk docs: Alerts, Dashboards

## What I learned

### From a search to a detection
A detection = **a search** + **a threshold** + **a schedule** + **an action**.

| Part | Decision | Example |
| --- | --- | --- |
| Logic | What behavior am I looking for? | ≥5 failed logons for one user in 5 minutes |
| Data | Which sourcetype/fields? | `WinEventLog:Security`, `EventCode=4625`, `TargetUserName` |
| Threshold | Avoids noise; tuned to the environment | 5 in 5 min for a workstation, higher for a VPN gateway |
| Schedule | How often it runs and over what window | Every 15 min, looking back 15 min |
| Trigger | When to fire | Number of results > 0 |
| Action | What happens | Create ticket, email, SOAR playbook |
| Mapping | ATT&CK ID + severity | T1110 – Credential Access – Medium |

### Detection 1 – Brute force (T1110)
```
index=homelab EventCode=4625
| bin _time span=5m
| stats count as fails, dc(IpAddress) as sources by _time, TargetUserName
| where fails >= 5
```
Variant for **password spraying** (one source, many users): `stats dc(TargetUserName) as users by _time, IpAddress | where users >= 10`.
Upgrade: join with 4624 to flag **failures followed by success**.

### Detection 2 – Discovery commands (T1087 / T1033 / T1018)
```
index=homelab EventCode=1
  (CommandLine="*whoami*" OR CommandLine="*net user*" OR CommandLine="*net localgroup*"
   OR CommandLine="*net group*" OR CommandLine="*query user*" OR CommandLine="*nltest*")
| stats count, values(CommandLine) as commands by host, User, ParentImage
| where count >= 3
```
Counting **several different discovery commands within minutes** from one host is far stronger than alerting on a single `whoami`.

### Tuning false positives
- Allow-list known scanners, service accounts, IT admin hosts (via a lookup).
- Raise thresholds where the baseline is noisy; lower them for high-value assets.
- Document every exception with a reason and an owner.

### Dashboard
Three panels built from the same searches: failed logons over time, top targeted users, discovery commands by host. A dashboard is for **monitoring**; an alert is for **action**.

## Hands-on (lab exercise)
1. Load the T1110 and T1087 datasets from `attack_data` into `homelab`.
2. Run both detections; adjust field names to what the add-on extracts.
3. Save Detection 1 as a **Report** locally; in a Splunk Cloud trial save it as an **Alert** (every 15 min, trigger if results > 0) and screenshot the configuration.
4. Build the 3-panel dashboard.
5. Fill in `projects/01-bruteforce-detection/README.md`.

## SOC takeaway
- Good detections describe **behavior over a window**, not a single event.
- Every alert needs an owner, a threshold rationale and a false-positive plan, or it becomes noise that analysts learn to ignore.

## Questions / next
- Portfolio write-up and program review (Day 14).
