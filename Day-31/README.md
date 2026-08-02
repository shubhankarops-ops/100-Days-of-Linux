# Day 31 - Log Investigation Lab

## Role

Security Log Investigator

## Server Structure

Code

```
Security-Lab/
├── logs/      auth.log, access.log, firewall.log, system.log
├── scripts/   backup.sh, monitor.sh, cleanup.sh
├── configs/   server.conf, network.conf
├── users/     employees.txt
├── reports/   security_report.txt
├── backup/    (empty — see Open Item below)
├── archive/
└── temp/
```

## Investigation Results

| Task | Command | Result |
| :--- | :--- | :--- |
| .log files | `find . -name "*.log"` | 4 files |
| Total files | `find . -type f` | 11 (listed) |
| Total folders | `find . -type d` | 8 |
| ERROR count | `grep -o "ERROR" logs/*.log \| wc -l` | 45 |
| FAILED LOGIN per file (ranked) | `grep "FAILED LOGIN" logs/*.log \| sort \| uniq -c \| sort -nr` | firewall.log: 12, system.log: 8, auth.log: 3, access.log: 3 |
| Linux per file (ranked) | `grep "Linux" logs/*.log \| sort \| uniq -c \| sort -nr` | firewall.log: 12, system.log: 8, auth.log: 3, access.log: 3 |
| Structure verification | `ls -R \| tee -a reports/security_report.txt` | full tree saved and displayed |
| Current directory | `pwd \| tee -a reports/security_report.txt` | saved and displayed |
| backup.sh permission | `chmod +x -> ls -l -> chmod -x -> ls -l` (repeated via history recall) | clean, no typos |
| History review | `history 6, then !550-!553` | 4 past commands re-run |


## Open Item — Backup Never Actually Created

ls backup returned empty — no cp -r was
run against the backup
folder this session, so there's nothing in it to
verify yet. Next step:

Code

```
cp -r configs backup
ls -R backup
```

## Q1–Q5: Real Admin Questions

Q1: FAILED LOGIN sabse zyada kis log file
me hua? Prove karo.

Code

```
grep "FAILED LOGIN" logs/*.log | sort | uniq -c | sort -nr
```

Result: firewall.log — 12 occurrences,
highest of all four files.
This works because grep (without -n) prints
filename:match for
every hit; identical file:FAILED LOGIN lines
then get counted by
uniq -c and ranked by sort -nr — one
pipeline, a ranked proof.

Q2: server.conf accidentally delete ho jaye —
backup sahi hai confirm karne ke liye pehli
command?

ls backup/configs/server.conf — confirms
the backup copy actually
exists. For a stronger check while the original
still exists:
diff configs/server.conf backup/
configs/server.conf confirms the
content matches exactly, not just that a file
with the right name is
present.
(Corrected during review — original answer
described investigating
the deletion via history, which answers "who/
how was it deleted," not
"is the backup valid.")

Q3: "Linux" kitni baar aaya — kaunsa
command aur kyun?

Code

```
grep "Linux" logs/*.log | sort | uniq -c | sort -nr
```
Gives an exact, per-file count ranked from
highest to lowest — more
useful during an investigation than a single
combined total, since it
also shows where the activity is concentrated.

Q4: 50 GB log file — nano khologe ya head/
tail/grep? Reason?

Never nano — it tries to load the whole file
into memory, which would
hang or crash on a file that size. head/tail are
safe for a quick
look at the start or end. grep is the right tool
for finding specific
content anywhere inside the file, because it
streams through line by
line instead of loading everything at once — it
doesn't replace
head/tail, it solves a different part of the same
problem (searching
vs. quick-glancing).
(Expanded during review — original answer
ruled out grep entirely,
reasoning it was only for "viewing," missing
its actual strength: safe
searching through huge files.)

Q5: Sirf error report bachani ho aur screen
par bhi dekhni ho — approach?

tee -a — appends the output to a file while
still printing it to the
screen, so nothing needs to be checked twice.

## Status

Mission #11 (Log Investigation Lab) —
Completed; backup step still pending
