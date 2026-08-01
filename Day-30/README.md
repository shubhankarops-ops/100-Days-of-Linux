# Day 30 - Redirection Lab

## Role

Log Analysis Engineer

## Theme

Redirection (>, >>, 2>) and Pipes (|) — saving
command output to
files instead of only viewing it on screen.

## Server Structure

Code

``` 
Log-Lab/
├── logs/      app.log, auth.log, server.log
├── reports/   report.txt
├── scripts/   monitor.sh, backup.sh
├── backup/    scripts/  (recovered copy — see Cleanup Note)
└── archive/
```

## Redirection & Pipe Reference (today's core lesson)

| Operator | Meaning | Example |
| :--- | :--- | :--- |
| `>` | overwrite file with output | `echo "hi" > file.txt` |
| `>>` | append output, keep existing content | `echo "hi" >> file.txt` |
| `2>` | send only ERROR output to a file (stdout stays on screen) | `command 2> error.log` |
| `\|` | send output to another command (never directly to a file) | `find . -name "*.log" \| wc -l` |
| `tee -a` | pipe target that both prints to screen AND appends to a file | `command \| tee -a file.txt` |


Key distinction: | only works into a command.
find . -name "*.log" | reports/
report.txt fails because the shell tries to run
"reports/report.txt" as a program. tee works
after a pipe because tee
itself is a command that accepts piped input
and writes it to a file.


## Investigation Results

| Task | Command | Result |
| :--- | :--- | :--- |
| Show + save current directory | `pwd \| tee -a reports/report.txt` | path saved and displayed |
| List logs, append to report | `find . -name "*.log" \| tee -a reports/report.txt` | 3 files listed and appended |
| ERROR count | `grep -o "ERROR" logs/*.log \| wc -l` | 19 (exact count — habit finally locked in) |
| Linux count | `grep -o "Linux" logs/*.log \| wc -l` | 6 (exact count) |
| Unique words | `sort logs/*.log \| uniq` | includes "LinuxERROR" — a data typo, see Data Note below |
| .log filenames without extension | *(attempted with cut -d. -f1 logs, failed — see Open Item)* | — |
| monitor.sh permission | `chmod +x` &rarr; `ls -l` &rarr; `chmod -x` &rarr; `ls -l` | clean, no typos |
| Backup | `cp -r scripts backup` *(after a reversed-direction detour)* | backup/scripts/ correctly populated |
| History review | `history 15, then !579` | 1 past command re-run, as required |


## Data Note

app.log contains a line reading LinuxERROR
(two words merged with no
space). Since grep -o matches substrings,
this single line counts
toward BOTH the "Linux" total and the
"ERROR" total, and shows up as its
own separate entry in the unique-words list.
Same category of issue as
the "INFOO" typo from Day 26 — always
worth a quick visual check of raw
data before trusting a count.

## Open Item — filenames without extension

cut -d. -f1 logs failed because cut
operates on lines of text, not
directly on a folder. The working version:

Code

```
ls logs | cut -d. -f1
```

ls logs first turns the folder's contents into a
list of text lines;
cut can then split each line on the . and take
the first field.

## Cleanup Note (from the backup detour)

An earlier mv backup ../Log-Lab command
didn't move the folder up a
level as intended — ../Log-Lab was a new
name at that location
(no folder called Log-Lab existed there yet), so
mv renamed "backup"
to "Log-Lab" instead of moving it. This left an
empty, oddly-nested
Log-Lab/Log-Lab folder behind. Cleanup for
next session:

Code

```
rm -r Log-Lab
```
Lesson: mv source destination — if
destination doesn't already exist
as a folder, the source gets renamed to that
destination name,
not moved into it. To simply delete an
accidental copy, rm -r foldername is the
more direct and predictable fix.

## Admin Decision - Reasoning (No Terminal)

Q1: > aur >> me practical difference?

> overwrites the file completely — anything
already there is gone.
>> appends — new output is added after
whatever's already in the file.

Q2: Error ko alag file me save karna ho, kaise
sochoge?

Use 2>, the stderr-specific redirect: command
2> error.log sends
only the error messages to that file, while
normal output still shows
on screen. For both at once: command >
output.log 2> error.log.
(Corrected during review — original answer
described the tee-append
workflow used for normal output, not the
dedicated error stream.)

Q3: Pipe (|) ka real-life use?

Chaining commands so each one's output
feeds directly into the next
without saving anything in between — e.g.
grep "Linux" file.log | sort | uniq -c
| sort -nr finds matches,
sorts them, counts duplicates, then ranks by
frequency, all in one line.

Q4: Bada output — screen pe dekhna better
ya file me save karna? Kyun?

File is better for large output: terminal
scrollback is limited and
messy to search through, while a saved file
can be grepped, sorted, or
reused later without re-running the original
command — important for
incident reports that need to be reviewed or
shared afterward.
(Expanded during review — original answer
focused only on nano being
nicer to read than a scrolling terminal.)

Q5: Har command ka result add karna ho,
overwrite nahi — approach?

tee -a (or plain >> if the output doesn't need
to show on screen
too) — both append instead of replacing
existing content.


## Status
Mission #10 (Redirection Lab) — Completed;
2 open items (extension-strip
command, Log-Lab cleanup) carried to Day 31
