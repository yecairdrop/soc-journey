# Day 11 – Alert Triage & Reporting

**Date:** 2026-10 · **Status:** ✅ Done

## Sources
- TryHackMe – SOC L1 Alert Triage, SOC L1 Alert Reporting · LetsDefend · MyDFIR

## What I learned

### What triage is
Triage = deciding **quickly** whether an alert is real, how serious it is, and what happens next. An L1 analyst may handle dozens a shift, so the process must be repeatable.

### The triage questions (in order)
1. **What** fired? Rule name, technique ID, severity set by the rule.
2. **Who / what is affected?** Host, user, IP. Is it a server, an admin, a VIP, a test machine?
3. **When?** Working hours? Is this a one-off or a repeating pattern?
4. **Where from?** Internal or external source IP? Known or unknown?
5. **Is it expected?** Change ticket, pentest, admin maintenance, known scanner?
6. **What happened around it?** Pivot: same host ±30 min, same user, same source IP.
7. **Verdict** and **next step.**

### Enrichment sources
| Question | Tool |
| --- | --- |
| Is this IP/domain known bad? | VirusTotal, AbuseIPDB, Talos Intelligence |
| Who owns this IP? | `whois`, ipinfo.io |
| Is this file hash malware? | VirusTotal, MalwareBazaar |
| Is this domain new? | Whois creation date (newly registered = suspicious) |
| What is this technique? | MITRE ATT&CK |
| Is this host/user important? | Asset inventory, HR/IT records |

### Verdicts
| Verdict | Meaning | Action |
| --- | --- | --- |
| **True Positive** | Malicious activity confirmed | Escalate / contain |
| **False Positive** | Rule fired, nothing malicious | Close, note tuning idea |
| **Benign True Positive** | Activity real but authorized | Close with reference (ticket, pentest) |
| **Inconclusive** | Not enough data | Escalate with what you have, request more logs |

### Severity
- Based on **impact** (admin account? domain controller? data exfil?) × **confidence** (how sure am I?).
- Later Kill Chain stage = higher severity (C2 beaconing > a single port scan).

### Escalate when
- Confirmed malware execution, credential theft, lateral movement, data exfiltration.
- Admin or service account involved.
- You cannot explain it within the time budget (typically 15–30 min for L1).

### The alert report
A good report lets L2 act **without re-doing your work**. Template saved at `templates/alert-report.md`: Summary → Affected assets → Timeline → Evidence/queries → IOCs → MITRE mapping → Verdict → Recommended actions.

Writing rules: facts with timestamps and sources; separate *observed* from *assumed*; include the exact searches so others can reproduce.

## Hands-on (lab exercise)
- On LetsDefend (free) or Blue Team Labs Online, take 2–3 alerts through the seven questions and write each up with the template.

## SOC takeaway
- Speed comes from a fixed checklist, not from guessing.
- "Benign true positive" is a real verdict; closing it as "false positive" hides a detection that actually works.
- Every closed alert should leave a tuning note: what would have made it clearer or quieter?

## Questions / next
- First full investigation (Day 12).
