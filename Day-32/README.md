# Day 32 - Backup & Archive Lab

## Role

Linux Administrator — backup and recovery
focus

## Server Structure

Code

```
backup-Lab/
├── configs/   server.conf, network.conf
├── logs/      auth.log, access.log, system.log
├── scripts/   backup.sh, monitor.sh
├── backup/    configs/  (recovered copy)
├── archive/
├── reports/   backup_report.txt
├── temp/
└── users/     employees.txt
```

## Investigation Results

| Task | Command | Result |
| :--- | :--- | :--- |
| Total files | `find . -type f` | 9 |
| Total folders | `find . -type d` | 8 |
| .sh files | `find . -name "*.sh"` | backup.sh, monitor.sh |
| .log files | `find . -name "*.log"` | 3 |
| .conf files (incl. backup copy) | `find . -name "*.conf"` | 4 (2 original + 2 in backup/) |
| ERROR count | `grep -o "ERROR" logs/*.log \| wc -l` | 10 |
| Linux match count | `grep -n "Linux" logs/*.log \| wc -l` | 10 |
| ERROR ranked by file | `grep "ERROR" logs/*.log \| sort \| uniq -c \| sort -nr` | system.log: 5, access.log: 5 |
| First lines of a log | `head logs/access.log` | ERROR, WARNING, INFO, FAILED LOGIN, SUCCESS LOGIN, SSH... |
| Multi-file head | `head -8 logs/*.log` | shows $\Longrightarrow$ filename $\Longleftarrow$ headers per file |
| Multi-file tail (fixed) | `tail -n 6 logs/*.log` | last 6 lines per file, correctly labeled |
| Unique values in a log | `sort logs/access.log \| uniq` | Docker, ERROR, FAILED LOGIN, INFO, Linux, Python, SSH, SUCCESS LOGIN, WARNING |
| Files containing a match | `grep -l "ERROR" logs/*.log` | all 3 logs — self-discovered, not previously taught |
| Files NOT containing a match | `grep -L "ERROR" logs/*.log` | none (all logs contain ERROR) |
| Backup | `cp -r configs backup` | correct, verified with ls/ls configs inside backup/ |
| monitor.sh permission | `chmod +x -> ls -l -> chmod -x -> ls -l` | clean, no typos |


## Data Note

logs/auth.log was initially left with
only "yy" (a stray nano typo,
not real data). Recovered creatively with:

Code

```
sort logs/access.log | uniq | tee -a logs/auth.log
```

which appended access.log's unique value list
into auth.log. Functional
fix, but for next time: fill every log file with its
own distinct data
from the start rather than borrowing from
another file afterward.

## Mistake Log (self-corrected during the session)

• sort logs/access.log | uniq -nr →
invalid: -n and -r belong to
sort, not uniq. Frequency ranking always
needs two commands:
sort file | uniq -c | sort -nr.

• tail -6 logs/*.log → "option used in
invalid context"; fixed with
the explicit form tail -n 6 logs/*.log.

## Q1–Q5: Real Linux Admin Questions

Q1: Sirf .conf files ka backup lena ho —
approach aur kyon?

Code

```
cp configs/*.conf backup/
```

The wildcard selects every .conf file and
copies it directly into
backup/ in one command — no need to name
each file individually.
(Corrected during review — the submitted
command,
find . -name "*.conf" | tee -a backup,
failed with "tee: backup: Is
a directory," and even if it hadn't, it would only
have listed
filenames, not actually copied anything.)

Q2: auth.log ki pehli 10 lines aur last 10 lines
dekhni hain?

Code

```
head -10 logs/auth.log
tail -10 logs/auth.log
```

Correct as submitted — two separate,
purpose-built commands.

Q3: configs folder accidentally delete ho gaya
— restore se pehle kya verify karoge?

Verify the backup's own completeness, not
the original's fate (it's
already gone by the time this question
applies):

Code

```
ls -R backup/configs
ls configs 2>/dev/null | wc -l   # (won't work post-deletion, so compare
                                    against a file-count noted at backup time)
```

The key check is whether backup/configs/
actually has everything that
was expected — matching file names/count —
before trusting it enough
to restore from.
(Corrected during review — same category of
mix-up as Day 31 Q2:
investigating what happened to the original is
a different question
from confirming the backup itself is
trustworthy.)

Q4: Sirf un log files ke naam chahiye jisme
FAILED LOGIN hai?

Code

```
grep -l "FAILED LOGIN" logs/*.log
```

(Corrected during review — the right flag was
used, -l, but the
demonstrated command searched for
"ERROR" instead of "FAILED LOGIN.")

Q5: Report file me save bhi karna ho, screen
par bhi dekhna ho?

tee -a — appends to the file while still
printing to the screen, so
nothing has to be checked twice.

## Status

Mission #12 (Backup & Archive Lab) —
Completed, 3 answers corrected on review
