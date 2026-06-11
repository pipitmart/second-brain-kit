# PROJECTS.md — Project Index
*Part of: ROOT / profile*
*Version: 1.0 | [DD Mon YYYY] — created by /genesis*
*Scope: Profile. The registry of every project. `/orient` reads it, `/new-project` registers into it, `/offload` updates it.*

> One row per project — the **map**, not the truth. Project content lives in each project's Decisions Log.

## Active

| Project | Drive folder | Folder ID *(convenience)* | SSOT | Status | Last session | Notes |
|---------|--------------|---------------------------|------|--------|--------------|-------|
| [first project — added by /new-project] | | | | Active | | |

## Paused / Closed

*(rows move here, never get deleted)*

## Rules
- **Machine-agnostic:** every machine sees the same full list; what differs is which folders are mounted. Never record machine state here.
- **Name is canonical; the ID is convenience.** If they disagree, trust name-search and fix the row.
- `/new-project` adds rows · `/offload` updates Status + Last session · `/orient` flags ghost rows and unregistered projects.
- Status vocabulary: Active · Paused · Closed (with close date).
