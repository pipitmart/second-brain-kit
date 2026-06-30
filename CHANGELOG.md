# CHANGELOG — Second Brain system

*Lives in the live ROOT. The system's version is recorded here — **not** in the folder name (the live folder is just `ROOT`) and **not** in file headers (live docs are versionless). This file ships with the kit so recipients can see what changed between versions.*

## How versioning works
- The live folder is always named `ROOT`. Version numbers live **here (CHANGELOG only)** — not in folder names, not in file headers.
- We version **releases, not edits.** The live ROOT runs ahead of the last shipped
  version; a new number is cut at a coherent feature boundary.
- At each release cutover: snapshot the outgoing version as a header-marked archive
  (e.g. `ROOT v2.2` stored in Project SB), bump the live header + this changelog.
- **From 13 Jun 2026 — one product-version line.** ROOT versioning ended at **2.3** (the
  last root tag); the system is now versioned as the **SB product number (0.4 → 1.0)**,
  and **v2.3 = the 0.4 baseline.** A parallel root-vX line was retired to avoid the
  version-collision trap (folder/header/prefs disagreeing). Roadmap + per-version gates
  live in the Project SB Decisions Log.

## Release-cut checklist (do this when a gate passes)
1. Rename `## Unreleased` → `## vX.X — FINAL [date]`
2. Add a **3-line user-facing summary** at the top of that block — what a recipient gets in this version, in plain English (not kernel-dev notes). This is the release note that goes with the kit.
3. Open a new `## Unreleased` block above it for the next version's accumulating changes.
4. Re-zip the kit and commit to git.

*The items listed under Unreleased are kernel-dev notes (for maintainers + future Claude instances). The user-facing summary is written fresh at release cut — don't conflate the two.*

---

## Unreleased — SB v0.5 in development
*0.5 = Fiverr-worthy guided product — install-friction reduction. Changes shipped in v0.4.1 are listed there; this block holds what's coming next.*
- **(parked, not built)** Optional Todoist capture module + other branch experiments (NanoClaw, etc.) — designs banked in Project SB Skunkworks; merge into live only on passing a stated bar (per kernel §10 Preservation).
- **(candidate)** Data-loss recovery flow (red-team 27 Jun): missing-file detection in /stress-test + /orient, recovery runbook in /help-me. See Project SB.

## v0.4.1 — patch 30 Jun 2026
**What's new since v0.4:** The kit now catches a corrupted unzip at turn one (START-HERE step 2 checks your folder structure before anything installs). The `/new-project` command no longer silently fails when creating a project folder — it now stops and tells you to create the folder in Drive first. The license has been updated from MIT to a personal-use / no-resale license. Minor fixes to the write-access recovery flow and the "it forgets" hook in START-HERE (Claude now has native memory; the pitch is updated to reflect what SB still adds on top of that).
- **(30 Jun)** START-HERE — structure-check step added (step 2): catches ROOT-in-ROOT and loose files from a botched unzip before genesis runs.
- **(30 Jun)** `/new-project` — folder-creation fixed: Claude stops and prompts you to create the Drive folder manually before writing any files (bash sandbox can't create Drive siblings).
- **(29 Jun)** License — MIT replaced with personal-use / no-resale (`LICENSE.md`). Use freely for your own work; don't sell or repackage without permission.
- **(29 Jun)** START-HERE — "it forgets" hook retired; updated to reflect Claude's native memory and what SB adds on top.
- **(28–29 Jun)** Kernel — §11 (data integrity: never write guessed values to the SSOT) and §12 (action integrity: verify user-owned open actions before surfacing them) added.
- **(27 Jun, post-v0.4)** Write-access fix — `kernel/CLAUDE-KERNEL.md §4` + `ORIENT.md` Step 0: Cowork can attach folders read-only; fix is `request_cowork_directory` in-session.

## v0.4 — FINAL 27 Jun 2026 — install baseline
**What you get in v0.4 (stabilized core):** a self-contained second-brain kit — the engine (kernel) ships identical to everyone, while your personal profile and per-project decision logs stay yours, in your own Drive. Guided setup (`/stress-test`, `/genesis`) and a daily loop (`/orient`, `/offload`) that remembers your decisions and drives the next step. Live files are versionless (the version lives here), and a new Preservation rule protects your system before any change.
*v2.3 dev opened 12 Jun 2026 immediately after the v2.2 snapshot; re-tagged SB v0.4 on 13 Jun 2026 (single-version-line switch); cut FINAL 27 Jun 2026 and committed to git as the 0.4 install baseline (the build used for Samantha + Charmain).*
- **Preservation principle added (27 Jun):** `kernel/CLAUDE-KERNEL.md` §10 — preservation as a first-class duty; ROOT is shared infra (edit it deliberately, default to the project layer); restore-point-before-merge; experiments parked + gated; the COS drift check. Thin-pointer skills read the kernel live — no `.skill` repackage needed.
- **(cornerstone, in progress)** genesis **Demo/Live** branch: Demo = guided
  taste-first run on a sample persona in a separate demo-profile folder (never touches
  `profile/`); Live = the real once-per-user genesis. Demo doubles as a setup
  diagnostic and teaches the orient/offload loop + the /help-me reflex.
- **`.skill` zips repackaged versionless (12 Jun):** version numbers removed from zip
  filenames (`orient.skill`, not `orient-v2.2.skill`) — same principle as the ROOT
  folder rename: versions live in headers + this changelog, never in names. All six
  rebuilt from current sources (genesis carries the Demo/Live fork; offload the swept
  description), round-trip verified. Old `*-v2.2.skill` files to be deleted manually.
- **Live docs made versionless (13 Jun):** `*Part of: ROOT vX.X*` and `*Version: ...*` lines removed from all 15 kernel + profile files — version lives in CHANGELOG only, never in file headers.
- **Orient verifies user-owned actions (14 Jun):** `kernel/ORIENT.md` Step 3 now requires checking any user-manual open action against real state (folder contents, Settings, what's on screen) before surfacing it as pending — a step done outside Claude's visibility must not re-surface as phantom work. Kills the stale-record failure mode. Thin-pointer skills read the kernel live, so no `.skill` repackage needed.
- **Skunkworks idea-inbox template added (25 Jun):** `kernel/SKUNKWORKS-TEMPLATE.md` — a project-layer template (copy into a project as `Skunkworks - [PROJECT].md`) for capturing raw, ungated ideas outside the SSOT's read path, keeping orient cost flat. Optional per project. `kernel/CLAUDE-KERNEL.md` file index updated; `kernel/NEW-PROJECT.md` Step 4 updated with optional skunkworks creation step.
- **Read-only-folder write fix (27 Jun, post-FINAL):** Cowork can attach a connected folder **read-only** — native Read works but Write/Edit fail (*"path outside the connected folder"*) though the folder is genuinely writable; **fix = `request_cowork_directory` to re-grant write.** Documented in `kernel/CLAUDE-KERNEL.md §4` + operationalized in `ORIENT.md` Step 0. Install-relevant — protects the write loop on guided installs. *(Live ROOT now ahead of the git `v0.4` tag + `SB v0.4` Drive snapshot — re-zip/re-tag before the next kit send.)*
- **(planned)** validate Demo on user004.

## v2.2 — FINAL 12 Jun 2026 — RETIRED (snapshot archived in Project SB / `ROOT v2.2`)
**Baseline (11 Jun):** kernel/profile split — 10 kernel files (CLAUDE-KERNEL, GLOSSARY,
ORIENT, SSOT-TEMPLATE, NEW-PROJECT, OFFLOAD, STRESS-TEST, PROFILE-GENESIS, HELP-ME,
TEACHING-LOOP), 5 profile files, 6 thin-pointer skills, START-HERE, profile-templates.
Obsidian removed. Private GitHub repo. In-place editing on Drive via native file tools.

**Final-state additions (11–12 Jun, included in the snapshot):**
- **Boot redesigned:** ROOT located by fingerprint (`kernel/CLAUDE-KERNEL.md`), no
  hardcoded folder ID; ROOT defined as one universal Drive-top-level folder beside
  projects; live folder named just `ROOT`.
- **START-HERE:** "Why this exists" lead section (kills "isn't this just a skill /
  isn't Cowork enough"; JARVIS positioning). Setup entry = bootstrap paste-prompt.
- **Standing-prefs constitution:** version-agnostic; decaying facts derived, not
  hardcoded.
- **Kernel-anchor concept retired:** `CLAUDE-KERNEL.md` v1.2 §1–§3 + `GLOSSARY.md`
  v1.2 — fingerprint boot is the only locator; any profile ID is a convenience pointer
  that loses to the fingerprint. GLOSSARY "Anchor" → "Fingerprint."
- **Fresh-mount listing caveat:** a just-connected folder can list empty while direct
  Read works; kernel §3 — verify via Read / retry before concluding empty.
- **v2.1 lineage notes swept** from `NEW-PROJECT.md`, `OFFLOAD.md`,
  `skills/offload/SKILL.md`.

## v2.1 — prior — origin
README-GLOBAL based, pre-split, Obsidian in the loop. Retired. Snapshot archived in
Project SB as the origin point (12 Jun 2026).
