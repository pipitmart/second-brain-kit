# ORIENT.md — Session Orientation
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic. Runs at the start of every session, in every project.*
*Triggered by: `/orient`*

> Purpose: get Claude fully up to speed before any work — confirm it can read and write, load the system and the project, and surface what's open. Person-specific context comes from `/profile`; this protocol never names a person. Written to be run by any model.

---

## README-first rule

**Before running any step below, read the project README.** If the README defines a custom `/orient` protocol (look for a heading like `## /orient` or `## /orient — [project] protocol`), execute that protocol instead of Steps 1–6. The README is the project's authority on how orientation should work for that context — a coordination layer, a learning project, or any non-standard shape may need a different flow than a standard execution project.

**The gates are non-overridable.** A custom protocol replaces *what gets loaded and surfaced* (Steps 1–6), never the safety and routing sub-steps: **Step 0 (access), Step 0.5 (demo), Step 0.6 (freshness), and Step 2.5 (Noticeboard drain) run in every orientation, custom or standard.** **Kernel §13 (verify before you assert — cite-or-mark) binds every door too**, custom ones included: a custom protocol that surfaces an action, an absence, or a status without a `(checked: …)` or `unverified` tag is malformed exactly as a standard one would be. *(Added 14 Jul 2026, 0.7-15 — a custom door's §12 verification is the surface on which two of the five failures fired.)* **So does the session beacon** (Step 6's post, `/offload`'s clear): it is two advisory writes and it is the *only* thing that tells a session it is not alone — and a custom door is not a lesser writer of the shared ROOT files, it is typically a heavier one. *(Named explicitly because Step 6 otherwise sits inside the replaceable range: a custom door that skipped the beacon would run silent on the board for exactly the sessions the beacon exists to warn about — caught at this build's cold `/review`.)* **And they bind ANY session-opening protocol, not just `/orient`:** if a project's README defines its own session doors (a daily morning/evening command, a custom start ritual), each of those protocols must run these gates too — a custom door that skips them silently re-opens every failure the gates exist to kill (observed live 04–05 Jul 2026: a project's custom daily doors bypassed the freshness gate and the drain entirely). A custom protocol runs the drain against its own SSOT. *(Why named explicitly: the drain sits at Step 2.5, inside the replaceable range — without this rule, any project with a custom orient silently never drains its notes, and the cross-project channel fails for exactly the coordination-layer projects most likely to depend on it.)*

If no custom protocol is found, proceed with Steps 0–6 below as normal.

*Rationale: `/orient` is a single command regardless of project type. The README makes it context-aware without adding new commands or user friction. Any project can override orient by documenting its protocol in its README.*

---

## Load-first rule — the read-only load is non-skippable

A session may open with a direct question instead of a clean `/orient` — answer it, but **the read-only load (Steps 0–6) still runs in full first.** It is read-only: no writes, no approvals, no cost — and it is what puts the Profile (timezone, date format, names, key people, guardrails) and the project state into context *before* any value is used or written. **Never skip the load to jump to the answer** — a value back-filled from assumption because the Profile wasn't loaded is how false data enters the record (kernel §11).

Only **Step 6** (the session-row *write*) is ever deferrable — e.g. to avoid an extra write-grant on a quick-question session. If you defer it, **say so explicitly** in the response; never drop it silently.

---

## Step 0 — Access check *(do this first, every time)*

Before loading anything, confirm Claude can actually operate:

1. **Read access** — can the ROOT folder be reached (via mounted folder or Drive)? Try to read `kernel/CLAUDE-KERNEL.md`.
2. **Write access** — is the ROOT/project folder **mounted locally** for in-place editing?

| Result | Meaning | Action |
|--------|---------|--------|
| Read ✅ + Write ✅ | Full access | Proceed to Step 1 |
| Read ✅ + Write ❌ | Read-only — folder not mounted, **or mounted but attached read-only** (native Read works; Write/Edit fail with *"path outside the connected folder"*) | **First try `request_cowork_directory` on that folder to re-grant read-write** — this fixes the read-only-attach case, and a UI/project re-mount does *not* (it can re-attach read-only). If still blocked, the folder isn't mounted — offer to mount. **Do not `create_file`** until write works. |
| Read ❌ | Can't reach ROOT | Stop. Drive not synced or no access — direct user to `/stress-test`. |

*Note (27 Jun 2026): a Cowork folder can mount with Read working but Write/Edit blocked — it attached read-only, even though the folder is genuinely writable. `request_cowork_directory` on it re-grants write. See `CLAUDE-KERNEL.md §4`.*

*Why Step 0 exists: a read-only session must never fall back to `create_file` — that re-creates the duplicate-file problem the write-path rule was built to kill.*

---

## Step 0.5 — Demo-mode check

After access, before loading: **if `profile/` is empty (or missing) and `ROOT/profile-demo/` exists → DEMO MODE.**

- Show the banner on every response that touches system state: `🟡 DEMO MODE — sample data, nothing here is yours yet`.
- Load the demo profile and the demo project (inside `profile-demo/`) instead of Steps 1–3's real paths; run the same checks against them.
- Remind the user how to go real: *"Run `/genesis` and pick Live when you're ready — the demo folder gets deleted then."*
- If `profile/` has real content **and** `profile-demo/` still exists: the real profile wins, no demo mode — flag the leftover demo folder and offer to delete it.
- Demo rules live in `kernel/PROFILE-GENESIS.md → The DEMO branch`.

---

## Step 0.6 — Freshness gate *(built 01 Jul 2026 — P1 fix)*

Before trusting any local read or making any write this session, prove the local copy is current against the cloud. This runs after the access check and demo-mode check, before anything is loaded or acted on.

1. Read the active project's SSOT header `**Last updated:**` line via the native file tools (local).
2. Read the same file's cloud state via the Drive API — `get_file_metadata` (`modifiedTime` is the **primary, machine-comparable signal**; the header-stamp text is a human-readable cross-check only, never the primary comparison — two instances must not disagree on which one governs).
3. Compare. If the cloud is newer (later `modifiedTime`) → **STALE.**

**On STALE: HALT.** State plainly: *"this machine is behind the cloud — local as of [X], cloud updated [Y]."* Do not trust local reads and do not write. Offer: (a) wait for Drive to finish syncing, then re-run `/orient`; or (b) continue **read-only** for this session off the **cloud** copy (Drive API reads only) — all writes blocked until local catches up.

**On Drive-API failure (can't reach `get_file_metadata` — rate-limited, offline, permission error):** treat this the same as STALE, not as fresh. "Can't check" is never grounds to proceed as if verified — say so explicitly and offer the same two options.

**On fresh (local matches cloud):** proceed to Step 1 as normal.

**Execution shape — batch the calls, never the checks** *(added 14 Jul 2026, 0.7-14).* These checks are independent and read-only, so **issue them as ONE parallel tool-call batch** (the SSOT's, `profile/PROJECTS.md`'s and `profile/Noticeboard.md`'s metadata calls together, alongside the board read) rather than one round-trip at a time. The wall-clock floor becomes the slowest single call instead of their sum. **This is an execution change only — every check that ran before still runs, with identical semantics.** No sub-agents here: a spawn costs more than the calls it would parallelize (measured — `Research - 0.7-14 Token Architecture - SB Product.md`), and a check whose result must land in the parent's context is not a thing to delegate. *Correctness is never traded for latency:* **"1+1=3 in 0.1 sec is fast, but it's wrong."**

**Third stale surface — the bash-sandbox mount *(added 03 Jul 2026)*.** Local (file-tool) and cloud are not the only two views: the bash sandbox's mount of a Drive folder can serve a stale, NUL-padded, or **silently truncated** copy of a file even when the file tools and cloud agree — and a `request_cowork_directory` re-grant does NOT refresh it (observed 02–03 Jul 2026; detail `profile/LAB.md`). Rule: **when the bash view and the file-tool view of a file disagree, the file tool wins.** Never write a Drive file from bash until the mount is proven fresh (mount size == cloud `fileSize`); this gate's local-vs-cloud comparison says nothing about the bash mount — check it separately whenever bash is about to read or package a Drive file.

**Scope — every file this session is about to write, not just two.** Check at minimum the active project's SSOT and `profile/PROJECTS.md` before Step 1 loads them — but before writing to *any* other file this session (Skunkworks, Session Ledger, Users, Issues, Scorecard, CHANGELOG, `profile/Noticeboard.md` — written **twice** at orient: by the Step 2.5 drain **and** by Step 6's beacon post — or any ROOT/kernel file), run the same check on that specific file first. *Why named explicitly: the 01 Jul 2026 incident corrupted eight files, not two — a gate that only covers the SSOT + PROJECTS.md would have let the other six happen again unchecked.* Re-run the same check at `/offload`, immediately before each write — sync state can change mid-session (see `OFFLOAD.md`).

*Why: 01 Jul 2026 — a session ran on a Streaming Mac whose local Drive cache hadn't pulled the prior night's changes. The file tools read that stale local cache, concluded files/sessions didn't exist, then wrote to the stale base — producing a duplicate folder chain via `create_file` on a path it couldn't find, and losing two sessions' worth of Decisions Log narrative even though the underlying work had already landed. Full incident + evidence: `profile/LAB.md`. Principle: verify the freshness of the medium, not just the value — extends kernel §11 (data integrity) and §12 (action integrity).*

---

## Step 0.7 — Kit update check *(cadence-guarded — built 04 Jul 2026, v0.6)*

A quiet check that this install isn't running an outdated kit. **Guarded so it costs nothing on a fresh install and hits the network at most once a week.**

1. Read the shared stamp in `profile/STACK.md`: `SB kit: vX.Y · last update-check: DD Mon YYYY`. **No stamp** → this is a pre-0.6 install; surface one line ("this install predates the update mechanism — say 'update SB' when you want to bring it current") and proceed. **Stamp present and `last update-check` ≤ 7 days ago** → skip silently (zero fetches, zero output). Only if >7 days old, continue.
2. One raw fetch of the repo `CHANGELOG.md` (`https://raw.githubusercontent.com/pipitmart/second-brain-kit/main/CHANGELOG.md`); parse the top released version (rule in `kernel/UPDATE.md`). **Update the stamp's `last update-check` to today regardless of outcome** (this is what makes the 7-day guard shared across all doors).
3. **Fetch failure = note-and-proceed, never a halt** — one line ("couldn't reach the repo to check for updates; carrying on"), continue orientation.
4. If the repo version is newer: **tier-aware surfacing** — a *safety*-tier update surfaces as one line **every** orient until applied; a *feature*-tier update is mentioned **once**, then stays quiet unless the user asks. Offer the run (`kernel/UPDATE.md`); "later" is always fine and never blocks the session.
5. An explicit "update SB" / "check for updates" at any time ignores the 7-day guard and fetches immediately.

*This step is read-mostly: its only write is the shared stamp's date, subject to the same freshness discipline as any other write this session.*

*(Step 0.7 is NOT one of the non-overridable gates — a project running a custom README orient protocol may omit it; the `/stress-test`, `/help-me`, and explicit "update SB" doors still cover the cadence.)*

---

## Step 1 — Load the system + daily profile *(silent)*

Read, in order:
- `kernel/CLAUDE-KERNEL.md` — engine rules
- `profile/PROFILE.md` — who the user is, working style, guardrails
- `profile/PROJECTS.md` — the project index: what projects exist and where
- `profile/LEARNING-ME.md` — how the user learns
- `profile/STACK.md` — the user's tools and machines

`profile/ABOUT-ME.md` (deep profile) loads on `/stress-test`, not daily. `profile/LAB.md` (cross-project infra investigations) loads on demand — STACK's settled summary points to it when an infra issue is live. `kernel/GLOSSARY.md` is referenced on demand.

---

## Step 2 — Load project context

Locate the active project in `profile/PROJECTS.md` — flag if it has no row (unregistered) or if its row points at a folder/SSOT that can't be found anywhere in Drive (ghost row). *A project that exists in Drive but isn't mounted on this machine is an **access gap**, not a ghost — prompt to mount, don't flag the row.* Then name-search for `README-[PROJECT].md` in the active project's folder.

**Scope rule:** orient surfaces only the active project for this session. Other projects in the index are read to detect ghost rows and unregistered folders — but never mentioned in the orient output. Cross-project work belongs in a separate session with that project's Cowork context.
- Read it fully.
- Confirm the project folder is identified and recorded in the README.
- Be ready to surface the project instructions (so they can be pasted into Cowork project settings on a fresh machine if needed).
- Flag if the README is missing or out of date.

---

## Step 2.5 — Noticeboard drain *(built 02 Jul 2026 — cross-project routing)*

Projects can't see each other — every session mounts only ROOT + its own project folder. `profile/Noticeboard.md` (in ROOT, reachable from every session) is the one shared channel: a session in one project pins a note tagged for another; the target project picks it up here, at its next orientation. This step is the pickup. *(The pin side is `/offload`'s job — see `OFFLOAD.md → Noticeboard pin`.)*

1. **Read `profile/Noticeboard.md`.** Missing or no open notes → skip silently, proceed to Step 3.
2. **Freshness-check it** (same discipline as Step 0.6 — cloud `modifiedTime` vs local) since this step will write to it. Stale or uncheckable → surface matched notes **read-only** this session; no drain writes, no deletes.
3. **Match.** Collect open notes whose tag equals the active project's **`Noticeboard tag`** in `profile/PROJECTS.md`. Tags are **stored** in that column, never derived or guessed from the project name — a rename must never silently break routing. One tag per line, by format; multi-target notes were duplicated per project at pin time.
4. **Drain — interleaved with Step 3** (it needs the loaded SSOT). After Step 3 reads the Decisions Log:
   - **Dedupe first (idempotency).** Skip any matched note already present as a `[from Noticeboard]` Open Action or already reflected in the SSOT. This is what makes a failed delete harmless: a note that couldn't be removed last session re-drains as a no-op, never a duplicate.
   - **Append** each remaining note to the SSOT's **Open Actions** table, marked `[from Noticeboard]`, in the same orient turn — merged into the unified open-actions list Step 3 presents, so it's visible and correctable in the same window, not a silent unattended mutation.
   - **Confirm the SSOT write landed** (re-read cloud `modifiedTime` — the `OFFLOAD.md` write-confirm discipline), then **re-read the board and hard-delete** the drained line from `Noticeboard.md` (never delete against the copy read at step 1 — kernel §4 shared-write rule). Write → confirm → delete, atomically **per note**; nothing is ever held in an intermediate "drained" section. *(Why hard-delete: a held-then-pruned state is the exact two-phase-move failure shape that caused the 01 Jul 2026 half-landed-compaction duplicate. The audit trail lives in the target SSOT, not the board.)*
   - **Both failure paths fail closed.** SSOT write fails or unconfirmed → the note stays untouched, retried next orient. Delete fails (e.g. the project-folder write grant lapsed — irrelevant here, but the ROOT grant can hiccup too) → say so explicitly; the dedupe above absorbs the re-read next session.
5. **Ghost tags** (a tag matching no `PROJECTS.md` row): flag in one line, never touch the note. **Notes tagged for *other* projects: never surface their content** — the privacy wall applies inside the board too; they are not this session's business.
6. **Protected sections (binding): `## Session beacons` is NOT a note section — the drain never reads, matches, or deletes a line in it** *(added 14 Jul 2026, NB-23)*. It is machinery, not a channel; a beacon carries a project tag and would otherwise look exactly like a drainable note. Beacons are handled by Step 6 (post) and `/offload` (clear), and by nothing else. Likewise: any note under a `## Standing notes` heading on the board is read-every-session context, not a routable note — read it, apply it where relevant, and leave it untouched. Standing notes are never matched, drained, or deleted by any project's drain, regardless of tag; only the project that pinned one retires it, by deliberate edit, not via this step. *(Codified 05 Jul 2026 — previously only the board's own inline header protected them.)*
7. **A missing note is not a lost note — run the drain-fingerprint check before concluding anything** *(added 13 Jul 2026)*. Notes vanish from the board healthily all the time: another project's orient drained them (concurrent sessions are normal — kernel §4). If a note you expected is gone, read the two worlds apart: **the target project's SSOT written just after the note's last sighting → healthy drain** (the audit trail lives in that SSOT, by design); **the target SSOT untouched → possible real loss** — surface it to the user, never repair it silently. **Never "reconstruct" a missing note without this check:** a re-pinned duplicate double-drains into the target SSOT, a worse corruption than the loss it imagined (live near-miss, 12 Jul 2026 — a "lost" pin had in fact drained correctly minutes earlier). All writes to the board itself follow the shared-write rule in kernel §4: re-read immediately before writing, own lines only, confirm after.

*Why this position: after Step 2 (matching needs the active project identified), interleaved with Step 3 (writing needs the loaded SSOT for dedupe + append). This step is one of the non-overridable gates — see the README-first rule above.*

---

## Step 3 — Load the Decisions Log (SSOT) — **the hot zone only** *(read boundary added 14 Jul 2026 — SB Product 0.7-14)*

Name-search for `Decisions Log - [PROJECT].md` in the project folder.
- **Integrity check:** confirm there is **exactly one**. Flag any duplicate or `conflicted copy` immediately and ask which is canonical — do not proceed on an ambiguous SSOT.
- **Read the HOT ZONE — down to the marker line, and stop:**

  ```
  <!-- ═══ WARM BELOW — /orient reads above this line ═══ -->
  ```

  The hot zone carries everything orientation needs: the `Last updated:` header, the current status paragraph, the **open** actions/issues index, the **Evidence manifest** (what exists and where — 0.7-15), the next-session agenda, and the Session Log totals. Everything below the marker — dated Decisions entries, Session Log rows, Backlog, Parked — is **warm**: read **on demand**, when this session actually needs that history, by searching or offsetting into that section. **Never re-read the whole file by reflex.** Full spec: `kernel/SSOT-TEMPLATE.md → The hot/warm split`.
  - **Count the marker before you trust it** — one cheap grep over the whole file for the literal line. **Exactly one** → read to it and stop. **Zero or more than one** → fail open (below). *(Stated explicitly because "read to the marker and stop" cannot, by itself, notice a second marker further down.)*
  - **Fail open, always — but say which failure it was.** Never halt; never silently repair it (the offload that owns the file does that, with the user seeing what moved — kernel §10). Two cases, two different one-line flags, because they are not the same fact:
    - **No marker** → *"SSOT not yet split — full read; it restructures at its next `/offload`."* (Normal for a pre-0.7 log; nothing is wrong.)
    - **Marker duplicated, or the hot zone malformed** → *"SSOT marker is ambiguous/malformed — full read; flagged for the next `/offload`, not auto-repaired."* **Do not tell the user it "restructures at its next offload"** — an already-split file with a broken boundary is a defect to surface, and `OFFLOAD.md` explicitly refuses to regenerate it silently.
    In both cases: **read the whole file exactly as before** and carry on.
  - **A dangling pointer is a flag, not a blocker.** An index row pointing at a Decision entry or brief that can't be found → surface that row, carry on.
  - **The gates are untouched.** Steps 0, 0.5, 0.6 and 2.5 run in full, unchanged. This step changes *how much is read*, never *what is verified* — a check traded away for speed is a check that will be re-bought with an incident.
- Confirm it's readable and not empty.
- Extract all open actions.
- **Verify user-owned actions against reality before surfacing them** (kernel §12). Any open action whose owner is the **user** — a manual step only they can do (upload a skill, drop a file into a folder, connect an account, flip a setting) — may already be done outside Claude's visibility. Before listing it as still-open, check actual state (folder contents via name-search/Read, what the user shows on screen, the relevant Settings). If verifiable, check it; if not verifiable, surface it as unverified and ask. When confirmed done, close it in the SSOT in this session. *(The system verifies; it does not rely on the user remembering to report.)* **Every surfaced action is a *status* claim, so §13's cite-or-mark binds it: each one carries `(checked: …)` or the literal `unverified` — no exceptions, and no naked "still open."** *(This is the surface where the 14 Jul 2026 "delete the file you already deleted" failure fired: one `search_files` would have killed it, and the claim went out untagged instead.)*
- Flag if missing, unreadable, empty, or stale (no recent updates while work is active).
- **Archive existence check — on demand, not every orient** *(narrowed 14 Jul 2026, 0.7-14).* Compaction markers live **warm**, so a hot-zone read no longer sees them and this check can no longer fire at load. Confirm `Archive - [PROJECT].md` exists (name-search) **the first time this session actually follows a pointer into it** — which is the only moment its absence matters. **Never read the archive to check it exists**, and never read it at all unless history is genuinely needed: it is the cold layer.
- **Gap check:** compare today against the project's Last session (in `profile/PROJECTS.md`). If the gap exceeds the profile's threshold *while Open Actions sit untouched*, ask one question — "It's been [X] days — what happened?" — and triage per `kernel/HELP-ME.md` Branch B. Benign answer (trip, rest, life): log one line, move on. Loaded answer: shift to the red-alert flow before starting work. **Benign-but-lost** *(added 03 Jul 2026)* — the answer is fine but reads lost ("honestly I forgot how this works"), or the log shows sessions happening with nothing landing: offer the ~10-minute recovery refresher (`kernel/HELP-ME.md` Branch C) — offer, never force; a decline is logged and not re-offered for ~2 weeks unless the user asks.

---

## Step 4 — Verify consistency

Cross-check the README instructions against the Decisions Log:
- No contradictions between stated conventions and logged decisions.
- No retired tools or files still referenced.
- Project folder identified.
- Flag anything inconsistent — ask the user to clarify before proceeding.

---

## Step 5 — Confirm key people

List the key people for this project (from the README). Flag if the section is missing or incomplete.

---

## Step 6 — Open the session (write)

Once access is green and the SSOT is loaded, open a new **Session Log** row in the Decisions Log: append a line with today's date and the **start time** in the user's local timezone, end left blank (`/offload` closes it). Then **surface the running total** — *"N sessions · ~H hours on this project so far"* — in the orientation output. It's a small, deliberate hit of visible progress at the top of every session. *(Read-only session: skip the write, still show the last-known total.)*

**Writing the row against a partial read** *(0.7-14).* The Session Log's header, `**Totals:**` line **and the table's column-header + separator rows** sit **inside** the hot zone; the data rows sit warm, just below the marker. So the append target is always in the read window: **anchor the edit on the separator row + the marker** — both already read this step — and insert the new row **directly beneath the marker** (rows are **newest-first**; `SSOT-TEMPLATE.md → Session Log` carries the rule and why it changed). No full read is needed to open a session. *(If the file isn't split yet, the fallback full read applies and this is moot.)*

### Post the session beacon *(built 14 Jul 2026 — NB-23)*

ROOT is mounted in **every** session, so a session in another project is a live writer to the shared ROOT files (`Noticeboard.md`, `PROJECTS.md`, `Scoreboard.md`) — and until now **no session knew another was running.** Prose cannot fix that; only a signal can.

In the same turn that opens the Session Log row, **append one line** to `profile/Noticeboard.md → ## Session beacons`:

```
- [DD Mon YYYY HH:MM tz] #project-tag — session N open — [machine, if known]
```

- **Append only, own line only, confirm it landed** (kernel §4). Never rewrite the section.
- **First, read what's already there.** Another project's live beacon means: re-read before every shared-file write, prefer append over rewrite, and never delete a line you don't own. That is the beacon's entire job — it raises discipline, it does not grant permission.
- **Ignore any beacon older than 12 hours** — presume it dead (a session that crashed without `/offload` never cleared it). **Do not delete another project's stale beacon**; only sweep your *own* project's, if a previous session of yours left one behind.
- **It never blocks.** A beacon is advisory. If the board is unreachable or the write fails, note it in one line and carry on — orientation does not halt on a beacon.

`/offload` clears this line at close.

**This is the only place a session number is ever minted** *(built 06 Jul 2026 — fixes a live mislabeling where continued work after an `/offload` close, with no fresh `/orient` in between, got numbered as a new session)*. Even a deferred write (see the Load-first rule above) still mints its number here — just later, at offload, rather than up front. The one case with **no** number to give is when this step never ran at all in the current sitting — see `OFFLOAD.md → Session Log` for what to do instead (reopen the last row, don't invent one).

---

## Output

Report clearly:

| Check | Status | Notes |
|-------|--------|-------|
| Access (read + write) | ✅ / ⚠️ / ❌ | Read-only? Name it. |
| Freshness (local vs cloud) | ✅ / ❌ | STALE halts everything below — see Step 0.6 |
| System + profile loaded | ✅ / ❌ | |
| Project in index (PROJECTS.md) | ✅ / ❌ | Flag ghost / unregistered rows |
| Project README found | ✅ / ❌ | |
| Decisions Log — exactly one | ✅ / ❌ | Flag duplicates / conflicted copies |
| Noticeboard drain | ✅ / ⚠️ / — | N notes drained into Open Actions / — = none tagged for this project; ⚠️ = write unconfirmed or board stale, notes left pinned |
| Open actions identified | ✅ / ❌ | List them (incl. `[from Noticeboard]` rows) |
| Gap check | ✅ / ⚠️ | Days since last session; ⚠️ triggers the one question |
| Instructions consistent | ✅ / ❌ | Flag contradictions |
| Key people known | ✅ / ❌ | |
| Session opened + total shown | ✅ / ⚠️ | Start-time row added; "N sessions · ~H hrs" surfaced (⚠️ if read-only) |

**All green** → state what's loaded and what's open. Work begins.
**Any red** → fix before proceeding. Do not start work on a partial orientation.

---

## On a fresh machine

If the project isn't set up in Cowork yet:
1. Run Steps 0–5.
2. Surface the full project instructions from `README-[PROJECT].md`.
3. Prompt: "Paste these into your Cowork project settings, then re-run `/orient`."

The Drive files themselves need no copying — they sync via Drive. Only the Cowork project settings are per-machine.
