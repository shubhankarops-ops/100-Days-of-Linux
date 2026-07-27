# Day 25 - Mega Linux Mission #4: Incident Response (Real Admin Edition)

## Incident Report

Time reported: 9:00 AM
Alert: Production server has files
in the wrong place, some scripts are
not executable, and the backup folder is incomplete.
Assigned to: Junior Linux System Administrator
Task: Inspect the server, decide the
structure myself, investigate, and
restore it to a working state — no predefined
folder layout given.

## Structure Decided

Code

```
Day-25/
├── README.md
├── notes/
│   └── linux_notes.txt
└── Company-Server/
    ├── logs/
    │   ├── access.log
    │   ├── auth.log
    │   ├── system.log
    │   └── old.log
    ├── scripts/
    │   ├── backup.sh
    │   ├── monitor.sh
    │   └── cleanup.sh
    ├── users/
    │   └── employees.txt
    ├── backup/
    │   └── scripts/   (recovered copy)
    ├── reports/
    │   └── report.txt
    └── archive/
```


## Investigation Steps

| Step | Command | Finding |
| :--- | :--- | :--- |
| Locate all logs | `find . -name "*.log"` | 4 files: access.log, auth.log, system.log, old.log |
| Locate all scripts | `find . -name "*.sh"` | 3 files: backup.sh, monitor.sh, cleanup.sh |
| List all files | `find . -type f` | 10 files total |
| List all folders | `find . -type d` | 8 folders total |
| Count ERROR (exact) | `grep -o "ERROR" logs/*.log \| wc -l` | 16 total occurrences |
| Count Linux (exact) | `grep -o "Linux" logs/*.log \| wc -l` | 12 total occurrences |
| Unique technologies | `sort logs/*.log \| uniq` | Cloud, DevSecOps, Docker, ERROR, Git, INFO, Linux, Python, WARNING |
| Extract employee names | `cut -d: -f1 users/employees.txt` | (data gap — see Root Cause below) |
| Extract employee skills | `cut -d: -f2 users/employees.txt` | (data gap — see Root Cause below) |
| Recover missing backup | `cp -r scripts backup` | scripts/ successfully copied into backup/ |
| Toggle execute permission | `chmod +x / chmod -x on monitor.sh` | permission added, verified, removed, re-verified |
| Save incident summary | `echo "..." \| tee -a reports/report.txt` | appended without losing existing report content |
| Review recent activity | `history 20, !<number>` | last 20 commands reviewed, command #611 re-run |


## Root Cause Notes (Self-Review)

• employees.txt was left with only the
header line (Name:Skill) — no
actual employee rows were added,
so cut returned the header text
instead of real data. Fix for next mission:
always populate the file
with real rows before running extraction commands.

• chmod +x was briefly applied to
old.log (a log file, not a
script) before being reverted.
Log files should never need execute
permission in a real system — this was
an unnecessary action, caught
and undone.

• Duplicate-only technology count
was attempted with uniq -c, which
counts every line including ones that appear
only once. The correct
tool for "duplicates only" is uniq -d.

## Final Boss - Reasoning (No Terminal)

Q1: 1 lakh files hon, pehla command kya chalaoge aur kyon?

find . -name "<known-name-or-pattern>" — find 
traverses by matching
name/pattern, so it doesn't
slow down proportionally to how the files are
scattered; it's the right first move
whether there are 100 files or
1,00,000.

Q2: cp aur mv me practical difference? Kaunsa kab?

cp (and cp -r for folders) keeps
the original in place — use it for
backups. mv moves a file to a new
location or renames it — the file no
longer exists at the old path afterward.

Q3: chmod +x dene ke baad bhi script
run na ho — ab kya check karoge?

Permission is already confirmed fine at
this point, so the next checks
are: the shebang line (#!/bin/bash at the
very top of the script), any
syntax errors (bash -n script.sh
checks syntax without running it),
running it explicitly with bash script.sh to
see the actual error
message, and confirming it's being called
with the correct path (./script.sh,
not just script.sh).

Q4: Report overwrite ho gayi thi — tee ya tee -a, kyon?

tee -a — it appends new output to the
file instead of replacing
everything already in it.
Plain tee overwrites the file, which is what
caused the original problem.

Q5: Duplicate technologies ka count
dekhna ho (sirf unique nahi) — approach?

sort file | uniq -d — uniq -d
shows only the lines that appeared more
than once; anything that occurred
just once is hidden. (uniq -c counts
every line's occurrences including the ones
that appeared just once —
not the right tool when only duplicates are needed.)


## Resolution Status

✔ Server structure rebuilt
(self-decided layout, all required folders present)

✔ Logs and scripts isolated and confirmed

✔ ERROR / Linux counts confirmed exactly via wc -l

✔ Unique technologies extracted across all logs combined

✔ Backup folder recovered with cp -r

✔ Script permission toggled and verified twice

✔ Incident summary appended and displayed (tee -a)

✔ Command history reviewed, one past command re-run

⚠ employees.txt data gap identified and
logged for next mission

⚠ uniq -d vs uniq -c distinction still needs
to be internalized


## Status

Mission #4 (Incident Response) — Completed 
with 2 open follow-up items
