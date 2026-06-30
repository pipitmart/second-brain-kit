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
*0.5 = Fiverr-worthy guided product — install-friction reduction. Cut from v0.4 on 27 Jun 2026; v0.4 is the public/install baseline, v0.5 is not yet released.*
- **(shipped 28 Jun)** `/orient` is now README-first: before running the standard 6-step protocol, Claude reads the project README and executes any custom `/orient` protocol defined there (look for `## /orient` heading). Standard steps run only if no custom protocol is found. Enables non-standard project types (e.g. COS pan-project layer) to define their own orient flow without a new command or user friction. Any project can override by documenting its protocol in its README. *(kernel/ORIENT.md — README-first rule added above Step 0.)*
- **(shipped 28 Jun)** Data-integrity guardrail — kernel **§11** added (`CLAUDE-KERNEL.md`): never write an assumed/guessed/default-filled value (times, timezones, IDs, dates, names) into the SSOT or any system file — a value must be loaded from profile/project data, observed this session, or user-confirmed, else **ask**. Operationalized in **`ORIENT.md`** (new *Load-first rule*: the read-only load Steps 0–5 is non-skippable even when a session opens on a direct question; only the Step 6 *write* is deferrable, and only if stated aloud) and **`OFFLOAD.md`** (no-fabrication rule extended to assumed/default values). *Trigger: a session opened on a direct question skipped the profile load, then back-filled the session timezone from a nationality guess (SGT) instead of the Profile's recorded zone (`Asia/Ho_Chi_Minh`/ICT) and wrote it to the SSOT — caught + corrected; guardrail added so it can't recur.*
- **(shipped 29 Jun)** Action-integrity rule — kernel **§12** added (`CLAUDE-KERNEL.md`): never surface a user-owned open action as pending without first verifying its current state. Verifiable items (file/folder present, setting visible) must be checked before being listed; unverifiable items (external actions — message sent, call made) must be flagged as unverified, not asserted pending. Default assumption is *it may already be done* — burden on Claude to check, not on the user to report. Operationalized in **`ORIENT.md`** Step 3 (updated to reference §12 and cover the unverifiable case explicitly). Applies at orient, mid-session, offload, and any context where open actions are listed. *Trigger: a manual cleanup item (delete two files) was surfaced as still-open without checking — both files were already gone.*
- **(shipped 29 Jun)** START-HERE.md "it forgets" hook retired. Opening rewritten to acknowledge Claude's native cross-chat memory, then pivot to what SB provides that Claude's built-in memory still can't: structured auditable decisions in the user's own Drive, per-project source of truth, cross-machine consistency, and drives the next step. Four value bullets updated accordingly; before/after and referral tagline revised to match. *Trigger: Claude shipped native cross-chat memory ~Mar 2026 — the lead hook became factually inaccurate and a liability with technical recipients who know about it.*
- **(shipped 29 Jun)** START-HERE.md bootstrap prompt — structure-check step added. New **step 2** in the copy-paste bootstrap box: before installing anything, Claude opens ROOT and confirms the three folders (kernel/, profile-templates/, skills/) are present, populated, and not double-nested (ROOT-in-ROOT); if anything is loose, missing, or doubled it STOPS and gives the one fix (delete → re-unzip → drag ROOT in once, don't open/rename) before continuing. Old steps 2–6 renumbered 3–7. Runs at cold start before any skill is installed, so it catches a botched unzip-rename-drag at turn one. *Trigger: Samantha's install (user007) corrupted the folder structure during the manual drag (ROOT nested in ROOT + loose skill files); undetected until Genesis broke, forcing a full teardown + redo mid-call. The check turns silent late corruption into a named, turn-one fix — zero new manual steps.*
- **(shipped 29 Jun)** Kit relicensed — **MIT replaced with a personal-use / no-resale license** (`LICENSE.md` added to ROOT; terms line + footer reconciled in START-HERE). You may use the Kit for your own work (incl. commercial) and pass it free to a friend; you may not sell, repackage, or include it in a paid product/course/training without permission. *Trigger: the kit was MIT-licensed — which explicitly grants resale/redistribution, the opposite of intent — surfaced when assessing redistribution risk from an AI-trainer installer (Charmain). Note: copies already shipped under MIT keep that grant; new sends carry the new license. **Action: swap the MIT file in the git repo for `LICENSE.md` — until then the repo still licenses MIT.***
- **(shipped 30 Jun)** `/new-project` — folder-creation flow fixed (`kernel/NEW-PROJECT.md`). Step 0 now has an explicit branch for "folder doesn't exist yet": if the new project is a sibling of ROOT (standard case), Claude cannot create it — bash's `mnt/` paths are a sandbox construct, not Drive's folder hierarchy, so `mnt/ROOT/../[Name]/` writes to sandbox memory and disappears at session end. Fix: Claude stops at Step 0 and prompts the user to create the Drive folder manually, then mount it, before any files are written. If the project is a child of an already-mounted folder (e.g. a sub-project inside a mounted container), Claude can create it via bash — that case is explicitly allowed. Step 4 updated to assert the folder must already exist before building. *Trigger: HCM Medical Centre session — Claude ran mkdir on `mnt/ROOT/../HCM Medical Centre/`; folder landed in sandbox void, never reached Drive; root-caused in Project SB Sess.38 (30 Jun).*
- **(parked, not built)** Optional Todoist capture module + other branch experiments (NanoClaw, etc.) — designs banked in Project SB Skunkworks; merge into live only on passing a stated bar (per kernel §10 Preservation).
- **(candidate)** Data-loss recovery flow (red-team 27 Jun): missing-file detection in /stress-test + /orient, recovery runbook in /help-me. See Project SB.

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
