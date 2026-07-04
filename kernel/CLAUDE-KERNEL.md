# CLAUDE-KERNEL.md — System Engine
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic — this file ships unchanged to any user. Nothing here names a specific person. Who the user is lives in /profile.*

> **Read this first.** It is the entry point. It explains how the system is laid out, how to find files, how to write without breaking things, and where to load the person-specific context (the Profile). If a term is unfamiliar, see `kernel/GLOSSARY.md`.

---

## 0. What this system is

A second brain that gives Claude persistent memory of **(a)** who the user is and how they work (the **Profile**) and **(b)** what they're working on (the **Projects**) — so the user stops re-briefing Claude every session, and decisions live in one trusted place.

The machinery that runs this — boot, orientation, file rules, project setup — is the **Kernel**. The Kernel is identical for every user. Only the Profile changes from person to person.

---

## 1. Architecture

```
ROOT  (ONE universal folder, sibling to the project folders — never inside a project; may sit at the Drive top level OR inside a shared container that also holds the projects. Found at boot by its fingerprint: this file)
├── START-HERE.md       → the only file a new human reads; contains the bootstrap prompt
├── kernel/             → the engine (this folder; person-agnostic, ships to anyone)
├── profile/            → who the user is, how they work, their tools (regenerated per person; NEVER ships)
├── profile-templates/  → blank skeletons /genesis copies into profile/ and fills in
└── skills/             → thin pointer skills (source + installable .skill files); each reads its kernel .md

Project folders  (elsewhere in the user's Drive — siblings of ROOT, never inside it)
├── README-[PROJECT].md         → project context + instructions
└── Decisions Log - [PROJECT].md → SSOT for that project's decisions and open actions
```

---

## 2. Nomenclature

| Term | Meaning |
|------|---------|
| Instance | A machine or Claude install |
| Session | A single chat within a project |
| Project | A defined scope of work |
| Global / Kernel | Applies to every user, instance, session, project |
| Profile | The person-specific layer — swapped per user |
| Fingerprint | How the live ROOT is found: the presence of `kernel/CLAUDE-KERNEL.md` — no hardcoded ID |
| SSOT | Single source of truth — the project's Decisions Log |

Full definitions: `kernel/GLOSSARY.md`.

---

## 3. File discovery

- **ROOT is located by fingerprint, not by ID.** The live ROOT is the folder containing `kernel/CLAUDE-KERNEL.md` that sits **beside** the project folders (a sibling of them), **never inside a project**. It may live at the Drive top level or inside a shared container folder that also holds the projects — the fingerprint finds it at any depth within the mounted scope. **Exactly one ROOT is live.** Any copy sitting inside a project folder, or whose header marks it archived/versioned, is a snapshot — never load or edit it as live.
- **No file or folder IDs are hardcoded anywhere.** `profile/PROFILE.md` may record ROOT's folder ID as a *convenience pointer only*; if pointer and fingerprint ever disagree, trust the fingerprint and fix the pointer.
- Everything else is found by **name search** within ROOT or the relevant project folder.
- Always **search before referencing** a file. If Drive is unavailable, say so explicitly — never guess a file ID or silently fail.
- **A just-mounted folder can list as empty.** Right after a folder is connected, name-search/enumeration may return nothing while a direct Read by exact path works (observed 12 Jun 2026 on a streamed Drive folder). Never conclude a mounted folder is empty from one empty listing — verify with a direct Read of a known file, or retry the listing before declaring an access problem.

---

## 4. Write path *(verified cross-machine, 10 Jun 2026: Windows → Mac)*

- **Edit ROOT and project docs IN PLACE** via Cowork local Edit (the native Read/Write/Edit tools) on the Drive-synced folder. Drive desktop syncs the change to every machine.
- **A connected folder can attach read-only — re-grant it, don't re-mount.** On Cowork a Drive folder may mount with native **Read** working but native **Write/Edit** failing (*"path outside the connected folder"*) even though the folder is genuinely writable (the bash sandbox can create files in it). This is a read-only *connection grant*, not a permission, ACL, or sync problem. **Fix: call `request_cowork_directory` on that folder in-session to re-grant read-write — Write/Edit then work immediately.** A UI / project folder-picker re-mount does **not** reliably fix it (it can re-attach read-only, which is what makes re-mounting feel circular). *(Root-caused 27 Jun 2026; falsified along the way: folder lineage, new-folder migration, mount-registration freshness, spaces-in-name, sandbox-mount mode.)* **The re-grant is NOT durable** — observed lapsing **mid-session** (a folder writable earlier in the same session blocked again later; re-granting restored it). So **re-grant whenever a write blocks, not once**, and treat **ROOT — the always-writable anchor — as the only reliable write target for anything critical** (this is *why* kernel §7a mounts ROOT first). Cross-session persistence also unconfirmed.
- **Use the native file tools (Read/Write/Edit), never the bash shell, to touch Drive files.** Native file access materialises a streamed file the moment it's opened; the bash sandbox reaches Drive through a mount that *can't* trigger that, so streamed/online-only files show up there as an **empty folder**. If a folder reads empty in bash but the files plainly exist in Drive, this is the cause — not a missing mount.
- **Never use the Drive MCP `create_file` on a doc that already exists** — it spawns a duplicate and breaks the SSOT. The Drive MCP is for **read/search only**.
- **Streaming vs Mirror — what actually works (tested 11 Jun).** The native file tools reach Drive through the Windows API, so they work in **either** mode — the entire SB machinery (orient, offload, genesis, new-project, every markdown edit) runs fine on plain **Streaming** with nothing downloaded. **Available offline changes nothing for the sandbox** — with the whole Drive pinned offline, bash still saw empty folders. Reason: the streaming drive (`G:`) is a *virtual/projected* filesystem; the bash sandbox can't enumerate or read it, and its writes land in a throwaway overlay that never reaches Drive. **Code/data skills (pdf, xlsx, docx, python) run through bash**, so they cannot operate directly on a Streaming Drive folder. Two ways to run them against Drive content: **(a) Mirror mode** — My Drive becomes a real `C:\Users\<user>\My Drive` folder the sandbox reads normally (costs whole-Drive disk); or **(b) stay on Streaming and route I/O through the sandbox** — read inputs with native Read, work in the sandbox scratch dir, deliver the finished file with `present_files` (or native Write back into the Drive folder). Never assume bash can see a Streaming Drive path. *(Also on Streaming: Claude can create a Drive file via native Write but cannot delete it — no native delete, and bash can't see it to remove it.)*
- **Discipline:** let Drive finish syncing (no spinner) before switching machines; never edit the same file on two machines at once, or Drive makes a conflicted copy.

*This rule is model-agnostic — it binds whether Claude runs as Opus, Sonnet, or any other model.*

---

## 5. Decisions Log (SSOT)

- Lives **only at the project layer** — never in ROOT.
- Naming: `Decisions Log - [PROJECT].md`. **Exactly one per project.**
- It is the **single source of truth** for that project's decisions, open actions, and pending items.
- On orientation, verify there is exactly one, that it is current, and **flag any duplicate or conflicted copy.**
- **Before writing to it, confirm edit access** (the folder is mounted locally for in-place Edit). If access is read-only, do **not** fall back to `create_file` — prompt the user to mount the folder first.

---

## 6. Commands

| Command | What it does |
|---------|-------------|
| `/stress-test` | Instance setup check — run once on a new machine |
| `/orient` | Session-start orientation — load ROOT + project, surface open actions |
| `/offload` | Session-close + autosave — write the session's decisions and open actions to the SSOT; pairs with `/orient` |
| `/new-project` | Set up a new project from a rough objective |
| `/genesis` | Build a new user's profile — once per user; auto-runs when stress-test finds an empty profile |
| `/help-me` | Support channel, two branches: troubleshoot a stuck system/task (snapshot → fix → restore), or walk the red-alert flow when the *person* is in a loop |
| `/feedback` | Send a feature request or problem about SB back to the maker — Claude articulates it and hands the user one pre-filled link to submit |
| `"update SB"` *(phrase, not a slash-command)* | Check the kit repo for a newer version and apply it — migrations included, content untouched — via `kernel/UPDATE.md`. Automatic doors (orient/stress-test/help-me) do the same on a 7-day cadence. |
| `/review` | Independent review gate — red-team an artifact against a fixed bar (accuracy · staleness · consistency · dangling refs · clarity), grounded against real state; flags, never auto-fixes. On-demand + pre-ship release gate |
| `/grill-me` | Interview the user branch-by-branch down a design tree before building — one question at a time, each with a recommended answer, dependencies resolved in order. Runs *before* a build (vs `/review`, which checks one already made) |
| `/eli5` `/eli14` `/eli1` | Explain at a chosen level of simplicity |
| `/goal` | Restate the session goal + show the learning checklist |

The Profile may define personal aliases for these. Full definitions: `kernel/GLOSSARY.md`.

---

## 7. Boot sequence

- **New instance** (once per machine): `/stress-test` → install skills → mount ROOT + project folder into Cowork (see §7a) → set Available offline → environment check.
- **New session** (every time): `/orient` — **starts with an access check** (can I read ROOT? is the folder mounted for in-place writes?), then loads the active project and surfaces open actions.
- **New project:** `/new-project`.

### 7a. Cowork folder mount SOP *(established 15 Jun 2026)*

**ROOT is the permanent write anchor.** Always mount ROOT first in every Cowork project. ROOT is never empty, so Cowork always has write access. Project folders can be added, removed, or swapped freely alongside it — no archiving required.

- Mount **ROOT first** (permanent — never remove it from any project).
- Mount **project folder/s second** (interchangeable — add or swap as the work changes).

**Why:** Cowork grants write access based on mounted folders. If the only mounted folder is empty or missing, Claude gets "Write access is off." ROOT being non-empty and permanent guarantees write access is always live, regardless of which project folders are present.

---

## 8. Profile linkage — the separation rule

**The Kernel never names a person.** At orientation, load the Profile for everything person-specific:

- `profile/PROFILE.md` — who the user is, working style, guardrails
- `profile/PROJECTS.md` — the project index: every project, its folder, SSOT, and status
- `profile/ABOUT-ME.md` — deep profile + the living mirror (load on stress-test, not daily; `/offload` appends observed patterns)
- `profile/LEARNING-ME.md` — the user's teaching standard and learning patterns
- `profile/STACK.md` — the user's tools and machines
- `profile/Noticeboard.md` — the cross-project channel: project-tagged notes pinned at `/offload`, drained into the target project's SSOT at `/orient` Step 2.5 (ROOT is mounted in every session, so this file crosses the mount/privacy wall)
- `profile/LAB.md` — cross-project system/infra investigations log (loaded on demand, not daily; STACK carries the settled summary + a pointer). This is where infra problems that span projects — Drive sync, mounts, the write path, connectors — get worked, since ROOT is the only folder mounted in every project.

**To set the system up for a different person, swap the `profile/` folder. The Kernel stays untouched.** This is the whole point of the kernel/profile split.

---

## 9. Model use

Protocols are written to be executed by **any** model. Use a lighter model (e.g. Sonnet) for execution — applying conventions, logging, edits — and a stronger model (e.g. Opus) for design and stress-testing. **Authorisation, not model strength, gates changes.**

---

## 10. Preservation — protect the working system

**Preservation is a first-class duty, equal to progress.** A working system is an asset to defend, not just a draft to overwrite. Before anything experimental changes the live system, the known-good state must be recoverable, and the experiment must earn its way in against a stated bar. Experiments run on copies; live changes only on passing the bar. Continuity is the system's job to protect — not the user's to remember.

- **ROOT is shared infrastructure.** One live ROOT serves *every* project, so a change to ROOT touches all of them. Editing ROOT is therefore a deliberate, system-maintenance act — never a side effect of ordinary project work. The default working surface for any Claude instance is the **project layer** (README + Decisions Log), not ROOT. Most users rarely need to touch ROOT at all.
- **Restore point before merge.** No experimental change reaches live (kernel, profile, or the shipped kit) until the current known-good state is captured — a version-controlled commit and/or a dated snapshot. Every release is committed to version control; that commit is the system's final "oh-shit" restore point.
- **Experiments are parked, not loose.** New ideas and experiments land in a project's idea-inbox (Skunkworks) and are built/tested on copies or in a sandbox. They merge into live only on passing a stated bar — the same gate discipline as a version bump.
- **The COS check.** At orientation and offload, surface whether the current work is serving the live goal or displacing it. Drift gets named, not silently followed.

---

## 11. Data integrity — the SSOT records only what's known

Wrong information is more corrosive than missing information: it propagates silently and erodes trust in the whole system. Two hard rules, binding on every model:

- **Never write an assumed, guessed, or default-filled value into the SSOT or any system file** — dates, times, timezones, IDs, names, numbers. A value earns its place in a file only if it was **(a)** loaded from the profile/project data, **(b)** observed in this session, or **(c)** confirmed by the user. If none hold, **ask — don't fill.** A plausible guess written as fact is exactly the failure mode this rule exists to kill.
- **Load before you stamp.** Person-specific values — timezone, date format, names, key people, guardrails — live in the Profile and must be loaded before any of them is used. This is *why* `/orient`'s read-only load is non-skippable (see `ORIENT.md`): skipping the load to save time and then back-filling a value from assumption is precisely how false data enters the record.

---

## 12. Action integrity — verify before surfacing

A user-owned open action logged in an SSOT may already be done outside Claude's visibility. **Never surface a user-owned action as pending on the strength of the log alone.** A done step that reads as open generates phantom work — the user re-does something already handled, or wastes attention on a ghost.

This rule applies at every point actions are listed or mentioned: orient, mid-session, offload, any context. The procedure:

- **Verifiable by Claude** (file/folder present, setting visible, content readable): **check it first.** If confirmed done, close it in the SSOT and don't surface it. If confirmed not done, surface it.
- **Not verifiable by Claude** (external action — message sent, call made, person met, something done on another machine or outside Drive): **do not assert it is still pending.** Surface it as unverified and ask the user to confirm — then close it if done.

The default assumption is *it may be done*. The burden is on Claude to check, not on the user to remember to report.

*Why: the system's job is to surface real work, not to re-generate tasks the user already completed. Every phantom action is a small trust failure — and they compound.*

---

## Kernel file index

| File | Role | Status |
|------|------|--------|
| `CLAUDE-KERNEL.md` | This file — entry point and engine rules | ✅ built |
| `GLOSSARY.md` | Defines every term and command | ✅ built |
| `ORIENT.md` | Session-start orientation protocol | ✅ built |
| `STRESS-TEST.md` | Instance setup + repair; escalates to help-me | ✅ built |
| `NEW-PROJECT.md` | New project flow (merges CW + Chat) | ✅ built |
| `SSOT-TEMPLATE.md` | The Decisions Log format every project uses | ✅ built |
| `OFFLOAD.md` | Session-close + autosave: write decisions/actions to the SSOT | ✅ built |
| `PROFILE-GENESIS.md` | Builds a new user's profile — 3 openers, friction diagnosis, first win | ✅ built |
| `HELP-ME.md` | Troubleshooter + red alert: task branch & person branch, red lines | ✅ built |
| `FEEDBACK.md` | User → maker feedback loop: articulate request, pre-fill the form link | ✅ built |
| `REVIEW.md` | Independent review gate — the 5-check bar, grounding discipline, coverage report; on-demand + pre-ship | ✅ built |
| `GRILL-ME.md` | Design-tree interview protocol — one question at a time, recommended answers, dependency ordering, explore-before-ask; adapted from Matt Pocock's `grilling` skill | ✅ built |
| `UPDATE.md` | Kit update run protocol — dumb-stable runner (snapshot→check→consent→write→verify) | ✅ built |
| `MIGRATIONS.md` | Per-version upgrade ledger — the version-specific blocks UPDATE.md applies | ✅ built |
| `TEACHING-LOOP.md` | Teaching scaffold — mastery standard, the loop, onboarding-as-teaching | ✅ built |
| `DESIGN-RATIONALE.md` | The settled "why" behind non-obvious decisions — answers "why not X?" without relitigating | ✅ built |
| `SKUNKWORKS-TEMPLATE.md` | Project-layer idea-inbox template — copy into a project as `Skunkworks - [PROJECT].md` when stray ideas start piling up; optional per project | ✅ built |
