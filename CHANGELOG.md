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
- **Version-header shape is a load-bearing interface (do not break it).** The update mechanism (`kernel/UPDATE.md`) detects new versions by parsing the **first released header** in this file. Released headers MUST match `## vX[.Y[.Z]] — <FINAL|patch> DD Mon YYYY[ — optional subtitle]`; the leading `## Unreleased …` block is ignored by detection. Change this shape and every install's update check silently breaks. If the format ever must change, ship a migration that teaches installs the new parse first.

## Release-cut checklist (do this when a gate passes)
1. Rename `## Unreleased` → `## vX.X — FINAL [date]`
2. Add a **3-line user-facing summary** at the top of that block — what a recipient gets in this version, in plain English (not kernel-dev notes). This is the release note that goes with the kit.
3. Open a new `## Unreleased` block above it for the next version's accumulating changes.
4. Re-zip the kit and commit to git.
5. **Verify the new header parses** — it must match `## vX[.Y[.Z]] — <FINAL|patch> DD Mon YYYY[…]` (the update-detection contract above). A malformed header ships a silent break to every install.
6. **Verify the new MIGRATIONS block names every changed file** — diff the kit's file list against the previous release and confirm the block's delivery set covers every added or changed file (including changed `.skill`s). The updater fetches only what blocks name — an unnamed file silently never reaches updating installs.

*The items listed under Unreleased are kernel-dev notes (for maintainers + future Claude instances). The user-facing summary is written fresh at release cut — don't conflate the two.*

---

## Unreleased — SB v0.7 in development
*(Scope not yet set at this cut — see `Backlog Parked - SB Product.md` → "0.6 and beyond" for candidates. Fill in this line at the next roadmap review rather than guessing a forward/improvement cornerstone here.)*

## v0.6 — FINAL 06 Jul 2026 — updates + recovery
**What's new in v0.6:** Claude can now check for and apply kit updates on its own — say "update SB" any time, or it'll offer when one's overdue — and it only ever pulls in changes that have actually shipped, never work-in-progress ones, with your OK at every step and without touching your own projects or profile content. `/help-me` gained a third mode for when nothing's technically broken but you feel lost — it re-teaches the loop hands-on instead of pointing you at docs. Also under the hood: a session-counting bug that could double-count a sitting is fixed.
- **[03 Jul 2026] `/help-me` Branch C — recovery (improvement cornerstone):** third branch for "I'm lost, nothing's broken" — re-teaches the loop by running one real loop on the user's own work (calibrate → orient together → one real action → offload → persistence proof), never from docs. `/orient`'s gap-check now offers it on benign-but-lost answers (decline logged, ~2-week re-offer cap); GLOSSARY updated to three branches. *(**Done 06 Jul 2026:** `help-me.skill` description updated to name all three branches; `ROOT/skills/help-me.skill` re-zipped in place — re-upload in Settings + git push still owed.)*
- **[03 Jul 2026] Bash-mount staleness rule (safety):** `ORIENT.md` Step 0.6 + `OFFLOAD.md` freshness gate now name the bash-sandbox mount as a third stale surface — when bash and file-tool views of a file disagree, the file tool wins; bash writes are unsafe until the mount matches cloud `fileSize`. (Second live sighting 03 Jul, PMX repro — `profile/LAB.md`.)
- **[04 Jul 2026] Kit update mechanism (forward cornerstone):** new `kernel/UPDATE.md` (dumb-stable run protocol — snapshot → check → consent → additive write → verify; the OLD install's protocol executes the NEW kit's migration blocks) + `kernel/MIGRATIONS.md` (append-only per-version ledger; v0.5 Noticeboard + v0.6 blocks backfilled). Pull-to-system detection: one raw-fetch of this CHANGELOG behind a single shared 7-day stamp in `profile/STACK.md`, read by every automatic door (orient Step 0.7, stress-test Gate 0.5, help-me A+C) and bypassed by the explicit phrase "update SB". Migrations are additive-only on profile *structure* — user *content* is never modified or guessed (kernel §11). Genesis now stamps the kit version; `profile-templates/STACK.md` carries the placeholder. Pre-0.6 installs get one hand-carried bootstrap prompt (maintainer-carried; not part of the kit), self-serve from 0.6 on.
- **[04 Jul 2026] STRESS-TEST.md skill-install step made count-agnostic:** Step 1 hardcoded "the six `.skill` files" — stale since v0.4 added `feedback` and v0.5 added `grill-me`/`review` (nine files ship today). The step now names the `skills/` folder listing as the authoritative set and installs every file present; no count or list to rot at future releases. (Caught by a live `/stress-test` reporting "all six" against a nine-file folder.)
- **[05 Jul 2026] Orient gates bind every session door + Arguments rule in all skills (safety):** `ORIENT.md`'s non-overridable gates (Step 0 access / 0.5 demo / 0.6 freshness / 2.5 drain) now explicitly bind ANY session-opening protocol — including project-defined custom daily doors — not just `/orient`. And all nine `.skill` thin pointers now carry a binding **Arguments rule**: content passed as arguments is handled within or after the protocol, never instead of it. *(Live failure 05 Jul: a question passed as `/orient` arguments displaced the entire protocol — the kernel's Load-first rule lives in `ORIENT.md`, which the skipping session never read; the rule now also lives at the skill layer it can't skip.)* *(At release cut: all nine `.skill` files re-zipped — re-upload in Settings.)*
- **[05 Jul 2026] Standing notes codified in `ORIENT.md` Step 2.5 (safety):** notes under a `## Standing notes` heading on the Noticeboard are read-every-session context — never matched, drained, or deleted by any project's drain; only the pinning project retires them. Previously only the board's own inline header protected them.
- **[06 Jul 2026] Session numbering fixed to `/orient` only — reopen, don't renumber (safety):** a session that ran `/offload` and then kept going in the same sitting with no fresh `/orient` was getting split into two numbered Session Log rows (live incident: Education Sess.6/7, merged back by hand 06 Jul). Fix: a session number is minted in exactly one place, `/orient` Step 6 — never at `/offload`. Three branches at offload time, by what actually happened this sitting: **(1)** `/orient` ran and opened a row → close normally; **(2)** `/orient` ran but Step 6 was deferred (a quick-question session) → mint the number now, open-and-close together; **(3)** `/orient` never ran this sitting at all → reopen the most recently closed row (extend end time, recompute `~Hrs`, append as a marked continuation) instead of inventing a new one; falls back to a fresh row if the target was already compacted to Archive. A project's separate Session Ledger (if it has one) mirrors whichever branch fires, so the two files never drift. `kernel/SSOT-TEMPLATE.md → Session Log` carries the full rule; `ORIENT.md` Step 6 and `OFFLOAD.md`'s Session Log bullet point to it. *(Grilled via `/grill-me` first (one blocking question: reopen-the-row, chosen over always-new-number or a hard re-orient block) → built → Fable `/review` caught a real 🔴 in the first pass — the two-branch version would have wrongly merged a legitimate deferred-Step-6 session (SB Product's own Sess.41 is a real example) into the reopen branch — fixed by splitting out branch 2. Project-defined custom doors with their own Session Log — e.g. COS's `/today`/`/close` — are not automatically covered; flagged to mirror separately, same precedent as the freshness gate.)*
- **[06 Jul 2026] Released-version eligibility gate in `kernel/UPDATE.md` (safety):** the runner now applies only migration blocks whose version is a **released** CHANGELOG header — new step 4a cross-checks every precondition-applicable block against the newest released version (same parse contract detection uses; one definition of "released"), holds and plainly reports any block still under `## Unreleased` or absent from the CHANGELOG, and applies nothing if no released version parses. A held block becomes eligible automatically at its version's cut. Consent summaries (step 5) now also front-load the manual steps (`.skill` drags + Streaming-Drive cleanup) so nothing lands as an end-of-run surprise; `MIGRATIONS.md` authoring rules gain the skill-churn floor (skip-proof rules stay in the binary) and the released-only note. *(Live case 06 Jul: a bootstrap run offered an unreleased block and the user approved it — only the model's own unprompted CHANGELOG check held it back. Unreleased = uninstallable is now structural, not model diligence.)*
- **[04 Jul 2026] New `STRESS-TEST.md` project-defined instance setup step (reviewed + repositioned 06 Jul):** a generic, person-agnostic hook — if the active project's README defines local per-instance setup (e.g. a scheduled-task backstop), offer to (re)create it on a fresh machine, and prefer a calendar-anchored reminder over a local Cowork scheduled task wherever the project supports one (a local scheduled task doesn't sync via Drive and doesn't survive a machine switch). *(Process trail: originally merged direct from a COS session without the kernel-change ceremony and flagged ⚠️ UNREVIEWED. Retroactive Fable `/review` ran 06 Jul 2026 — content confirmed sound; one 🟡 fixed same day: as "Step 1.5" it sat before Load-ROOT and Gate 0, at a point where the project folder isn't yet confirmed mounted or its README loaded, so it would silently no-op on exactly the fresh machines it exists for — relocated to run after Gate 0. Flag cleared; git push of both changes still owed at next repo sync.)*

## v0.5 — FINAL 02 Jul 2026 — release well on git
**What's new in v0.5:** a pre-flight checklist catches setup snags before they cost you; clearer help when Drive accounts don't match; notes now route between your projects automatically — plus the new `/review` and `/grill-me` commands.
- **(0.5-2)** `PRE-FLIGHT.md` added to kit ROOT — four pre-install checks (paid Claude desktop app, Drive for Desktop syncing, same Google account, kit unzipped) — with a new step 0 in `START-HERE.md` pointing to it.
- **(0.5-3)** Bootstrap prompt (`START-HERE.md` step 2) now names account mismatch as the first thing to check when ROOT reads empty or can't be found.
- **(0.5-5)** Thin front-door README added to the GitHub repo; `START-HERE.md` remains the kit's own front door (ROOT carries no README by design).
- **(0.5-6)** Star CTA added to the install call-close script and the post-install follow-up.
- **Cross-project Noticeboard routing (cornerstone):** project-tagged notes pinned to `profile/Noticeboard.md` now route automatically — `/orient` (new Step 2.5) drains notes tagged for the active project into its Decisions Log; `/offload` pins outgoing notes; new `Noticeboard tag` column in `PROJECTS.md` (live + template); tags assigned at `/new-project`; blank board template copied in at genesis.
- **New command `/grill-me`** (`kernel/GRILL-ME.md` + skill): branch-by-branch design interview before a build — one question at a time, each with a recommended answer. Adapted from Matt Pocock's `grilling` skill.
- **New command `/review`** (`kernel/REVIEW.md` + skill): independent review gate — checks an artifact against real state (accuracy, staleness, consistency, dangling references, clarity); runs on demand and before every release.
- **`/orient` freshness gate (new Step 0.6):** local files are verified against the cloud before anything is trusted or written; `/offload` re-checks before each write and confirms every write landed.
- **`PROFILE-GENESIS.md`** positioning updated to match `START-HERE.md` — acknowledges Claude's native memory, pitches what SB adds on top.
- **`/offload` cross-project learnings reworked — route, don't write:** reusable learnings now route via the Noticeboard (or `profile/LAB.md` for system/infra findings) instead of being written directly into `profile/`, `STACK`, or the kernel from a project session.

## v0.4.1 — patch 30 Jun 2026
**What's new since v0.4:** The kit now catches a corrupted unzip at turn one (START-HERE step 2 checks your folder structure before anything installs). The `/new-project` command no longer silently fails when creating a project folder — it now stops and tells you to create the folder in Drive first. The license has been updated from MIT to a personal-use / no-resale license. Minor fixes to the write-access recovery flow and the "it forgets" hook in START-HERE (Claude now has native memory; the pitch is updated to reflect what SB still adds on top of that).
- **(30 Jun)** START-HERE — structure-check step added (step 2): catches ROOT-in-ROOT and loose files from a botched unzip before genesis runs.
- **(30 Jun)** `/new-project` — folder-creation fixed: Claude stops and prompts you to create the Drive folder manually before writing any files (bash sandbox can't create Drive siblings). *(Verified 01 Jul: this fix is live in `kernel/NEW-PROJECT.md` Step 0 — matches git exactly. Only this CHANGELOG line was missing on the Drive side; the actual fix was never lost.)*
- **(29 Jun)** License — MIT replaced with personal-use / no-resale (`LICENSE.md`). Use freely for your own work; don't sell or repackage without permission. **Git repo LICENSE swapped MIT → `LICENSE.md` (30 Jun 2026) — repo no longer licenses MIT.**
- **(29 Jun)** START-HERE — "it forgets" hook retired; updated to reflect Claude's native memory and what SB adds on top.
- **(28–29 Jun)** Kernel — §11 (data integrity: never write guessed values to the SSOT) and §12 (action integrity: verify user-owned open actions before surfacing them) added.
- **(27 Jun, post-v0.4)** Write-access fix — `kernel/CLAUDE-KERNEL.md §4` + `ORIENT.md` Step 0: Cowork can attach folders read-only; fix is `request_cowork_directory` in-session.

*(Corrected 01 Jul 2026: this release-cut existed in git (`pipitmart/second-brain-kit`, committed 30 Jun) but was never applied to this file — the Drive-side CHANGELOG still had every v0.4.1 item sitting under "Unreleased" as if the version had never shipped. Caught only by a direct git cross-check; the reconstruction earlier the same day, based on Drive artifacts alone, missed it. See `profile/LAB.md`.)*

## v0.4 — FINAL 27 Jun 2026 — install baseline
**What you get in v0.4 (stabilized core):** a self-contained second-brain kit — the engine (kernel) ships identical to everyone, while your personal profile and per-project decision logs stay yours, in your own Drive. Guided setup (`/stress-test`, `/genesis`) and a daily loop (`/orient`, `/offload`) that remembers your decisions and drives the next step. Live files are versionless (the version lives here), and a new Preservation rule protects your system before any change.
*v2.3 dev opened 12 Jun 2026 immediately after the v2.2 snapshot; re-tagged SB v0.4 on 13 Jun 2026 (single-version-line switch); cut FINAL 27 Jun 2026 and committed to git as the 0.4 install baseline (the build shipped to user007; user008 onward receive v0.4.1).*
- **Preservation principle added (27 Jun):** `kernel/CLAUDE-KERNEL.md` §10 — preservation as a first-class duty; ROOT is shared infra (edit it deliberately, default to the project layer); restore-point-before-merge; experiments parked + gated; the COS drift check. Thin-pointer skills read the kernel live — no `.skill` repackage needed.
- **(cornerstone, in progress)** genesis **Demo/Live** branch: Demo = guided
  taste-first run on a sample persona in a separate demo-profile folder (never touches
  `profile/`); Live = the real once-per-user genesis. Demo doubles as a setup
  diagnostic and teaches the orient/offload loop + the /help-me reflex.
- **`.skill` zips repackaged versionless (12 Jun):** version numbers removed from zip
  filenames (`orient.skill`, not `orient-v2.2.skill`) — same principle as the ROOT
  folder rename: versions live in headers + this changelog, never in names. All six
  rebuilt from current sources (genesis carries the Demo/Live fork; offload the swept
  description), round-trip verified. Old `*-v2.2.skill` files retired.
- **Live docs made versionless (13 Jun):** `*Part of: ROOT vX.X*` and `*Version: ...*` lines removed from all 15 kernel + profile files — version lives in CHANGELOG only, never in file headers.
- **Orient verifies user-owned actions (14 Jun):** `kernel/ORIENT.md` Step 3 now requires checking any user-manual open action against real state (folder contents, Settings, what's on screen) before surfacing it as pending — a step done outside Claude's visibility must not re-surface as phantom work. Kills the stale-record failure mode. Thin-pointer skills read the kernel live, so no `.skill` repackage needed.
- **Skunkworks idea-inbox template added (25 Jun):** `kernel/SKUNKWORKS-TEMPLATE.md` — a project-layer template (copy into a project as `Skunkworks - [PROJECT].md`) for capturing raw, ungated ideas outside the SSOT's read path, keeping orient cost flat. Optional per project. `kernel/CLAUDE-KERNEL.md` file index updated; `kernel/NEW-PROJECT.md` Step 4 updated with optional skunkworks creation step.
- **Read-only-folder write fix (27 Jun, post-FINAL):** Cowork can attach a connected folder **read-only** — native Read works but Write/Edit fail (*"path outside the connected folder"*) though the folder is genuinely writable; **fix = `request_cowork_directory` to re-grant write.** Documented in `kernel/CLAUDE-KERNEL.md §4` + operationalized in `ORIENT.md` Step 0. Install-relevant — protects the write loop on guided installs. *(Resolved in v0.4.1.)*
- **(done — gate not passed)** Demo validated on user004: scored 5.6, below threshold. Install friction addressed in v0.4.1; gate testing continues under v0.5.

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
