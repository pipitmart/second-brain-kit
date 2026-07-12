# NEW-PROJECT.md — New Project Flow
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic. From a rough objective to a verified, ready-to-work project.*
*Triggered by: `/new-project [objective]`*

> One flow, with a branch for "has Drive (Cowork)" vs "no Drive (Chat)".

---

## Hard rules

1. **Claude builds the project files in place** (folder, README, Decisions Log) once the objective is agreed — the user approves each step. *(In-place Edit makes this safe; direct `create_file` used to spawn duplicates.)*
2. **No file creation without write access.** If the target folder isn't mounted + Available offline, stop and have the user mount it — never fall back to `create_file` as a workaround. *(Creating a brand-new file that doesn't exist yet is fine; but the folder must be locally editable for everything after.)*
3. **The project folder is a sibling of ROOT — created alongside it in Drive, never inside ROOT.** ROOT holds the engine and the private profile; project data stays separate. This keeps ROOT clean (it's only edited when the system changes) and preserves the privacy wall when the kit is shared — project content can never ride along with the kernel.
4. **Don't start the actual work** (research, analysis, writing) until `/orient` passes green.
5. **Don't skip phases.**

---

## Definition of Done

- Objective is concrete and decision-oriented — not "explore X".
- Folder + `README-[PROJECT].md` + `Decisions Log - [PROJECT].md` exist — correctly named, **exactly one each**.
- README carries: objective, folder location, key people, constraints, priorities, conventions, **and the project's red line** — and **no live action list**. Open actions, decisions, and status live only in the Decisions Log; the README is the charter and changes only when the charter does.
- Decisions Log seeded with the session's **small, concrete next actions** in Open Actions.
- Project registered in `profile/PROJECTS.md` — including a unique `Noticeboard tag` (without one, cross-project notes can never route to this project).
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
- ROOT must be mounted and writable — the build registers the project in `profile/PROJECTS.md`, a ROOT write.
- **Project folder — two cases (decide before building):**
  - **Folder already exists in Drive:** mount it alongside ROOT, confirm write access, proceed.
  - **Folder doesn't exist yet:** determine where it will live.
    - **Sibling of ROOT** (standard case — a new top-level project under `Brain/`): Claude cannot create this folder. **Stop here and prompt the user:** *"I can't create a Drive folder that's a sibling of ROOT — the parent (`Brain/`) isn't mounted. Please create `Brain/[Project Name]/` manually in Drive now, then add it to this Cowork project's folder connections alongside ROOT. Tell me when it's done."* Wait for confirmation, then verify the mount is live before proceeding.
    - **Child of an already-mounted folder** (e.g. a sub-project inside a mounted container like `SCX Digital/`): Claude can create it via bash (`mkdir` on the mounted path). Proceed.
- Never start building files until the folder exists in Drive and is mounted with write access.

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
- By this point the project folder already exists in Drive and is mounted (Step 0 guaranteed this). **Do not attempt to create it here.** If somehow it's still missing, stop and return to Step 0.
- Create `README-[PROJECT].md` — instructions, folder location, key people, constraints.
- Create `Decisions Log - [PROJECT].md` from `kernel/SSOT-TEMPLATE.md`.
- Seed Open Actions with the session's small, concrete next steps.
- **Register the project in `profile/PROJECTS.md`** — add its row (folder, SSOT, status Active, **and a `Noticeboard tag`**: unique, kebab-case, derived once from the project name at creation (e.g. `#my-project`) then **stored** — routing never re-derives it, so a later rename can't break a pinned note. Check it collides with no existing row's tag). **Write the two counter cells too: `Sessions` = `0`, `Hours` = `0`** — a brand-new project has genuinely run zero sessions, so `0` is an observed value, not a guess (`*pending*` means "has counters but hasn't reported since they existed," which is a different state and belongs only to projects that pre-date them). They fill from the project's first `/offload` onward. *(Added 12 Jul 2026, Sess.80, from Fable's pre-push `/review`: Step 4 named every column except the two the metric added, so a literal execution registered a row with blank counters.)*
- **Optionally create `Skunkworks - [PROJECT].md`** from `kernel/SKUNKWORKS-TEMPLATE.md` — only when the project is likely to generate more ideas than its roadmap/backlog should hold. Skip for narrow, well-scoped projects.

### 5 — Wire Cowork *(Drive branch only)*
- Create the Cowork project; paste the README instructions into project settings.
- **Connect TWO folders to the project: the project folder AND ROOT.** Cowork scopes write access to connected folders, and a normal session writes to *both* — the project's Decisions Log **and** ROOT (`profile/PROJECTS.md` every `/offload`, the mirror in `ABOUT-ME.md` occasionally). Connect only the project folder and `/offload` will fail to update the index and mirror. This is the single most common setup miss — make it explicit, every project, every machine. *(`/orient` Step 0 detects a missing ROOT mount and prompts to fix it, but don't rely on the self-heal — connect both up front.)*
- *(These mounts are the only per-machine step — the Drive files themselves sync on their own.)*

> **One Cowork project = one SB project + ROOT.** Mount exactly those two folders — not a whole container of projects. A container folder holding ROOT + your projects is recommended for *finding* things on a busy Drive (see `profile/STACK.md`), but it is **organization, not a mount target**: mounting the whole container into one Cowork project makes a session see every project at once, which breaks per-project orientation (`/orient` + `/offload` assume one active project) and drops the privacy wall. **Organize with a container; mount per project.** Why this, and not the alternatives: `kernel/DESIGN-RATIONALE.md`.

### 6 — Verify
- Run `/orient`. All green → project is live. Any red → fix, re-run.

### 7 — Close the first session right *(first-ever project / brand-new user only)*
A routine is only learned once it's *used* — not once it's explained. Before a new user leaves their first session, make the loop happen, don't just describe it:
- Have them type **`/offload`** now, with you watching — so they run it once live and the muscle memory forms. Don't accept "I'll do it later."
- Then state the whole routine in one line: *"Next time you open me, type `/orient` first — that's how I pick up where we left off. Work. Then `/offload` to save. That's the whole thing."*
- Mention `/new-project` exists for when they want to start something new — **one sentence, don't drill it** (they just did one; more would overwhelm).

Skip this step for established users creating their Nth project — they already own the loop.

---

## Chat branch (no Drive)

In plain Chat with no Drive MCP: do steps 1–4 conceptually, output the README + Decisions Log **as text** for the user to save manually, and skip the in-place build. The Drive branch is preferred for anything that needs to persist.
