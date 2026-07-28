# Day 26 - Mega Linux Mission #6: Security Audit

## Role
Linux Security Auditor

## Scope

Audit-Server — a simulated production
environment with logs, scripts,
user records, backups, reports,
and a temp directory. Combine multiple
commands per task rather than settling
for the first one that works.

## Server Structure

Code

```
Audit-Server/
├── logs/       access.log, auth.log, kernel.log, secuity.log*
├── scripts/    backup.sh, monitor.sh, updata.sh*
├── users/      employees.txt
├── backup/     scripts/  (recovered copy)
├── reports/    andit_report.txt*
├── archive/
└── temp/       cache.tmp, debuy.log*

  * naming inconsistency found during audit — see Finding 1
```

## Audit Findings

## Finding 1 — Filename inconsistencies (Medium)

security.log, update.sh, audit_report.txt
and debug.log were
created with typos: secuity.log, updata.sh,
andit_report.txt,
debuy.log. In a real environment, any
automation or monitoring script
referencing the correct name would
silently fail to find these files.
Remediation: verify filenames against spec before running any
investigation or automation against them.

## Finding 2 — Data contamination in log content (Low)

One log entry contains "INFOO" instead
of "INFO". Because

grep -o "INFO" matches substrings,
not whole words, it still counts
"INFOO" as a match — inflating the true INFO
count and appearing as a
false extra entry in the unique-technologies
list.
Remediation: use grep -ow
"INFO" (word-boundary match) when exact
term counting matters, and spot-check sort |
uniq output for anomalies
before trusting it.

## Finding 3 — Backup direction error (self-corrected during audit)

An initial cp -r backup scripts copied the
(still-empty) backup folder
into scripts instead of the other way round.
Caught immediately via
ls -R, reverted with rm -r scripts/backup,
and corrected with
cp -r scripts backup. No data loss occurred.
Outcome: backup/scripts/ now correctly
contains backup.sh,
monitor.sh, updata.sh.

## Investigation Results

| Check | Command | Result |
| :--- | :--- | :--- |
| **.log files** | `find . -name "*.log"` | 5 (4 in logs/, 1 in temp/) |
| **.sh files** | `find . -name "*.sh"` | 3 |
| **.tmp files** | `find . -name "*.tmp"` | 1 |
| **Total files** | `find . -type f \| wc -l` | 10 |
| **Total folders** | `find . -type d \| wc -l` | 7 |
| **ERROR total** | `grep -o "ERROR" logs/*.log \| wc -l` | 27 |
| **WARNING total** | `grep -o "WARNING" logs/*.log \| wc -l` | 19 |
| **INFO total** | `grep -o "INFO" logs/*.log \| wc -l` | 26 (inflated by "INFOO" — see Finding 2) |
| **Linux count** | *(per-file scan across logs)* | present in all 4 logs |
| **SSH total** | `grep -c "SSH" logs/*.log` | access:1, auth:1, kernel:0, security:2 = 4 |
| **Unique technologies** | `sort logs/*.log \| uniq` | Cloud, DevSecOps, Docker, ERROR, Git, INFO, INFOO*, Linux, Python, SSH, WARNING |
| **Employee names** | `cut -d: -f1 users/employees.txt` | Doli, Ramesh, Raj, shubhankar, Raju, Lipe, joy, Aakash (8) |
| **Employee skills** | `cut -d: -f2 users/employees.txt` | Linux, Git, Docker, Python, Networking, and others |
| **monitor.sh permission** | `chmod +x` &rarr; `ls -l` &rarr; `chmod -x` &rarr; `ls -l` | added, verified, removed, re-verified |
| **Backup** | `cp -r scripts backup` *(after correcting direction)* | backup/scripts/ populated correctly |
| **Report** | `echo "..." \| tee -a reports/audit_report.txt` | appended, shown on screen |
| **History review** | `history 20`, then `!538` and `!531` | two separate past commands re-run |


## Bonus Challenge

Single pipeline for total ERROR count across
all .log files:

grep -o "ERROR" logs/*.log | wc -l → 27
(Already satisfied as part of the ERROR
investigation step above.)


## Final Boss - Reasoning (No Terminal)

Q1: 1 lakh files, sirf .log files dhoondhni hain
— approach aur kyon?

find . -name "*.log" -type f — find
matches by name pattern in a single
tree traversal; it doesn't need to load every file
into memory or open
each one, so it scales cleanly whether there
are 100 or 1,00,000 files.

Q2: grep -c vs grep -o | wc -l — practical
difference?

grep -c counts matching lines per file — if a
pattern appears
twice on the same line, that line is still counted
once. grep -o | wc -l
counts every individual occurrence, so the
same line would contribute
2 to the total. Use -c for a per-file line-count
summary, use
-o | wc -l when the exact number of
matches matters.

Q3: cp -r vs cp — difference?

cp copies a single file. cp -r copies a folder
and everything inside
it recursively — required whenever the source
is a directory.

Q4: Execute permission set but script still
won't run — what next?

Check the script's content is actually correct,
confirm the file isn't
empty, and confirm the correct path is being
used (./script.sh). Beyond
that: check the shebang line (#!/bin/bash)
and run bash -n script.sh
to catch syntax errors without executing
anything.

Q5: Duplicate entries + their count —
approach and why?

sort file | uniq -cd — uniq flags can be
combined. -d restricts the
output to lines that repeated (true duplicates
only), and -c adds the
count for each. Using -c alone would also
show lines that appeared just
once, which isn't what "duplicates" means.


## Status

Mission #6 (Security Audit) — Completed, 3
findings logged and remediated/noted
