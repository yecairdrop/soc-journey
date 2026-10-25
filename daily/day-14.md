# Day 14 – Portfolio & Program Review

**Date:** 2026-10 · **Status:** ✅ Done

## What a SOC portfolio needs
- **Daily notes** that show consistency (this `daily/` folder).
- **Cheat sheets** that show I organize knowledge (`cheatsheets/`).
- **At least one end-to-end project**: data → detection → alert → report (`projects/01-bruteforce-detection`).
- A **README** that says who I am, what I'm aiming for, and where to look first.

## Write-up structure that recruiters and SOC leads read
1. One-paragraph summary with the technique IDs.
2. Lab setup (so they know it's real).
3. The detection logic with the exact query.
4. Evidence: screenshots, counts.
5. What was hard, what I'd improve.

## 14-day self-check – can I explain, without notes…
- [ ] A packet's journey: DNS → TCP handshake → HTTP request → response; where MAC and IP change.
- [ ] Subnet mask, default gateway, private IP ranges, the main ports.
- [ ] Reading `auth.log` and building a `grep | sort | uniq -c` pipeline.
- [ ] Windows Event IDs 4624/4625/4688/4720/4732/1102 and Logon Types 2/3/10.
- [ ] Sysmon IDs 1/3/11/13/22 and a suspicious parent-child chain.
- [ ] SOC tiers, alert lifecycle, TP/FP/benign TP, Pyramid of Pain, Kill Chain.
- [ ] Mapping an alert to a MITRE technique and reading a technique page.
- [ ] Splunk concepts (index, sourcetype, field) and the `stats | where` detection shape.
- [ ] Triage questions and the structure of an alert report.

## Next phase (weeks 3–6)
- Phishing analysis (headers, SPF/DKIM/DMARC, URL and attachment analysis).
- Network traffic analysis with real pcaps (malware-traffic-analysis.net).
- More investigations: CyberDefenders BOTSv2/v3, LetsDefend alerts daily.
- A second detection project (persistence: scheduled tasks and Run keys).
- Start a certification path (BTL1 or Security+ when SY0-801 releases).
