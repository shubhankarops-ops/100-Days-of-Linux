# Day 29 - Wildcards & Pattern Matching

## Theme
First "real admin skill" mission — handling
many files with one command
using pattern matching instead of repeating
commands per file.

## Server Structure

Code

``` 
Pattern-Lab/
├── logs/     auth.log, app.log, kernel.log (renamed from system.log),
│             error.log, access.log, debug.log
├── configs/  server.conf, network.conf, ssh.conf
├── scripts/  backup.sh, monitor.sh, scan.sh, cleanup.sh
├── temp/     cache.tmp, old.tmp, test.tmp
├── reports/  daily.txt, weekly_report.txt (renamed from weekly.txt)
└── backup/   configs/  (recovered copy — see note below)
```

## Wildcard Reference (today's core lesson)

| Symbol | Meaning | Example | Matches |
| :--- | :--- | :--- | :--- |
| `*` | zero or more of ANY character | `*.log` | any file ending in `.log` |
| `*` | (prefix use) | `a*` | anything starting with "a" |
| `*` | (both ends) | `a*.log` | starts with "a" AND ends in .log |
| `?` | exactly ONE character | `*.???` | files with a 3-character extension |
| `[]` | one character from a set | `file[123].txt` | file1.txt, file2.txt, file3.txt |


Critical rule: the wildcard must go inside
 -name with quotes for
find — find . -name "*.txt" is correct;
passing the pattern as a
bare argument (find . -type f "*.txt") is
invalid syntax, since find
then treats it as a path, not a name pattern.

Also critical: .conf (no asterisk) is NOT a
 wildcard — it's a literal
filename. Only *.conf actually expands to
match multiple files.


## Investigation Results

| Task | Command | Result |
| :--- | :--- | :--- |
| .log files | `find . -name "*.log"` | 6 files |
| .conf files | `find . -name "*.conf"` | 3 files |
| .sh files | `find . -name "*.sh"` | 4 files |
| .tmp files | `find . -name "*.tmp"` | 3 files |
| .txt files | `find . -name "*.txt"` | 2 files |
| Files starting with "a" | `find . -name "a*"` | auth.log, app.log, access.log |
| Files starting with "s" | `find . -name "s*"` | system.log (pre-rename), server.conf, ssh.conf, scripts/ |
| 3-letter extension files | (not completed this attempt — see Open Item below) | — |
| Files named "backup" | `find . -name "backup"` | ./backup (the folder itself) |
| Verify full structure | `ls -R` | confirmed all 6 folders and their contents |


## Rename Challenge (Phase 4)

• mv logs/system.log logs/kernel.log —
done correctly

• mv reports/weekly.txt reports
weekly_report.txt — done correctly
after an initial typo (reports/weekly
without the .txt, which
doesn't exist — self-corrected)
Verified with ls -R after both renames


## Copy Challenge (Phase 5) — Open Item

Used cp -r configs backup, which copies
the entire configs folder
as a nested subdirectory (backup/configs/
*.conf). This works, but
doesn't demonstrate the wildcard skill the
mission was actually testing.


## The intended wildcard approach:

Code

```
cp configs/*.conf backup/
```
This copies just the three .conf files directly
into backup/
(flat — no extra configs/ subfolder in
between), using the wildcard
to select all matching files at once without
naming each one.


## Final Boss - Reasoning (No Terminal)

Q1: * aur ? me kya difference hai?

* matches zero or more of any character —
length is flexible.
? matches exactly one character — no more,
no less.
*.log matches any-length name before .log;
 file?.txt only matches
a single extra character (file1.txt, not
file10.txt).

Q2: .log aur a.log me kya difference hai?

*.log — any file at all, as long as it ends
in .log.
a*.log — must ALSO start with "a" — a
stricter, combined condition.

Q3: 10,000 files hon to wildcard ka
advantage?

Instead of typing or knowing 10,000
individual filenames, one pattern
(*.log, a*, etc.) selects every matching file in
a single command —
the advantage scales with how many files
there are, not despite it.

Q4: mv aur cp me sabse bada practical
difference?

mv moves/renames — the file no longer exists
at its old location
afterward. cp duplicates — both the original
and the copy exist,
useful specifically for backups.

Q5: Sirf .conf files backup karni ho — 
wildcard kaise help karega?

cp configs/*.conf backup/ — one
command copies every .conf file
without listing each name individually. This is
the real value of a
wildcard: acting on a whole category of files at
once instead of one
at a time.
(Corrected during review — original answer
focused on renaming a
single specific file, not the bulk-selection
benefit that's the actual
point of a wildcard.)

## Status

Mission #9 (Wildcards) — Mostly completed;
2 open items below
