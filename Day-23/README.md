# Day 23 - Mega Linux Mission #4
## Role 
Junior Linux System Administrator
## Goal
Build and investigate a simulated company
server structure using file management,
permissions, and text-processing commands — no hints,
no notes, pure reasoning.
## Project Structure
Code

```
Company-Server/
├── logs/
│   ├── access.log
│   ├── auth.log
│   └── security.log
├── users/
│   └── employees.log
├── scripts/
│   ├── backup.sh
│   ├── monitor.sh
│   └── cleanup.sh
├── backup/
└── reports/
    └── report.txt
```


## Commands Practiced

ls, ls -R
find . -name "*.sh"
find . -name "*.log"
find . -name "*.txt"
grep "ERROR" 
grep "Linux" 
sort 
cut -d: -f1 / -f2
chmod +x / chmod 740 / chmod -x
ls -l (permission verification)
echo "..." | tee 
history N
!<command_number> (history recall)
## New Commands (self-taught)

grep -o "ERROR" logs/*.log | wc -l →
total ERROR count across multiple files
grep -c "ERROR" logs/*.log → ERROR count per file
## Investigation Results

1.log files found: access.log, auth.log,
  security.log, employees.log

2.sh files found: backup.sh, monitor.sh, cleanup.sh

3.ERROR occurrences: 3 in access.log,
3 in auth.log, 3 in security.log

4.Linux occurrences: 3 in access.log,
0 in auth.log, 3 in security.log

5.Employee names
(cut -d: -f1): Rahul, Aman, Riya, Neha, Arjun, Sara

6.Employee skills
(cut -d: -f2): Linux, Docker, Python, Cloud, DevSecOps, Git

7.monitor.sh: execute permission added
(chmod +x), verified (ls -l),
then removed (chmod -x), verified again

8.Report written and displayed on screen using tee

9.Last 12 commands reviewed with
history 12; re-ran command #577 using !577

## Final Boss - Reasoning (No Terminal)
Q1: 500 log files hon to sirf .shfiles kaise dhoondoge?

find . -name "*.sh" — filters strictly
by the .sh extension, ignoring every .log
file no matter how many exist.

Q2: sort | uniq vs sort | uniq -c?

sort | uniq sorts alphabetically and removes
duplicate lines. sort | uniq -c does the
same but also prefixes each unique
line with how many times it appeared.

Q3: tee vs >?

tee shows output on screen AND writes it
to a file. > only writes to the
file — nothing shown on screen.

Q4: cp vs mv — kab use karoge?

cp when the original should stay in place
(e.g. backups: cp -r folder folder_backup).
mv when moving a file to a different folder,
or renaming it — the original location
no longer holds the file.

Q5: Script run nahi ho raha to pehle kya check karoge?

ls -l to check whether the execute
(x) permission is set. If missing,
chmod +x <script> before running again.

## Mission Completed

✔ Company-Server structure created
✔ logs, users, scripts, backup, reports folders set up
✔ ERROR / Linux occurrences investigated across all logs
✔ Employee names & skills extracted with cut
✔ monitor.sh execute permission added, verified, and removed
✔ Report generated (screen + file) with tee
✔ History reviewed and a past command re-run

##Status
Mission #4 Completed Successfully 🚀
