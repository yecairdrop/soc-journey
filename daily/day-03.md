# Day 03 – Linux Fundamentals & Log Analysis

**Date:** 2026-10 · **Status:** ✅ Done

## Sources
- TryHackMe – Linux Fundamentals 1–3 · OverTheWire Bandit (levels 0–12)
- Loghub sample logs (`OpenSSH_2k.log`)

## What I learned

### Why Linux matters for a SOC analyst
- Most servers, cloud instances, firewalls and security tools run Linux. Log analysis on the command line is a daily task.
- macOS is Unix-based, so almost every command below works in the Mac Terminal too.

### Filesystem map
| Path | What lives there |
| --- | --- |
| `/etc` | Configuration files (`/etc/passwd` users, `/etc/shadow` password hashes, `/etc/ssh/sshd_config`) |
| `/var/log` | **Logs** – the SOC analyst's main directory |
| `/home/<user>` | User home directories (`~`) |
| `/tmp` | Temporary files; attackers love dropping tools here |
| `/bin`, `/usr/bin` | Programs |
| `/root` | The administrator's home |

### Key log files
| File | Content |
| --- | --- |
| `/var/log/auth.log` (Debian/Ubuntu) · `/var/log/secure` (RHEL) | Logins, SSH, `sudo` usage, user changes |
| `/var/log/syslog` · `/var/log/messages` | General system messages |
| `journalctl` | systemd journal: `journalctl -u ssh`, `journalctl --since "1 hour ago"` |
| `/var/log/apache2/access.log` · `/var/log/nginx/access.log` | Web server requests |
| `~/.bash_history` | Commands a user typed |

### Commands for reading logs
| Command | Purpose | Example |
| --- | --- | --- |
| `cat` / `less` | Print / page through a file | `less auth.log` (`/` to search, `q` to quit) |
| `head` / `tail` | First / last lines | `tail -f auth.log` follows live |
| `grep` | Filter lines | `grep -i "failed" auth.log`, `-v` invert, `-c` count, `-E` regex, `-o` only the match |
| `cut` | Pick a column | `cut -d' ' -f1` |
| `awk` | Column processing | `awk '{print $11}'` prints the 11th word |
| `sort` / `uniq -c` | Count repeated values (`uniq` needs sorted input) | `sort \| uniq -c \| sort -nr` |
| `wc -l` | Count lines | `grep "Failed" auth.log \| wc -l` |
| `find` | Locate files | `find / -mmin -10 -type f` (changed in the last 10 min) |

**The core pipeline** – find brute-forcing IPs:
```bash
grep "Failed password" OpenSSH_2k.log | grep -oE "from [0-9.]+" | sort | uniq -c | sort -nr | head
```
1. `grep` keeps failed logins → 2. `grep -oE` extracts the IP → 3. `sort | uniq -c` counts each IP → 4. `sort -nr` biggest first → 5. `head` top 10.
This is exactly `stats count by src_ip | sort -count` in Splunk.

### Users, permissions, processes
- `whoami`, `id`, `cat /etc/passwd` (one line per user; `/bin/false` or `nologin` shells = service accounts).
- Permissions `rwx` for **owner / group / others**: `-rwxr-xr--`. `chmod 600 file` = only the owner reads/writes. `chmod +x` = executable.
- `sudo` = run as root; every use is logged in `auth.log`.
- Processes: `ps aux`, `top`; network: `ss -tulnp` (listening ports + the process behind them), `ip a` (interfaces).
- Scheduled jobs: `crontab -l`, `/etc/cron.*` – a classic **persistence** location.

## Hands-on (lab exercise)
1. Download `OpenSSH_2k.log` from Loghub and run the pipeline above. Record the top 3 IPs and their counts.
2. Count successful vs. failed logins: `grep -c "Accepted" OpenSSH_2k.log` vs `grep -c "Failed password"`.
3. List usernames attackers tried: `grep "Failed password" OpenSSH_2k.log | grep -oE "for (invalid user )?\S+" | sort | uniq -c | sort -nr | head`.

## SOC takeaway
- `auth.log` answers: who logged in, from where, did `sudo` get used, were users created (`useradd`) or added to groups.
- Suspicious patterns: many `Failed password` then one `Accepted` from the same IP; logins at odd hours; new user accounts; new cron jobs; executables in `/tmp`.
- `ss -tulnp` shows an unexpected listening port → possible backdoor.

## Questions / next
- Windows Event Logs (Day 04).
