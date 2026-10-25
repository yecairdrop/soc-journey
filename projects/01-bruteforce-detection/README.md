# Project 01 – Brute-Force & Discovery Detection in Splunk

**Status:** 🟡 In progress · **MITRE ATT&CK:** T1110 (Brute Force), T1087 (Account Discovery), T1033 (System Owner/User Discovery)

## Goal
Detect password-guessing attacks and post-compromise reconnaissance commands in Windows logs, turn the searches into scheduled alerts and a dashboard, and document the result like a SOC detection.

## Lab
| Component | Details |
| --- | --- |
| SIEM | Splunk Enterprise 10.4.1 (Free license), index `homelab` |
| Data | Splunk `attack_data`: T1110 Windows Security log, T1087 Sysmon log |
| Add-ons | Splunk Add-on for Microsoft Windows, Splunk Add-on for Sysmon |

## Detection logic
See `daily/day-13.md` for the two SPL searches and their rationale.

## Results
<!-- Screenshots of the searches, the alert configuration and the dashboard; counts observed; false positives found and how they were tuned. -->

## Alert report
<!-- Fill in templates/alert-report.md for one triggered alert. -->

## Lessons learned
-
