# Day 06 – SOC Fundamentals, Pyramid of Pain, Cyber Kill Chain

**Date:** 2026-10 · **Status:** ✅ Done

## Sources
- TryHackMe – Junior Security Analyst Intro, Pyramid of Pain, Cyber Kill Chain · MyDFIR

## What I learned

### What a SOC is
- A **Security Operations Center** is the team (and tooling) that **monitors, detects, investigates and responds** to security incidents 24/7.
- It sits between raw telemetry (logs, alerts) and the business: its output is *decisions*, not just alerts.

| Tier | Role | Typical tasks |
| --- | --- | --- |
| **L1 – Triage analyst** | First line | Watches the alert queue, classifies TP/FP, enriches, escalates. Target role for me. |
| **L2 – Incident responder** | Deep dive | Full investigation, containment, root cause |
| **L3 – Threat hunter / Detection engineer** | Proactive | Hunts for what the alerts missed; writes and tunes detection rules |
| **SOC manager** | Leadership | Processes, metrics, staffing |

### The toolbox
| Tool | What it does | Examples |
| --- | --- | --- |
| **SIEM** | Collects, normalizes, correlates logs; raises alerts; dashboards | Splunk, Microsoft Sentinel, Elastic, QRadar |
| **EDR** | Endpoint telemetry + response (isolate host, kill process) | CrowdStrike, Defender for Endpoint, SentinelOne |
| **SOAR** | Automates playbooks (enrich, ticket, block) | Splunk SOAR, Cortex XSOAR |
| **IDS/IPS** | Network signature detection / blocking | Snort, Suricata |
| **Firewall / proxy** | Allow/deny traffic, URL filtering | Palo Alto, Fortinet, Zscaler |
| **Threat intel platform** | IOCs, reports | MISP, VirusTotal, AbuseIPDB |

### The alert lifecycle
1. **Detection** – a rule fires in SIEM/EDR.
2. **Triage** – is it real? what is affected? how bad? (L1)
3. **Investigation** – scope, timeline, root cause (L1/L2)
4. **Containment** – isolate host, disable account, block IP
5. **Eradication & recovery** – remove the threat, restore
6. **Lessons learned** – tune rules, fix gaps

Verdicts: **True Positive** (real attack), **False Positive** (rule fired, nothing malicious), **Benign True Positive** (the activity happened but was authorized, e.g. a pentest or admin task). Missed attacks are **False Negatives**.

Metrics a SOC tracks: **MTTD** (mean time to detect), **MTTR** (mean time to respond), alert volume, FP rate.

### Pyramid of Pain (David Bianco)
How much it *hurts the attacker* when defenders detect and block each indicator type, from bottom to top:

| Level | Indicator | Attacker's cost to change |
| --- | --- | --- |
| 1 | **Hash values** | Trivial – recompile or change one byte |
| 2 | **IP addresses** | Easy – new VPS or proxy |
| 3 | **Domain names** | Simple – register another domain |
| 4 | **Network / host artifacts** (User-Agent strings, registry keys, file paths) | Annoying – must change tooling behavior |
| 5 | **Tools** (the malware or framework itself) | Challenging – rewrite or buy new tools |
| 6 | **TTPs** (tactics, techniques, procedures – *how* they operate) | **Tough** – changing behavior means retraining |

Lesson: blocking hashes and IPs is necessary but cheap for the attacker. Detections built on **behavior** (TTPs) last.

### Cyber Kill Chain (Lockheed Martin)
The seven stages of an intrusion; breaking any link stops the attack:

| # | Stage | What the attacker does | Where defenders can see it |
| --- | --- | --- | --- |
| 1 | **Reconnaissance** | Collects info: employees, emails, open ports, technologies | OSINT is mostly invisible; port scans show in firewall/IDS logs |
| 2 | **Weaponization** | Builds the payload (malicious document, exploit) | Happens on the attacker's side |
| 3 | **Delivery** | Sends it: phishing email, malicious link, USB | Email gateway, proxy logs |
| 4 | **Exploitation** | The payload runs (macro, vulnerability) | Sysmon ID 1, EDR |
| 5 | **Installation** | Persistence: new service, Run key, scheduled task | Sysmon 12/13, Event 7045/4698 |
| 6 | **Command & Control (C2)** | Malware calls home | Sysmon 3/22, proxy/DNS logs, beaconing patterns |
| 7 | **Actions on Objectives** | Data theft, ransomware, lateral movement | SMB/RDP logs, large outbound transfers |

Related models: the **Unified Kill Chain** (18 phases, adds internal movement in detail) and the **Diamond Model** (adversary – infrastructure – capability – victim) used to describe a single intrusion event.

## Hands-on (lab exercise)
- Take the SSH brute-force log from Day 03 and the T1110 Windows log from Day 04. Place each attack on the Kill Chain (which stage is a brute-force attempt?) and on the Pyramid of Pain (what would blocking the IP achieve vs. detecting the *behavior*?).

## SOC takeaway
- An L1 analyst's job is to turn an alert into a confident verdict quickly and to escalate with a clear summary.
- Map every alert to a Kill Chain stage: the later the stage, the more urgent.
- Prefer detections on behavior (TTPs) over single IOCs; they survive attacker infrastructure changes.

## Questions / next
- MITRE ATT&CK (Day 07).
