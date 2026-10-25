# Day 10 – Core SPL

**Date:** 2026-10 · **Status:** ✅ Done

## Sources
- Splunk Education – Statistical Processing, Working with Time · TryHackMe – Investigating with Splunk · Splunk Search Reference

## What I learned

### The search pipeline
```
index=homelab sourcetype=WinEventLog:Security EventCode=4625   ← filter (search terms)
| stats count by TargetUserName, IpAddress                     ← transform
| where count > 5                                              ← filter the table
| sort -count                                                  ← order
```
- Everything before the first `|` is the **base search**: index, sourcetype, field=value terms, free-text in quotes. Put the most selective terms first.
- Each `|` passes results to the next command. Order matters.

### Commands I use daily
| Command | Does | Example |
| --- | --- | --- |
| `stats` | Aggregate | `stats count, dc(IpAddress) as sources by TargetUserName` |
| `table` | Pick columns | `table _time host user src` |
| `sort` | Order (`-` = descending) | `sort -count` |
| `head` / `tail` | First/last N | `head 20` |
| `dedup` | Remove duplicates | `dedup user` |
| `top` / `rare` | Most/least common values | `top limit=10 Image` |
| `eval` | Compute new fields | `eval user=lower(TargetUserName)` |
| `where` | Filter on computed values | `where count >= 5` |
| `rename` | Rename a field | `rename IpAddress as src_ip` |
| `rex` | Extract with regex | `rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"` |
| `timechart` | Count over time | `timechart span=1h count by EventCode` |
| `bin` | Group `_time` into buckets | `bin _time span=5m` |
| `transaction` | Group related events | `transaction src_ip maxspan=10m` |
| `fillnull` | Replace empty values | `fillnull value="-"` |
| `lookup` | Enrich from a CSV | `lookup asset_list ip as src_ip OUTPUT owner` |

### `stats` functions
`count`, `dc()` (distinct count), `values()` (unique list), `list()`, `sum()`, `avg()`, `min()`, `max()`, `earliest()`, `latest()`.

### `eval` functions worth knowing
`if(cond, a, b)`, `case(...)`, `lower()`, `upper()`, `len()`, `coalesce(a, b)` (first non-empty), `mvindex(field, n)` (pick one value from a multivalue field), `strftime(_time, "%H:%M")`, `round()`.

### `search` vs `where`
- `search` / base terms: text matching, wildcards (`user=adm*`), fast.
- `where`: expressions and comparisons between fields (`where fails > logins`), runs after `stats`.

### Time
- `earliest=-24h latest=now` in the search, or the picker.
- `timechart span=1h count` builds a per-hour series; `bin _time span=5m | stats count by _time, user` is the building block of threshold alerts.

### Good habits
- Start narrow: `index=` + `sourcetype=` + a time window.
- Use `| head 10` while developing, remove it at the end.
- Name computed fields clearly (`as fails`).
- Save useful searches as **Reports** (Free license) or **Alerts** (Enterprise/Cloud).

## Hands-on (lab exercise)
Run the eight queries from the plan's SPL practice section (S1–S8) against `homelab`; for each, change one thing (the grouping field, the span, the threshold) and note what changes.

## SOC takeaway
- Almost every detection is `filter → bin by time → stats count by X → where count > N`. Learn that shape and you can write most L1 alerts.
- `dc()` catches **spraying**: one IP, many usernames (`dc(user) > 20`), where plain `count` would not.

## Questions / next
- Alert triage and reporting (Day 11).
