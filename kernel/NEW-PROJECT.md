# NEW-PROJECT.md — New Project Flow
*Part of: ROOT v2.2 / kernel*
*Version: 1.1 | 11 Jun 2026*
*Scope: Kernel. Person-agnostic. From a rough objective to a verified, ready-to-work project.*
*Triggered by: `/new-project [objective]`*

> Replaces v2.1's NEW-CW-PROJECT + NEW-CHAT-PROJECT — one flow, with a branch for "has Drive (Cowork)" vs "no Drive (Chat)".

---

## Hard rules

1. **Claude builds the project files in place** (folder, README, Decisions Log) once the objective is agreed — the user approves each step. *(This reverses v2.1's manual-checklist rule, which existed only because `create_file` made duplicates. In-place Edit fixed that.)*
2. **No file creation without write access.** If the target folder isn't mounted + Available offline, stop and have the user mount it — never fall back to `create_file` as a workaround. *(Creating a brand-new file that doesn't exist yet is fine; but the folder must be locally editable for everything after.)*
3. **Don't start the actual work** (research, analysis, writing) until `/orient` passes green.
4. **Don't skip phases.**

---

## Definition of Done

- Objective is concrete and decision-oriented — not "explore X".
- Folder + `README-[PROJECT].md` + `Decisions Log - [PROJECT].md` exist — correctly named, **exactly one each**.
- README carries: objective, folder location, key people, constraints, priorities, conventions, **and the project's red line**.
- Decisions Log seeded with the session's **small, concrete next actions** in Open Actions.
- Project registered in `profile/PROJECTS.md`.
- `/orient` runs green — no contradictions, SSOT singular, key people known.

---

## Profile used

- `profile/PROFILE.md` — working style + constraints (timezone, date format, decomposition preference)
- `profile/STACK.md` — which tools/MCPs are available

*(Objective and key people are project input, not profile.)*

---

## The flow

### 0 — Access + instance gate
- Has this machine passed `/stress-test`? If never set up → run it first.
- Is the target folder mounted + Available offline (write access)? If not → have the user mount it. *(This is `/orient`'s Step 0, applied before building.)*

### 1 — Carry prior context
- Name-search for any existing Decisions Log in the likely folder. If found, read it and carry decisions + open actions forward. Flag old naming for rename.

### 2 — Clarify the objective
Probe until it's concrete and actionable:
- **5 Whys** — the real motivation
- **Pre-mortem** — assume it failed; why?
- **Red line** — "What observable event means stop, pause, or ask for help here — a % of capital, N days of silence, a missed date?" Defined in calm, enforced in storm; recorded in the README and SSOT. (See `kernel/HELP-ME.md → Red Lines`.)
- **Jobs-To-Be-Done** — what problem, for whom?
- **Constraint-mapping** — capital, time, people, regulatory
- **Data first** — if the objective implies data the user already holds (finances, sales, inventory, trackers), ask for it and scope from the real data, not the abstract goal — then propose: "want to build this?"

"Explore feasibility" is not enough. "Decide by [date] whether to [specific action], given [constraints]" is enough.

### 3 — Draft + stress-test instructions
- Draft project instructions from kernel + profile + objective.
- Stress-test before showing the user: contradictions with global conventions? missing key people / scope / constraints? correct Decisions Log naming? Fix the gaps first.

### 4 — Build it *(Claude executes, user approves)*
- Create the project folder (if new).
- Create `README-[PROJECT].md` — instructions, folder location, key people, constraints.
- Create `Decisions Log - [PROJECT].md` from `kernel/SSOT-TEMPLATE.md`.
- Seed Open Actions with the session's small, concrete next steps.
- **Register the project in `profile/PROJECTS.md`** — add its row (folder, SSOT, status Active).

### 5 — Wire Cowork *(Drive branch only)*
- Create the Cowork project; paste the README instructions into project settings. *(The only per-machine step — Drive files sync on their own.)*

### 6 — Verify
- Run `/orient`. All green → project is live. Any red → fix, re-run.

---

## Chat branch (no Drive)

In plain Chat with no Drive MCP: do steps 1–4 conceptually, output the README + Decisions Log **as text** for the user to save manually, and skip the in-place build. The Drive branch is preferred for anything that needs to persist.
