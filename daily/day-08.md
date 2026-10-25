# Day 08 – SIEM Basics & First Data in Splunk

**Date:** 2026-10 · **Status:** ✅ Done

## Sources
- Splunk Education – Intro to Splunk · TryHackMe – Intro to SIEM, Splunk: Basics

## What I learned

### What a SIEM does
**S**ecurity **I**nformation and **E**vent **M**anagement. One platform that:
1. **Collects** logs from everywhere (servers, firewalls, endpoints, cloud, apps)
2. **Normalizes** them into common fields (`src_ip`, `user`, `action`)
3. **Indexes** them so terabytes can be searched in seconds
4. **Correlates** events across sources ("failed VPN logins + new admin account on the same user")
5. **Alerts** when a rule matches, and shows **dashboards**

Without a SIEM an analyst would have to log in to every system separately; with one, a single search covers the whole company.

### How logs get in
| Method | Use |
| --- | --- |
| **Agent / forwarder** on the host (Splunk Universal Forwarder, Elastic Agent) | Windows, Linux, Sysmon |
| **Syslog** (UDP/TCP 514) | Firewalls, switches, Linux systems |
| **API pull** | Cloud services (Microsoft 365, AWS CloudTrail) |
| **File upload** | Lab work, one-off investigations |

### Splunk architecture
- **Universal Forwarder** – lightweight agent that ships logs
- **Indexer** – stores and indexes data
- **Search Head** – the web UI where you search (`localhost:8000`)
- In a lab, one Splunk Enterprise instance plays all three roles.

### Core Splunk concepts
| Term | Meaning | Example |
| --- | --- | --- |
| **Event** | One log entry with a timestamp | One 4625 record |
| **Index** | A bucket where data is stored; the first filter in every search | `index=homelab` |
| **Sourcetype** | The log format; drives field extraction | `WinEventLog:Security`, `linux_secure` |
| **Source** | The file or input the event came from | `/var/log/auth.log` |
| **Host** | The machine that produced the event | `DESKTOP-01` |
| **Field** | A key–value pair extracted from the event | `src_ip=10.0.0.5` |
| `_time` | The event's timestamp (Splunk's primary axis) | |

### The search interface
- **Search bar** + **time range picker** (always set it; uploaded lab data is old → use *All time*).
- Left panel: **Interesting fields** with counts (click one to see top values).
- Tabs: **Events** (raw), **Statistics** (tables from `stats`), **Visualization** (charts).
- A search without a transforming command returns raw events; with `stats`/`timechart` it returns a table.

### Licensing
- Free license: 500 MB/day, no alerts, no authentication. Enough for a lab.
- Enterprise trial: 60 days full features. Cloud trial: 14 days.

## Hands-on (lab exercise)
1. Start Splunk (`/Applications/Splunk/bin/splunk start`), open `http://localhost:8000`.
2. Settings → Add Data → Upload `OpenSSH_2k.log` → index `homelab`.
3. Run `index=homelab "Failed password" | stats count` with *All time*. Compare with the `grep -c` result from Day 03.
4. Run `index=homelab | stats count by sourcetype` and explore the Interesting fields panel.

## SOC takeaway
- Every investigation starts with `index=… sourcetype=…` and a tight time window; this keeps searches fast and relevant.
- Knowing which **sourcetype** holds which evidence (auth, process, network, DNS) is half the job.

## Questions / next
- Ingesting Windows and Sysmon logs (Day 09).
