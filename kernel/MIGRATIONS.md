# MIGRATIONS.md — Per-Version Upgrade Ledger
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic. Append-only. One block per version — the version-specific intelligence `kernel/UPDATE.md` reads and applies.*

> **What this is.** `UPDATE.md` is the dumb, stable runner; **this file is the brains.** Every kit change that needs *anything beyond a plain file overwrite* — especially any addition to the profile layer's structure — ships as a block here. The OLD install's `UPDATE.md` fetches the NEW kit's `MIGRATIONS.md` and executes these blocks. That is why this file is append-only and why each block is self-describing: a forked or older install's Claude must be able to read a block cold and apply it correctly with no other context.

> **The invariant every block obeys (semantic, not spatial).** Blocks may **add structure, additive-only** (create a file from a template, add a column, add a header line). They may **never** modify, delete, or infer user **content**. Any value a block writes must be **loaded, observed, or user-confirmed** (kernel §11) — never guessed. A change that cannot be expressed as *precondition + additive action* is a change not yet understood well enough to ship.

---

## Block shape (authoring rules)

Every block uses exactly this shape:

```
### v0.X → [name]
- Why: [one line — the rationale a forked install's Claude needs to translate intent]
- Tier: safety | feature
- Precondition: [machine-checkable state test — file/column/line present or absent]
- Action: [additive steps; reference templates, never inline their contents]
- Values: [which values are derived, and the §11 confirm rule for each — or "none"]
- Verify: [post-checks; DEMAND /stress-test when the block touches skills, START-HERE, or STRESS-TEST]
```

Authoring rules:
- **State-canonical.** The Precondition is checked against the *actual install*, not the version stamp. A satisfied precondition makes the block a **no-op** — this is what makes the whole mechanism idempotent and retries safe.
- **Additive only.** No block ever rewrites or removes user content. Structure in; content never touched except by explicit user confirmation.
- **Templates by reference.** Point at `profile-templates/…`; never paste template bodies into a block (they'd rot).
- **Tier drives surfacing.** `safety` = surfaced every orient until applied; `feature` = surfaced once. (Read by `UPDATE.md`'s channel step.)
- **Stress-test escalation.** If a block's changes touch **skills, `START-HERE.md`, or `STRESS-TEST.md`**, its Verify line **must DEMAND a full `/stress-test`** — instance-level risk earns instance-level proof.
- **Enumerate the full delivery set.** The runner fetches only the files blocks name (per-file model — kept by explicit decision, 04 Jul 2026, over a wholesale kernel swap: wholesale overwrite risks disrupting daily flows on user-modified kernels). At authoring time, diff the kit's file list against the previous release and name every added or changed file — kernel/, kit-root (CHANGELOG, LICENSE, START-HERE, PRE-FLIGHT), profile-templates/, and changed `.skill`s. The release-cut checklist enforces this (step 6). A file no block names silently never reaches updating installs.
- **Kernel-swap-only changes still get a block.** When a version's changes are pure kernel-file overwrites (no profile-layer action), the block still exists for ledger completeness: Precondition = a content check on the incoming file, Action = "delivered by the kernel swap — no profile write," Verify = the new content is present after the swap.
- **Minimize `.skill` churn — with a hard floor.** Every changed `.skill` binary costs the user a manual re-drag in Settings (the one irreducibly manual step), so keep the thin pointers pure-delegation — logic lives in the kernel `.md` the pointer reads, and routine kernel changes shouldn't touch binaries. **The floor: skip-proof rules stay in the binary.** A rule whose job is to bind even when the kernel `.md` is never read (e.g. the Arguments rule — added after a live session skipped the pointer's read step entirely) cannot be delegated to the file it exists to survive missing; never optimize such a rule out of the binary to save a re-drag.
- **A block may only be applied once its version is released.** The runner enforces this structurally (`UPDATE.md` step 4a: block version ≤ newest released CHANGELOG header, else held) — but authors should know it: a block sitting here under an uncut version is *visible* to every `main`-fetch and *installable by nothing*. That is by design; don't work around it by mislabeling a block with an already-released version.

---

## Blocks (oldest first — apply in order from the local version forward)

### v0.5 → Noticeboard cross-project routing
- **Why:** v0.5 shipped automatic cross-project note routing. It needs two profile-layer structures a kernel-only swap cannot deliver — the board file itself, and a routing-tag column in the project index. Without them, `/orient` Step 2.5's drain has nothing to read and cross-project notes are silently lost. This is the archetype the whole update mechanism was built to handle (surfaced by a 0.4.1 install lacking exactly this structure).
- **Tier:** feature
- **Precondition:** `profile/Noticeboard.md` does **not** exist, **OR** the Active table in `profile/PROJECTS.md` has **no** `Noticeboard tag` column. (Either missing → block applies. Both present → no-op.)
- **Action:**
  1. **Profile-layer (additive, the core of this block):**
     a. If `profile/Noticeboard.md` is absent, create it by copying `profile-templates/Noticeboard.md` verbatim (pure structure — no values).
     b. If `profile/PROJECTS.md` lacks the `Noticeboard tag` column, add it to the Active table additively: insert the header cell + separator cell, and one tag cell per existing project row (leave every other column byte-identical).
     c. For each existing Active project row, derive a candidate tag — unique, kebab-case, from the project name (e.g. `SCX Corporate` → `#scx-corporate`), per the `kernel/NEW-PROJECT.md` Step 4 rule — **show it to the user, confirm, then write it.** Ensure no two rows collide.
  2. **Kernel-swap deliveries (no profile write — take-new, conflict-checked per `UPDATE.md` step 7):** the v0.5 kernel/kit changes an older install is missing — new `kernel/GRILL-ME.md` + `kernel/REVIEW.md`; updated `ORIENT.md` (Step 0.6 freshness gate + Step 2.5 Noticeboard drain), `OFFLOAD.md` (pin step + write-confirm + route-don't-write rework), `NEW-PROJECT.md` (tag assignment), `PROFILE-GENESIS.md` (positioning + blank-board copy), `CLAUDE-KERNEL.md` (§8 + file index), `GLOSSARY.md`; new `START-HERE.md` (step 0 + account-mismatch branch) + `PRE-FLIGHT.md` (new kit-root file); kit-root `CHANGELOG.md` + `LICENSE.md` (v0.4.1 relicensed MIT → personal-use/no-resale — without this a 0.4-baseline install keeps the retired MIT terms); `profile-templates/Noticeboard.md` + `profile-templates/PROJECTS.md` (tag column). *(A pre-0.5 install takes all differing kernel/ + kit-root + profile-templates/ files new; a 0.5+ install no-ops them.)*
  3. **Skill refresh (new skills — manual install):** `grill-me.skill` and `review.skill` were introduced in v0.5. Give the user the download link for each; they upload in **Settings → Capabilities → Skills**.
- **Values:** the per-row `Noticeboard tag`s are **derived but must be user-confirmed before writing (§11)** — a tag is routing infrastructure that a later rename must not silently break, so it is *stored*, never re-derived at runtime. Show each derived tag; write only what the user confirms. A row the user does not confirm gets an **empty** tag cell and a flag (never a guessed value). The `Noticeboard.md` body carries no values — it is a template copy. No other values.
- **Verify:** `profile/Noticeboard.md` exists and matches the template's shape (headers + "Open notes" section); `profile/PROJECTS.md` Active table has the `Noticeboard tag` column with a confirmed (or explicitly-blank-and-flagged) cell in every row; local `kernel/GRILL-ME.md` + `kernel/REVIEW.md` present; local `LICENSE.md` is the personal-use license (not MIT); run `/orient` — Step 2.5 drain must execute with no error and no ghost-tag warning for the migrated rows. **DEMAND `/stress-test`** — this block installs new skills, so instance-level proof is required.

### v0.6 → update mechanism + recovery + safety
- **Why:** v0.6 delivers the update mechanism itself, the `/help-me` Branch C recovery flow, and the bash-mount staleness safety rule. All of these are kernel-file changes delivered by the swap — **except** the kit-version stamp, which is the one profile-layer structure v0.6 adds so future update checks have a local version to compare against (this is the "retro-write the stamp on the first update run" step).
- **Tier:** safety *(carries the bash-mount data-loss guard; a safety-tier item surfaces every orient until the update is applied)*
- **Precondition (profile-layer part):** `profile/STACK.md` has **no** `SB kit:` stamp line. *(Kernel-file parts are content-checks on the incoming swap — see Action.)*
- **Action:**
  1. **Profile-layer (additive):** if absent, add the stamp line to `profile/STACK.md`: `SB kit: v0.6 · last update-check: [today]`. This is the only profile write in this block; `UPDATE.md` step 10 then normalizes its value at close.
  2. **Kernel-swap deliveries (no profile write — overwrite these files from the new kit, conflict-checked per `UPDATE.md` step 7):** new `kernel/UPDATE.md` + `kernel/MIGRATIONS.md`; `HELP-ME.md` (Branch C recovery); `ORIENT.md` (gap-check benign-but-lost routing · bash-mount third-surface rule in Step 0.6 · the update-check sub-step); `OFFLOAD.md` (bash-mount third-surface rule); `GLOSSARY.md` + `CLAUDE-KERNEL.md` §6 ("update SB" phrase + UPDATE/MIGRATIONS index rows); `STRESS-TEST.md` (version-check health item); kit-root `CHANGELOG.md` (carries the v0.6 notes + the parse-contract header line); `PROFILE-GENESIS.md` + `profile-templates/STACK.md` (genesis stamps kit version).
  3. **Skill refresh:** re-upload **all nine `.skill` files** — every thin pointer gained the binding **Arguments rule** (05 Jul 2026: arguments are handled within/after the protocol, never instead of it); `help-me.skill` additionally now names three branches, not two. Direct links provided at `UPDATE.md` step 8.
- **Values:** the stamp date = today (observed, not guessed). No other values.
- **Verify:** `profile/STACK.md` carries the `SB kit:` line; local `HELP-ME.md` contains "Branch C"; local `ORIENT.md` contains the update-check sub-step, the bash-mount rule, the any-session-door gates-binding line, and the Step 2.5 Standing-notes rule; `kernel/UPDATE.md` and `kernel/MIGRATIONS.md` are present. **DEMAND `/stress-test`** — this block touches all nine skills and boot-relevant kernel files, so instance-level proof is required before the update is considered complete; then `/orient` must come back clean.

---

## Notes for future authors
- The ledger starts at **v0.5** on purpose: it is the first version to add profile-layer *structure* (everything earlier was kernel edits, delivered by swap, or was itself the baseline). A pre-0.5 install (e.g. a 0.4.1 install) is missing exactly the v0.5 structure, which is why its update run begins with the Noticeboard block.
- When you cut a new version, append its block here **before** the release — the release SOP checklist references this file. A version whose changes are all kernel-swap still gets a completeness block (see authoring rules).
- Never renumber or edit a shipped block's semantics — append a corrective block instead (supersede-never-delete, mirroring the SSOT discipline).
