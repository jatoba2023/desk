# desk

**desk** is a personal productivity suite for the command line.  
It keeps your birthdays, tasks, calendar, professional experience, and projects in one place — fast, offline, and distraction-free.

| Tool | Purpose | Data file |
|------|---------|-----------|
| `bday` | Birthday manager | `~/.bday.json` |
| `tsk` | Task manager | `~/.tsk.json` |
| `cld` | Calendar | `~/.cld.json` |
| `xp` | Experience tracker | `~/.xp.json` |
| `pln` | Project planner | `~/.pln.json` |

All tools share the same visual identity: a consistent ANSI color palette, prompt style, and footer convention.

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
sudo install -m 755 pln  /usr/local/bin/pln
```

> **No sudo access?** Install to your user directory instead:
> ```bash
> mkdir -p ~/.local/bin
> cp bday tsk cld xp pln ~/.local/bin/
> chmod +x ~/.local/bin/bday ~/.local/bin/tsk ~/.local/bin/cld ~/.local/bin/xp ~/.local/bin/pln
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
C:\desk\pln.py
```

**Step 3 — Create a launcher `.bat` file for each tool**

`bday.bat`:
```bat
@echo off
python "%~dp0bday.py" %*
```
Repeat for `tsk.bat`, `cld.bat`, `xp.bat`, and `pln.bat`.

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
pln
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
mkdir -p ~/pCloudDrive/Apps/desk

mv ~/.bday.json ~/pCloudDrive/Apps/desk/
mv ~/.tsk.json  ~/pCloudDrive/Apps/desk/
mv ~/.cld.json  ~/pCloudDrive/Apps/desk/
mv ~/.xp.json   ~/pCloudDrive/Apps/desk/
mv ~/.pln.json  ~/pCloudDrive/Apps/desk/

ln -s ~/pCloudDrive/Apps/desk/.bday.json ~/.bday.json
ln -s ~/pCloudDrive/Apps/desk/.tsk.json  ~/.tsk.json
ln -s ~/pCloudDrive/Apps/desk/.cld.json  ~/.cld.json
ln -s ~/pCloudDrive/Apps/desk/.xp.json   ~/.xp.json
ln -s ~/pCloudDrive/Apps/desk/.pln.json  ~/.pln.json
```

**Windows (run PowerShell as Administrator):**
```powershell
$desk = "$env:USERPROFILE\pCloudDrive\Apps\desk"
New-Item -ItemType Directory -Force -Path $desk

Move-Item "$env:USERPROFILE\.bday.json" "$desk\.bday.json"
Move-Item "$env:USERPROFILE\.tsk.json"  "$desk\.tsk.json"
Move-Item "$env:USERPROFILE\.cld.json"  "$desk\.cld.json"
Move-Item "$env:USERPROFILE\.xp.json"   "$desk\.xp.json"
Move-Item "$env:USERPROFILE\.pln.json"  "$desk\.pln.json"

New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.bday.json" -Target "$desk\.bday.json"
New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.tsk.json"  -Target "$desk\.tsk.json"
New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.cld.json"  -Target "$desk\.cld.json"
New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.xp.json"   -Target "$desk\.xp.json"
New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.pln.json"  -Target "$desk\.pln.json"
```

**On a second machine**, pCloud already has the files — just create the symlinks:
```bash
ln -s ~/pCloudDrive/Apps/desk/.bday.json ~/.bday.json
ln -s ~/pCloudDrive/Apps/desk/.tsk.json  ~/.tsk.json
ln -s ~/pCloudDrive/Apps/desk/.cld.json  ~/.cld.json
ln -s ~/pCloudDrive/Apps/desk/.xp.json   ~/.xp.json
ln -s ~/pCloudDrive/Apps/desk/.pln.json  ~/.pln.json
```

---

## bday — Birthday Manager

```
bday                    Show upcoming birthdays (next 30 days)
bday add                Add a birthday
bday list               List all birthdays sorted by date
bday all                Alias for bday list
bday remove <n>         Remove a birthday (fuzzy name match)
```

Birthdays within 30 days are shown with urgency badges: **TODAY**, **TOMORROW**, **THIS WEEK**.  
Provide a birth year to display the person's upcoming age. An optional notes field is available.

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
tsk <id> edit           Edit a task
tsk <id> delete         Delete a task
```

Tasks are automatically ranked by due date: overdue → today → tomorrow → this week → future. Completed tasks appear with strikethrough at the bottom of `tsk list`.

When adding or editing a task, you can optionally provide a **start time** and **end time** (displayed in magenta).

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

If the weekday you type matches today (e.g. you type `monday` and today is Monday), the app asks whether you mean today or next Monday.

**Data format — `~/.tsk.json`:**
```json
[
  {
    "id": 1,
    "name": "Finish proposal",
    "due": "2026-03-11",
    "note": "",
    "done": false,
    "created_on": "2026-03-01",
    "start_time": "09:00",
    "end_time": "10:30"
  }
]
```
`note`, `start_time`, and `end_time` are optional.

---

## cld — Calendar

```
cld                     Show current month + next 2 months as a list
cld next                Next month
cld prev                Previous month
cld 2026-08             Jump to a specific month
cld agenda              Chronological list for next 60 days
cld agenda 30           Agenda for next N days
cld add                 Add a new event
cld list                List all cld events with IDs and urgency badges
cld <id> edit           Edit an event
cld <id> delete         Delete an event
cld show                Weekly view (current week, Mon–Sun, 08:00–18:00)
cld show next           Weekly view for next week
cld show prev           Weekly view for previous week
```

The calendar pulls data from all three sources automatically:

- **Magenta** `ev ◆` — events from `cld`
- **Cyan** `tk ✦` — tasks with a due date from `tsk`
- **Yellow** `bd 🎂` — birthdays from `bday`

### Recurring events

When adding an event, you can mark it as recurring. Supported frequencies: daily, weekly, monthly, yearly. You will be asked for a stop year.

When editing or deleting a recurring event, the app asks whether you want to change the **next occurrence only** or the **entire series**.

### Weekly view (`cld show`)

Displays a 7-day grid from Monday to Sunday with hourly rows (08:00–18:00). Events with a start time appear as filled blocks in the correct time slot. Navigate with `cld show next` and `cld show prev`.

**Example list output:**
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
  {
    "id": 1,
    "title": "Vacation",
    "date": "2026-03-23",
    "end_date": "2026-03-28",
    "note": "Paris",
    "created_on": "2026-03-01",
    "start_time": "09:00",
    "recurring": "yearly",
    "recur_until": 2030
  }
]
```
`end_date`, `note`, `start_time`, `recurring`, and `recur_until` are optional.

---

## xp — Experience Tracker

Track your professional projects with rich context: client, employer, role, skills, functional areas, industry, and narrative fields.

```
xp                      List all projects (compact view, sorted by date)
xp full                 List all projects with full detail
xp add                  Add a project
xp <id> detail          Full detail of a single project
xp <id> edit            Edit a project
xp <id> delete          Delete a project
xp filter <term>        Search across all fields
xp export               Export all projects to a Markdown file
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
| Industry | The client's industry |
| Skills | Technologies, tools, or soft skills used |
| Business problem | What problem was being solved |
| Solution | What was built or done |
| Results | Measurable outcomes — included in export |
| Lessons learned | Personal takeaways — excluded from export |

### Taxonomy

Functional areas, industries, and skills are reusable lists. Type `N` at any selection screen to add new items on the fly without leaving the flow.

### Filter

`xp filter <term>` searches case-insensitively across every field. Multi-word terms work:

```
xp filter python
xp filter financial services
xp filter data engineering
```

### Export

`xp export` saves `xp-export-YYYY-MM-DD.md` in the current directory. Includes Business Problem, Solution, and Results. Lessons is excluded.

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

---

## pln — Project Planner

`pln` is a full project management tool with ALAP scheduling, dependency tracking, resource allocation, and HTML report generation. The interface is a **hybrid TUI shell**: each command clears and redraws the screen, keeping the Gantt chart and task list always visible.

The interface language is **Portuguese**.

```
pln                     Open the interactive TUI
```

### Project selection screen

On launch, `pln` shows a list of active projects.

| Key | Action |
|-----|--------|
| `1`, `2`, … | Open that project |
| `N` | Create a new project |
| `A` | Archive a project (hides from list, data preserved) |
| `D` | Unarchive a project |
| `S` | Quit |

### Project shell commands

Once a project is open, the screen shows a Gantt chart sorted by earliest start date and a task list below it. All commands are typed at the prompt.

```
task add                Add a new task
task <n> edit           Edit a task (resources pre-filled, adjustable)
task <n> done           Mark done — dependent tasks are recalculated
task <n> delete         Delete a task
recurso add             Add a resource to this project
recurso list            List resources by category
recurso delete <n>      Remove a resource
edit                    Edit project name or target date
report                  Generate an HTML executive report
projetos                Return to project selection
sair                    Quit
```

### Scheduling

The scheduler uses **ALAP (As Late As Possible)** — tasks are pushed as close to the target date as possible. Marking a task done recalculates all dependents using the actual completion date.

**Dependency types:**

| Type | Meaning |
|------|---------|
| `FI` (Fim→Início) | B starts when A finishes |
| `II` (Início→Início) | B starts when A starts |

Both support a **lag** in days (positive = delay, negative = overlap).

### Window constraints

Tasks can have an optional date window (`window_start` and/or `window_end`) to force them into a specific period — for example, purchasing materials during a low-season window. If the ALAP schedule conflicts with the window, the window wins and a warning is shown:

```
⚠ fim forçado para 30 Jun 2026 (janela)
```

In the Gantt: magenta bars have a window, yellow bars have a conflict.

### Resources

Resources belong to a project and have three categories: **Mão de Obra**, **Equipamento**, **Insumos**. Each has a name and a daily cost in R$.

When assigning a resource to a task, you specify a **quantity**. Cost = `custo_dia × qty × duration_days`. Displayed as `Engenheiro (×2)` when qty > 1.

When editing task resources, the current allocation is shown with quantities. Press Enter to keep all (and adjust quantities per item), `0` to remove all, or pick a new selection.

### Gantt chart

| Color | Meaning |
|-------|---------|
| Cyan | Scheduled, on track |
| Magenta | Has a window constraint |
| Yellow | Window conflict with predecessors |
| Green | Completed |
| Red | Projected past target date |

Tasks are sorted by earliest start date, then by ID. Dates and durations are shown in the task list below the chart, not on the bars themselves.

### HTML Report (`report`)

The `report` command generates `~/pln-report-<id>-<YYYYMMDD>.html` and opens it in the browser. The report includes:

- KPI cards: realized cost, planned total cost, progress, target date
- Alerts for tasks with risk
- Executive summary paragraph
- Proportional Gantt chart with today marker
- Previsto × Realizado bar chart and deviation table
- Cost breakdown by resource with percentage share

### Data format — `~/.pln.json`

```json
{
  "projects": [
    {
      "id": 1,
      "name": "Lançamento do Produto",
      "target_date": "2026-06-30",
      "archived": false,
      "recursos": [
        { "id": 1, "name": "João Silva", "categoria": "Mão de Obra", "custo_dia": 400 }
      ],
      "tasks": [
        {
          "id": 1,
          "name": "Pesquisa de mercado",
          "duration_days": 10,
          "predecessors": [{ "tid": 2, "tipo": "FI", "lag": 0 }],
          "recursos": [{ "rid": 1, "qty": 2 }],
          "window_start": "2026-05-01",
          "window_end": "2026-05-31",
          "done": false,
          "started_on": null,
          "completed_on": null
        }
      ]
    }
  ]
}
```

Optional fields: `archived`, `window_start`, `window_end`, `started_on`, `completed_on`. `lag` defaults to `0`, `qty` defaults to `1`.

### Legacy CLI commands

For scripting, the original one-shot commands still work:

```
pln <id>                        Show project detail
pln <id> gantt                  Print Gantt chart
pln <id> edit / delete          Edit or delete project
pln <id> task add               Add task
pln <id> task <tid> edit        Edit task
pln <id> task <tid> done        Mark task done
pln <id> task <tid> delete      Delete task
pln <id> recurso add            Add resource
pln <id> recurso list           List resources
pln <id> recurso <rid> delete   Delete resource
```
