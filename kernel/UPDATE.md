# UPDATE.md — Kit Update Protocol
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic. The one protocol that brings an install to a newer kit version — including profile-layer structural migrations — without losing or inventing user content.*
*Triggered by: `/orient` (cadence-guarded), `/stress-test` (health item), `/help-me` Branches A + C (outdated-as-diagnosis), or the natural-language ask "update SB" / "check for updates".*

> **Read this constraint first — it is the whole design.** This protocol must stay **dumb and stable forever.** All version-specific intelligence lives in `kernel/MIGRATIONS.md` blocks, never here. When an install updates, the **OLD** install's `UPDATE.md` executes the **NEW** kit's `MIGRATIONS.md` — so this file has to be forward-compatible with versions that don't exist yet. The permanent shape is five moves: **snapshot → check → consent → write → verify.** Never add version-specific logic to this file. A migration that needs new behaviour ships that behaviour as data in its own block (precondition + additive action + verify), which this dumb runner reads and applies. The run overwrites `UPDATE.md` itself as part of the kernel swap; the next update runs whatever protocol shipped with the newer kit.

> **The invariant this protocol defends (semantic, not spatial).** Sacred = **user content**, never the `profile/` folder as such. Profile files hold system-owned *structure* (which files exist, columns, schemas, headers) and user-owned *content* (values, answers, customizations). This protocol may **add structure, additive-only**; it may never modify, delete, or infer content. A value lands only by kernel §11: loaded, observed, or user-confirmed — never guessed. *(This supersedes any older "the update never touches `profile/`" claim: true for content, false for structure — the Noticeboard-on-0.4.1 case proved a file-swap can't deliver profile-layer structure.)*

> **Concept in one line.** The public repo is to the kit what the Drive cloud is to a file — the Step 0.6 freshness principle, one level up. Detection compares the local version to the repo's canonical version exactly as Step 0.6 compares a local file to its cloud copy.

---

## The channel — pull-to-system, one shared 7-day guard

- **Canonical remote:** the public repo `pipitmart/second-brain-kit`. Version truth is the top version header in the repo's `CHANGELOG.md`.
- **Detection = one cheap raw fetch:** `https://raw.githubusercontent.com/pipitmart/second-brain-kit/main/CHANGELOG.md`, parse the first released version header, compare to the local `SB kit:` stamp (see below).
- **One shared stamp, one shared guard.** `profile/STACK.md` carries a single line: `SB kit: vX.Y · last update-check: DD Mon YYYY`. **Every automatic door reads this same stamp.** An automatic check fires only if the last check is >7 days old — so no matter how many doors open in a day, the network is hit at most once a week. **Exception: an explicit user ask ("update SB", "check for updates") always fetches, ignoring the guard.**
- **Every automatic door updates the shared stamp's `last update-check` date** whenever it runs a check (found something or not) — that is what makes the guard shared across all doors.
- **Tier-aware surfacing** once a check finds a newer version: a **safety**-tier change surfaces as one line at *every* orient until applied; a **feature**-tier change is mentioned *once*, then stays quiet unless the user asks. (Tier is read from the `MIGRATIONS.md` block — see step 1.)
- **Scheduled background check** (Cowork scheduled task, weekly) is a **documented opt-in only, never a default** — it depends on the desktop app running and adds infra the zero-infra bias avoids.

### CHANGELOG header = load-bearing interface
Detection parses the CHANGELOG's top version header, so its shape is a **contract, not cosmetics.** The parse rule:
- Skip any leading `## Unreleased …` block — it is in-development, not shipped.
- The newest **released** version is the first line matching **`## vX[.Y[.Z]] — <FINAL|patch> DD Mon YYYY[ — optional subtitle]`**. Both release kinds count: a FINAL cut (`## v0.5 — FINAL 02 Jul 2026 — release well on git`) and a point/patch release (`## v0.4.1 — patch 30 Jun 2026`). Extract the version token between `## v` and the first ` —`.
- Compare version tokens numerically, field by field (`0.5` newer than `0.4.1` newer than `0.4`).
- If no line matches this shape, treat detection as **failed** (note-and-proceed) — never guess a version.

*(Verified against the live CHANGELOG at build: `v0.5`/`v0.4`/`v2.2` are `— FINAL`, `v0.4.1` is `— patch`; all parse. Any release cut that changes this header shape silently breaks every install's update check — the constraint is recorded in `CHANGELOG.md`'s own header and in the release-cut checklist for exactly this reason.)*

*This same parse contract also gates **block eligibility** at run time (step 4a below) — detection and eligibility share one definition of "released." A block in the ledger for a version not yet released is held, never applied.*

---

## The run — order of operations

Runs when a check has found a newer version **and** the user said yes (or asked directly). Ten steps, fail-closed throughout.

**1. Fetch.**
   - **Primary — raw per-file fetch at the new tag.** Fetch `MIGRATIONS.md` first (`https://raw.githubusercontent.com/pipitmart/second-brain-kit/vX.Y/kernel/MIGRATIONS.md`), read its blocks (each carries a **Tier** used for surfacing), then fetch only the files the applicable blocks actually name. No zip, no user file-handling. *(Precedent: the live repo was verified via raw fetches at release.)*
   - **Fallback on fetch failure** (network blocked, raw unreachable): give the user a direct link to the Release asset zip, have them download and drag it into the chat; unpack in the sandbox, write natively. Never make the user unzip to a folder themselves.
   - **`.skill` carve-out:** `.skill` files are zip binaries — raw text-fetch cannot carry them. For each changed skill, give a direct download link; the user clicks and drags it into **Settings → Capabilities → Skills** (this step was always manual; the link just adds one click). Handled at step 8, flagged here so no run assumes skills arrive by fetch.

**2. Freshness-gate every target file.** Before touching anything, run the Step 0.6 discipline (local vs cloud `modifiedTime`, **and** the bash-mount third-surface rule — file tool wins on disagreement) on every file this run will write. Stale or uncheckable → halt that file, do not write.

**3. Snapshot before the first write.** Copy `profile/` **and** every file the run will touch into a dated snapshot folder in ROOT (e.g. `ROOT/_update-snapshot-vX.Y-DD-Mon-YYYY/`) — kernel §10 restore point. Recovery is copy-back, never rebuild. No snapshot → no writes. **Fingerprint guard:** if the snapshot includes `kernel/CLAUDE-KERNEL.md`, store that one file renamed (`CLAUDE-KERNEL.md.snapshot`) — a same-named copy inside the snapshot would match kernel §3's ROOT fingerprint and create a second "live" ROOT candidate. (Rename it back on any copy-back recovery.)

**4. Evaluate preconditions against actual state — never the stamp.** For each `MIGRATIONS.md` block from the local version forward, run its **Precondition** against the real install (file present? column present?). Build the applicable list. Already-satisfied preconditions = **no-op**, reported as such. *(The stamp only tells you where to start looking; state decides what actually runs — mirrors kernel §3 fingerprint-over-ID and §12 verify-over-log. If no stamp exists, treat the install as pre-0.6 and evaluate every block from the earliest forward.)*

**4a. Eligibility — released versions only.** Cross-check every block on the applicable list against the newest **released** version, parsed from the **repo's canonical `CHANGELOG.md`** using the channel's parse contract above (skip `## Unreleased`; first `## vX[.Y[.Z]] — <FINAL|patch>` header; field-by-field numeric compare) — **never** the local install's copy (stale mid-update by definition) and never a CHANGELOG this run is itself delivering. One source of version truth.
   - Block version **≤ newest released** → eligible; proceed to consent.
   - Block version **newer than newest released** (still under `## Unreleased`, or absent from the CHANGELOG entirely) → **held, never offered for consent.** Report it plainly: *"vX.Y exists in the migration ledger but isn't a released version yet — held, not applied."* A held block re-becomes eligible automatically the moment its version is cut (its header flips to FINAL) — nothing to undo, just re-run.
   - No released version parses at all → **apply nothing** (note-and-stop). Never fall back to "apply whatever the ledger carries."
   *(Why: `MIGRATIONS.md` is append-only and runs ahead of releases during development — "present in the ledger" can never mean "safe to apply." Unreleased = uninstallable must be a property of the structure, not of model diligence: live case 06 Jul 2026 — a bootstrap run offered an unreleased block and the user approved it; only the model's own unprompted CHANGELOG check held it back.)*

**5. Consent — one approval per version-block.** Present each **eligible** (step 4a) version's changes as one plain-language summary: *"v0.X applies N changes: [one line + why each]. Go?"* **Name the manual steps in this same summary, up front:** any `.skill` files the block changes (the user drags them in Settings at step 8) and any leftover files a Streaming Drive can't delete (the user trashes them at the end) — the user hears "two things will need your hands" before consenting, never as an end-of-run surprise. No consent → nothing writes for that block; the rest may still proceed. §11 value-confirms happen **inline** during execution (e.g. show each derived value before writing it), not batched here.

**6. Execute — additive, atomic, fail-closed.** Apply each consented block's **Action**: additive writes only. **Write → cloud-confirm (`modifiedTime`) → next**, one migration at a time. Any failure: **stop, report plainly, park the remainder.** Idempotent preconditions + the step-3 snapshot make retry free and safe — a half-applied run re-runs cleanly because satisfied preconditions no-op.

**7. Conflicts — user-modified kernel files.** Before overwriting any kernel file, diff the local copy against the incoming version. On a real divergence (the user edited it): surface the diff in plain words, **recommend take-new**, and **offer to relocate their customization** to the profile layer or a project-README override — where per-person adaptation architecturally belongs, since the kernel stays person-agnostic. No decision → that file stays as-is, its migration parks, the rest of the run proceeds.

**8. Skill refresh.** List every changed `.skill` file with its direct download link. Walk the user through uploading each in **Settings → Capabilities → Skills**, one at a time, confirming each shows active. (This is the one irreducibly manual step — `.skill` binaries can't be written by the file tools.)

**9. Verify — per block, then orient-clean.** Run each applied block's **Verify** line. Then `/orient` must come back clean — the persistence-proof beat, update edition. **Escalation:** any block whose changes touched **skills, `START-HERE.md`, or `STRESS-TEST.md`** must DEMAND a full `/stress-test` in its Verify line — instance-level proof for an instance-level risk surface. (The escalation is written per-block by the migration author; this runner executes it mechanically.)

**10. Close.** Bump the shared stamp in `profile/STACK.md` to `SB kit: vX.Y · last update-check: DD Mon YYYY` and add a one-line record in STACK. Done.

---

## The whole user surface

One "yes, update" (or "later") · one consent per version-block · the occasional §11 value-confirm or conflict decision · click-and-drag for any changed skill file. Everything else — fetch, snapshot, diff, write, verify — is the user's Claude. **The update never blocks session work; "later" is always a first-class answer**, and a safety-tier item simply re-surfaces at the next orient until applied.

---

## Fail-closed summary (the safety contract)
- No snapshot → no writes.
- Block version not released (step 4a) → held, never offered, never applied. No released version parseable → nothing applied.
- Fetch fails and no zip fallback → note it, proceed with the session, nothing written.
- Consent refused → nothing written for that block.
- Any write unconfirmed → stop, park the remainder, report.
- Precondition ambiguous / freshness uncheckable → skip that file, never guess.
- A failed or partial run always leaves the install **no worse than before it started** — that is the acceptance bar every migration inherits.
