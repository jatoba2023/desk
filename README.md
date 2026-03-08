# 🖥️ desk

**desk** is a personal productivity suite for the command line.  
It keeps your birthdays, tasks, calendar, and professional experience in one place — fast, offline, and distraction-free.

It is made of four tools that share the same visual identity and data files:

| Tool | Purpose | Data file |
|------|---------|-----------|
| `bday` | Birthday manager | `~/.bday.json` |
| `tsk` | Task manager | `~/.tsk.json` |
| `cld` | Calendar | `~/.cld.json` |
| `xp` | Experience tracker | `~/.xp.json` |

---

## Requirements

- **Python 3.10+**

Check your version:
```
python3 --version
```

Download at [python.org](https://www.python.org/downloads/) if needed.

---

## Installation

### Linux / macOS

```bash
sudo install -m 755 bday /usr/local/bin/bday
sudo install -m 755 tsk  /usr/local/bin/tsk
sudo install -m 755 cld  /usr/local/bin/cld
sudo install -m 755 xp   /usr/local/bin/xp
```

> **No sudo access?** Install to your user directory instead:
> ```bash
> mkdir -p ~/.local/bin
> cp bday tsk cld xp ~/.local/bin/
> chmod +x ~/.local/bin/bday ~/.local/bin/tsk ~/.local/bin/cld ~/.local/bin/xp
> ```
> Then add this to your `~/.bashrc` or `~/.zshrc`:
> ```bash
> export PATH="$HOME/.local/bin:$PATH"
> ```
> Restart your terminal and you're set.

---

### Windows

Python must be installed first. Download from [python.org](https://www.python.org/downloads/) and during setup check **"Add Python to PATH"**.

**Step 1 — Create an install folder:** `C:\desk\`

**Step 2 — Place the scripts there** and rename each one with a `.py` extension:
```
C:\desk\bday.py
C:\desk\tsk.py
C:\desk\cld.py
C:\desk\xp.py
```

**Step 3 — Create a launcher `.bat` file for each tool**

`bday.bat`:
```bat
@echo off
python "%~dp0bday.py" %*
```
Repeat for `tsk.bat`, `cld.bat`, and `xp.bat`.

**Step 4 — Add the folder to your PATH**

1. Press `Win + S` → search **"Edit the system environment variables"** → open it
2. Click **"Environment Variables…"**
3. Under **User variables**, select **Path** → **Edit**
4. Click **New** and add: `C:\desk`
5. Click **OK** on all dialogs

**Step 5 — Open a new terminal and test:**
```
bday
tsk
cld
xp
```

> **PowerShell note:** If you see a script execution error, run this once:
> ```powershell
> Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
> ```

---

## Cloud sync with pCloud

All tools store data as plain JSON in your home directory. Move the files into your pCloud folder and replace them with symlinks to sync across machines.

**Linux / macOS:**
```bash
mkdir -p ~/pCloudDrive/desk
mv ~/.bday.json ~/pCloudDrive/desk/
mv ~/.tsk.json  ~/pCloudDrive/desk/
mv ~/.cld.json  ~/pCloudDrive/desk/
mv ~/.xp.json   ~/pCloudDrive/desk/

ln -s ~/pCloudDrive/desk/.bday.json ~/.bday.json
ln -s ~/pCloudDrive/desk/.tsk.json  ~/.tsk.json
ln -s ~/pCloudDrive/desk/.cld.json  ~/.cld.json
ln -s ~/pCloudDrive/desk/.xp.json   ~/.xp.json
```

**Windows (run PowerShell as Administrator):**
```powershell
$desk = "$env:USERPROFILE\pCloudDrive\desk"
New-Item -ItemType Directory -Force -Path $desk

Move-Item "$env:USERPROFILE\.bday.json" "$desk\.bday.json"
Move-Item "$env:USERPROFILE\.tsk.json"  "$desk\.tsk.json"
Move-Item "$env:USERPROFILE\.cld.json"  "$desk\.cld.json"
Move-Item "$env:USERPROFILE\.xp.json"   "$desk\.xp.json"

New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.bday.json" -Target "$desk\.bday.json"
New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.tsk.json"  -Target "$desk\.tsk.json"
New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.cld.json"  -Target "$desk\.cld.json"
New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.xp.json"   -Target "$desk\.xp.json"
```

**On a second machine**, pCloud already has the files — just create the symlinks:
```bash
ln -s ~/pCloudDrive/desk/.bday.json ~/.bday.json
ln -s ~/pCloudDrive/desk/.tsk.json  ~/.tsk.json
ln -s ~/pCloudDrive/desk/.cld.json  ~/.cld.json
ln -s ~/pCloudDrive/desk/.xp.json   ~/.xp.json
```

---

## bday — Birthday Manager

```
bday                    Show upcoming birthdays (next 30 days)
bday add                Add a birthday
bday list               List all birthdays sorted by date
bday all                Same as bday list
bday remove <name>      Remove a birthday
```

Birthdays within 30 days are shown with urgency badges: **TODAY**, **TOMORROW**, **THIS WEEK**.  
Provide a birth year to display the person's upcoming age.

**Example:**
```
bday add
  Name       › Alice Johnson
  Month      › March
  Day        › 7
  Birth year › 1990
  Note       › best friend
```

**Data format — `~/.bday.json`:**
```json
[
  { "name": "Alice Johnson", "month": 3, "day": 7, "year": 1990, "observation": "best friend" }
]
```
`year` and `observation` are optional.

---

## tsk — Task Manager

```
tsk                     Show all active tasks ranked by urgency
tsk add                 Add a task
tsk list                Show all tasks including completed ones
tsk <id> done           Mark a task as done
tsk <id> delete         Delete a task
```

Tasks are automatically ranked by due date: overdue → today → tomorrow → this week → future. Completed tasks appear with strikethrough at the bottom of `tsk list`.

**Accepted due date formats:**

| Input | Meaning |
|-------|---------|
| `today` | Today |
| `tomorrow` | Tomorrow |
| `monday` | Next upcoming Monday |
| `next friday` | The Friday of next week |
| `next week` | 7 days from today |
| `next month` | Same day next month |
| `end of month` / `eom` | Last day of this month |
| `+7d` / `+2w` | Relative days or weeks |
| `2026-12-25` | Specific date (YYYY-MM-DD) |
| `Dec 25` / `25 Dec 2026` | Natural date |

If the day you type matches today (e.g. you type `monday` and today is Monday), the app asks whether you mean today or next Monday.

**Data format — `~/.tsk.json`:**
```json
[
  { "id": 1, "name": "Finish proposal", "due": "2026-03-11", "note": "", "done": false, "created_on": "2026-03-01" }
]
```

---

## cld — Calendar

```
cld                     Show current month + next 2 months
cld next                Next month
cld prev                Previous month
cld 2026-08             Jump to a specific month
cld agenda              Chronological list for next 60 days
cld agenda 30           Agenda for next N days
cld add                 Add a new event
cld list                List all cld events with urgency badges
cld <id> delete         Delete an event
```

The calendar pulls data from all three sources automatically:

- **Magenta** `ev ◆` — events from `cld`
- **Cyan** `tk ✦` — tasks with a due date from `tsk`
- **Yellow** `bd 🎂` — birthdays from `bday`

Multi-day events are supported — set a start and end date when adding an event.

**Example output:**
```
  MARCH  2026
  ──────────────────────────────────────────────
  10 Tue   ev  ◆  Team meeting
  14 Sat   ev  ◆  Conference
            tk  ✦  Buy birthday gift
            bd  🎂 Bob Smith's birthday
  23 Mon   ev  ◆  Vacation
  24 Tue   ev  ◆  Vacation (cont.)
```

**Data format — `~/.cld.json`:**
```json
[
  { "id": 1, "title": "Vacation", "date": "2026-03-23", "end_date": "2026-03-28", "note": "Paris", "created_on": "2026-03-01" }
]
```
`end_date` and `note` are optional.

---

## xp — Experience Tracker

Track your professional projects with rich context: client, employer, role, skills used, functional areas, industry, and narrative fields.

```
xp                      List all projects (compact view)
xp full                 List all projects with full detail
xp add                  Add a project
xp <id> detail          Full detail of a single project
xp <id> edit            Edit a project
xp <id> delete          Delete a project
xp filter <term>        Search across all fields
xp export               Export all projects to a .md file
xp summary              Stats overview with bar charts
xp functional add       Add functional areas
xp functional list      List functional areas
```

### Project fields

| Field | Description |
|-------|-------------|
| Project name | Name of the project |
| Client | The client or organization |
| Employer | Your employer at the time |
| Role | Your specific role on the project |
| Month / Year | When the project took place |
| Functional areas | One or more areas (e.g. Data Engineering, Product Management) |
| Industry | The client's industry (one) |
| Skills | Technologies, tools, or soft skills used (one or more) |
| Business problem | What problem was being solved |
| Solution | What was built or done |
| Results | Measurable outcomes (included in export) |
| Lessons learned | Personal takeaways (not exported) |

### Taxonomy

Functional areas, industries, and skills are reusable lists. When adding or editing a project, type `N` at any selection screen to add new items on the fly without leaving the flow.

Functional areas can also be managed directly:
```
xp functional add       Add one or more (comma-separated)
xp functional list      List all defined functional areas
```

Industries and skills are added exclusively on the fly during `xp add` or `xp <id> edit`.

### Filter

`xp filter <term>` searches case-insensitively across every field: project name, client, employer, role, industry, functional areas, skills, business problem, solution, results, and lessons. Multi-word terms work too:

```
xp filter python
xp filter financial services
xp filter data engineering
```

### Export

`xp export` saves a Markdown file named `xp-export-YYYY-MM-DD.md` in your current directory. It includes Business Problem, Solution, and Results for each project. The Lessons field is intentionally excluded from exports.

### Data format — `~/.xp.json`

```json
{
  "skills": [{ "id": 1, "name": "Python" }],
  "industries": [{ "id": 1, "name": "Financial Services" }],
  "functionals": [{ "id": 1, "name": "Data Engineering" }],
  "projects": [
    {
      "id": 1,
      "name": "Data Platform Migration",
      "client": "Acme Bank",
      "employer": "TechCorp",
      "role": "Lead Data Engineer",
      "month": 3,
      "year": 2024,
      "functionals": ["Data Engineering"],
      "industry": "Financial Services",
      "skills": ["Python", "AWS"],
      "problem": "Legacy warehouse was slow and expensive to scale.",
      "solution": "Migrated to cloud-native lakehouse on AWS.",
      "results": "10x query performance improvement.",
      "lessons": "Start infrastructure planning earlier.",
      "created_on": "2024-03-01"
    }
  ]
}
```
