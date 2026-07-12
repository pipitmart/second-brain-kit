# PROJECTS.md — Project Index
*Part of: ROOT / profile*
*Created by `/genesis` — [DD Mon YYYY]. (Versionless by design: the system's version lives in `CHANGELOG.md` only, never in folder names or file headers — kernel convention since v0.4.)*
*Scope: Profile. The registry of every project. `/orient` reads it, `/new-project` registers into it, `/offload` updates it.*

> One row per project — the **map**, not the truth. Project content lives in each project's Decisions Log.

## Active

| Project | Drive folder | Folder ID *(convenience)* | Noticeboard tag | SSOT | Status | Last session | Sessions | Hours | Notes |
|---------|--------------|---------------------------|-----------------|------|--------|--------------|----------|-------|-------|
| [first project — added by /new-project] | | | | | Active | | 0 | 0 | |

## Paused / Closed

*(rows move here, never get deleted)*

## Rules
- **Machine-agnostic:** every machine sees the same full list; what differs is which folders are mounted. Never record machine state here.
- **Name is canonical; the ID is convenience.** If they disagree, trust name-search and fix the row.
- `/new-project` adds rows · `/offload` updates Status + Last session · `/orient` flags ghost rows and unregistered projects.
- **`Noticeboard tag` is stored, never derived** — one unique kebab-case tag per project (e.g. `#my-project`), assigned at `/new-project`, used by `profile/Noticeboard.md` routing (`/orient` Step 2.5 drain · `/offload` pin). Stored explicitly so a rename never breaks a pinned note.
- **`Sessions` + `Hours` = the metric's source of truth.** Cumulative per-project counters, written at **every `/offload`** by the session that just ran — the only session that can read that project's log. `Sessions` = its latest session number. `Hours` = the running sum of its **recorded `~Hrs` stamps only** (never elapsed time, never an estimate for an untimed session — kernel §11); if some sessions were untimed, the cell carries `≥` and a ⚠️ — a floor, not a total. `profile/Scoreboard.md` **derives** its totals from this table and holds no independent count, so the two can't drift.
- Status vocabulary: Active · Paused · Closed (with close date).
