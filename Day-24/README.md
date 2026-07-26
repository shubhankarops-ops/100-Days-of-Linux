# Day 24 - Mega Linux Mission #5

##Scenario: Production Server Recovery

A company server crashed and has been recovered.
Files are scattered across
directories. Task: inspect the server,
investigate the mess, and produce a
report — without being told which commands to use.

##Role

Junior Linux System Administrator

##Project Structure

Code

```
Recovery-Server/
├── logs/
│   ├── app.log
│   ├── auth.log
│   └── network.log
├── scripts/
│   ├── backup.sh
│   ├── monitor.sh
│   └── cleanup.sh
├── users/
│   └── staff.txt
├── backup/
├── reports/
│   └── final_report.txt
└── temp/
    ├── old.log
    └── test.tmp
```

## Commands Used - and Why

| Task | Command | Why this one |
| :--- | :--- | :--- |
| Find all .log files | `find . -name "*.log"` | Matches by extension regardless of folder depth — catches temp/old.log too, which a simple ls inside logs/ would miss |
| Find all .sh files | `find . -name "*.sh"` | Same reasoning — extension-based search works no matter how many other file types are mixed in |
| Count all files | `find . -type f` | -type f filters out directories, leaving only real files |
| Count all folders | `find . -type d` | -type d filters out files, leaving only directories |
| Count ERROR total | `grep -o "ERROR" logs/*.log \| wc -l` | -o prints one match per occurrence (not per line), so wc -l gives an exact total across all files |
| Count ERROR per file | `grep -c "ERROR" logs/*.log` | -c gives a count per file, useful when you need to know which log is the noisiest |
| Unique technologies | `sort file \| uniq` | Alphabetizes first so identical lines sit next to each other, then uniq removes the repeats |
| Extract names | `cut -d: -f1 users/staff.txt` | staff.txt is colon-delimited; field 1 is always the name |
| Extract skills | `cut -d: -f2 users/staff.txt` | Field 2 is always the skill |
| Toggle execute permission | `chmod +x` / `chmod -x` | Adds/removes only the execute bit, leaving read/write untouched |
| Verify permission change | `ls -l` | Shows the full permission string (owner/group/other) to confirm the change actually applied |
| Save + display report | `echo "..." \| tee -a file` | -a appends instead of overwriting, so earlier report content isn't lost |
| Review recent commands | `history 15` | Shows the last 15 commands with their numbers |
| Re-run a past command | `!<number>` | Recalls and re-executes a specific line from history without retyping it |


##Investigation Results

1. .log files found: app.log, auth.log,
   network.log, temp/old.log

2. .sh files found: backup.sh, monitor.sh, cleanup.sh

3. Total files: 11 (via find . -type f)

4. Total folders: 8 (via find . -type d, including root .)

5. ERROR total: 11 (app.log: 3, auth.log: 5, network.log: 3)

6. Linux occurrences: found in app.log
and network.log (not counted with wc -l this
time — next mission, apply the same
grep -o | wc -l pattern used for ERROR)

7. Unique technologies per log (via sort | uniq):
  
   • app.log: Cloud, Docker, ERROR, Git,
     INFO, Linux, Python, WARNING
   
   • auth.log: ERROR, Login, Logout, WARNING
  
   • network.log: ERROR, Firewall, INFO, Linux, SSH

8. Staff names (cut -d: -f1): Rahul, Aman, Riya,
   Neha, Arjun, Sara, Rohit

9. Staff skills (cut -d: -f2): Linux, Docker
   Python, Cloud, DevSecOps, Git, Networking

10. monitor.sh: execute permission added,
    verified, then removed and re-verified

11. Report summary appended to final_report.txt
    and shown on screen via tee -a

12. Last 15 commands reviewed; re-ran command #553
    (ls -R) via !553


## Final Boss - Reasoning (No Terminal)

Q1: 10,000 files hon, ek specific file kaise
    dhoondoge? Kyun?

find . -name "filename" — matching is by name/extension,
not by scrolling through folders manually,
so the search cost doesn't grow painfully with
file count. For a system-wide search:
find / -name "filename" 2>/dev/null.

Q2: head aur tail ek Linux Admin kab use karta hai?

Jab file mein hazaaron lines ho aur pura
load karna impractical ho. head file ke shuru
ki lines dikhata hai (default 10) — quick check
ke liye ki file kis format mein hai.
tail end ki lines dikhata hai — logs check
karne ke liye sabse common, kyunki latest
events hamesha end mein hote hain.
tail -f file.log real-time mein naye log
entries live dekhne ke liye use hota hai
(production monitoring mein bahut common).

Q3: chmod +x aur chmod 740 mein kya difference hai?

chmod +x sirf execute bit ko toggle karta
hai, baaki permissions (read/write)
already jo hain wahi rehte hain.

chmod 740 poora permission set ek saath
fix kar deta hai: owner = rwx (7),
group = r-- (4), others = --- (0,
koi permission nahi). Yeh replace karta hai,
sirf ek bit change nahi karta.

Q4: File ka naam/path yaad nahi,
    sirf extension yaad hai — pehla step?

find . -name "*.txt" (ya jo bhi extension yaad hai)
— current directory se neeche har jagah
search karega bina exact path jaane.

Q5: Sirf duplicate technologies dekhni
    ho (unique nahi) — approach?

sort file | uniq -d — -d flag sirf un lines ko
dikhata hai jo kam se kam do baar repeat hui hon;
jo sirf ek baar aayi wo hide ho jaati hain.
Ulta chahiye ho (sirf non-repeated) to uniq -u.


## Mission Completed

✔ Recovery-Server structure created (7 folders)

✔ Logs, scripts, users, reports, temp files populated

✔ .log and .sh files isolated via find

✔ ERROR/Linux occurrences investigated

✔ Unique technologies extracted correctly
  with sort | uniq (improved from Day 23)

✔ Staff names & skills extracted with cut

✔ monitor.sh execute permission toggled and verified twice

✔ Report appended and displayed with tee -a

✔ History reviewed and a past command re-run

## Status
Mission #5 Completed Successfully 🚀

## Self-Correction Notes (for next mission)

Always pipe count-style greps through
wc -l for an exact total, not just a visual scan
Remember uniq -d / uniq -u for
duplicate vs unique filtering — don't stop
at plain sort | uniq
chmod numeric mode (e.g. 740) replaces the whole
permission set — treat it differently from chmod +x/-x
