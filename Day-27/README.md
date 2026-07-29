# Day 27 - Mega Linux Mission #7: Security Incident Investigation

## Role
Incident Response Team member

## Scenario

Suspicious activity detected on a company server.
Some files matter,
some are noise — deciding what to focus on is
part of the task.

## Server Structure

Code

```
SOC-Server/
├── logs/     auth.log, access.log, serurity.log*, firewall.log
├── scripts/  backup.sh, monitor.sh, scan.sh
├── users/    employees.txt
├── backup/   configs/  (recovered copy)
├── reports/  incident_report.txt
├── archive/
├── temp/     cache.tmp, debug.log
└── configs/  server.conf

  * typo — should be security.log (naming discipline still a work-in-progress)
```

## Investigation Results

| Check | Command | Result |
| :--- | :--- | :--- |
| **.log files** | `find . -name "*.log"` | 5 (4 in logs/, 1 in temp/) |
| **.sh files** | `find . -name "*.sh"` | 3 |
| **.conf files** | `find . -name "*.conf"` | 1 |
| **.tmp files** | `find . -name "*.tmp"` | 1 |
| **Total files** | `find . -type f` | 12 (listed, not yet counted with wc -l — see Open Item below) |
| **Total folders** | `find . -type d` | 8 (same — not yet counted with wc -l) |
| **ERROR count** | `grep "ERROR" logs/*.log \| wc -l` | 11 |
| **WARNING count** | `grep "WARNING" logs/*.log \| wc -l` | 11 |
| **FAILED LOGIN count** | `grep "FAILED LOGIN" logs/*.log \| wc -l` | 9 |
| **SUCCESS LOGIN count** | `grep "SUCCESS LOGIN" logs/*.log \| wc -l` | 9 |
| **SSH count** | `grep "SSH" logs/*.log \| wc -l` | 3 |
| **Unique technologies** | `sort logs/*.log \| uniq` | Cloud, DevSecOps, Docker, ERROR, FAILED LOGIN, Firewall, Git, INFO, Linux, Python, SSH, SUCCESS LOGIN, WARNING |
| **Employee names** | `cut -d: -f1 users/employees.txt` | Raju, Gopal, shubhankar, Lipe, Sam, author, Goku, Deepak, joy, Doli, Jin (11 — one more than the required 10) |
| **Employee skills** | `cut -d: -f2 users/employees.txt` | Linux, Linux engineer, Git, Github, Networ*, Networking, docker, Ai engineer, cloud engineer, cyber security (*typo — "Networ" instead of "Network"/"Networking") |
| **scan.sh permission** | `chmod +x -> ls -l -> chmod -x -> ls -l` | added, verified, removed, re-verified — clean, no typos |
| **Backup** | `cp -r configs backup` | correct direction on the first attempt — improvement over Day 26 |
| **Report** | `echo "..." \| tee -a reports/incident_report.txt` | appended, shown on screen |
| **History review** | `history 25, then !567, !571, !569` | 3 separate past commands re-run |
| **Extra exploration (not required)** | `grep -rn "FAILED LOGIN", grep -rn "Linux"` | recursive search across the whole project, found matches with line numbers — good initiative |



## Bonus Challenge - Resolved

Goal: one pipeline, total count of ERROR +
WARNING + FAILED LOGIN
across all .log files.

What went wrong first:
grep -iE ERROR|WARNING|FAILED LOGIN (no
quotes) — the shell treats
unquoted | as an actual pipe between commands,
not as part of the
regex. That's why "WARNING: command not
found" appeared — the shell
tried to run WARNING as its own command.

Working command:

```
grep -Ei "ERROR|WARNING|FAILED LOGIN" logs/*.log | wc -l
```
Wrapping the pattern in quotes tells the shell
"this whole thing is one
argument" — only then does grep's -E see the
| as a regex OR.


## Open Items (carried into Day 28)

1. Total files/folders still not counted with wc
-l — 4th
consecutive day this has been noted. This
needs to become automatic.

2. Final Boss answers not yet submitted for
Day 27's actual questions
(200-file FAILED LOGIN search, find
-type vs -name, chmod 755 vs +x,
post-delete backup verification,
ranking repeated entries by
frequency) — hints given, awaiting a real attempt.

3. Minor data hygiene: employees.txt has 11
entries (spec asked for 10)
and one typo ("Networ"); serurity.log
filename typo persists from
the naming-discipline lesson in Day 26.


## Status
Mission #7 — Investigation and Bonus
Challenge complete; Final Boss pending
