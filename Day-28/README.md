# Day 28 - SOC Incident Investigation (Level Up)

# Role
SOC (Security Operations Center) Linux Administrator

## Scenario
A suspicious login occurred. Investigate and
collect evidence.

## Server Structure

Code

``` 
Incident-Server/
├── logs/         access.log, auth.log, ssh.log, system.log
├── scripts/      backup.sh, monitor.sh, scan.sh
├── users/        users.txt
├── reports/      incident_report.txt
├── backup/       configs/  (recovered copy)
├── quarantine/   suspicious.txt
├── configs/      server.conf
└── archive/
```

## Setup Note (real troubleshooting moment)

Files were initially created in the wrong
folders (system.log inside
scripts/, then renamed to system.sh by
mistake, then backup.sh
bounced between logs/ and scripts/).
Resolved through a sequence of
mv commands until every file landed in its
correct folder. Messy in
the moment, but this is exactly the kind of
correction a real admin does
constantly — the key skill is noticing the
mistake via ls and fixing
it, which happened here.


## Investigation Results

| Check | Command | Result |
| :--- | :--- | :--- |
| .log files | `find . -name "*.log"` | 4: access.log, auth.log, ssh.log, system.log |
| .conf files | `find . -name "*.conf"` | 1: server.conf |
| .txt files | `find . -name "*.txt"` | 3: users.txt, incident_report.txt, suspicious.txt |
| Empty files | `find . -type f -empty` | system.log, backup.sh, monitor.sh, scan.sh, users.txt, incident_report.txt, suspicious.txt, server.conf (never filled with data) |
| First 5 lines of a log | `head -5 logs/access.log` | ERROR, WARNING, INFO, FAILED LOGIN, SUCCESS LOGIN |
| Last 5 lines of a log | `tail -5 logs/auth.log` | FAILED LOGIN, ERROR, WARNING, INFO, FAILED LOGIN |
| FAILED LOGIN matches | `grep -nr "FAILED LOGIN" logs/*.log` | found in access.log (x2), auth.log (x4), ssh.log (x5) — not yet totaled with wc -l |
| SUCCESS LOGIN matches | `grep -nr "SUCCESS LOGIN" logs/*.log` | found in access.log (x3), auth.log (x1), ssh.log (x1) — not yet totaled |
| SSH matches | `grep -nr "SSH" logs/*.log` | 1 match per file (3 total) — not yet totaled with wc -l |
| Linux matches | `grep -nr "Linux" logs/*.log` | 1 match per file (3 total) — not yet totaled with wc -l |
| Unique technologies | `sort logs/*.log \| uniq` | DevSecOps, Docker, ERROR, FAILED LOGIN, INFO, Linux, Python, SSH, SUCCESS LOGIN, WARNING — clean, no typos |
| Backup | `cp -r configs backup` | correct direction, first attempt (3rd day in a row — habit locked in) |
| Backup verify | `ls inside backup/configs` | confirmed server.conf present |
| scan.sh permission | `chmod +x -> ls -l -> chmod -x -> ls -l` | added, verified, removed, re-verified — clean |
| History review | `history 20, then !577 and !575` | 2 past commands re-run as required |


## Final Boss - Reasoning (No Terminal)

Q1: head aur tail ka practical use production
server me kab?

When a log file has thousands/lakhs of lines
and opening the whole thing
is impractical: head gives a quick look at the
format/start of a file,
tail shows the most recent entries — the ones
most likely relevant
during an active incident.

Q2: grep -n aur grep -rn me kya difference hai?

grep -n "pattern" logs/*.log searches
only the files matched by the
glob, in that one directory — with
line numbers.
grep -rn "pattern" . searches recursively
through every
subdirectory from the given path downward,
also with line numbers —
essential when file locations aren't known in
advance.
(Corrected during review — originally
answered "no difference".)

Q3: Empty files dhoondhne ke liye kaunsi command?

find . -type f -empty — filters to files (not
directories) that have
zero bytes.

Q4: Sirf naya add hui lines dekhni ho — approach?

tail -f logfile — follows the file live,
printing new lines the moment
they're written. Plain tail only gives a fixed,
one-time snapshot of
the last N lines; -f is what's needed for
continuously monitoring new
activity, which matters most during an active
incident.
(Corrected during review — originally
answered plain "tail".)

Q5: 1 lakh files ho to find ya ls -R, kaunsa aur kyon?

find — it can filter directly by name/pattern
and return only the
matching files, without ever printing the rest.
ls -R recursively
lists everything in every folder, which at that
scale would flood the
screen with irrelevant output.

## Open Items (carried forward)

1. FAILED LOGIN / SUCCESS LOGIN / SSH /
Linux counts still not finalized
with wc -l — same underlying habit gap
as the earlier files/folders
count, now showing up in content searches
too.

2. Day 27's Final Boss questions are still
unanswered (hints were given,
real answers pending).

## Status
Mission #8 (SOC Incident Investigation) —
Completed, 2 open items carried forward
