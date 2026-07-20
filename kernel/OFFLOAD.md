# OFFLOAD.md — Save to the SSOT
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic. The save function — persists session work into the project's Decisions Log.*
*Triggered by: `/offload`, plus automatic prompting (below).*

> The human save function. It writes decisions and open actions into the project's **Decisions Log (the SSOT)** so nothing lives only in chat memory. Target is the Drive markdown directly — Obsidian is not involved.

---

## Autosave model (event-driven, not a timer)

There is no background timer in a chat — Claude acts on turns. So "save often" is **event-driven**, which is stronger than a clock:

1. **Save-on-decision.** The moment a decision is made or confirmed, append it to the Decisions Log. You never lose more than the last exchange.
2. **Checkpoint prompts.** At natural breakpoints, before context gets long, and at session end, offer to offload. Don't nag every turn — prompt when there's unsaved material of substance.

### Blocker claims are decisions *(built 14 Jul 2026 — SB Product 0.7-15, kernel §13)*

A **blocker** claim — *can't-because · doesn't-exist · unproven · not-found · nothing-there* — is not an observation. **It is a decision**, because it redirects work: it gates a design, closes a path, or tells the user to stop. Save-on-decision therefore binds it.

**The rule:** a blocker claim entering **any SSOT or design doc** renders in **exactly one of two legal forms** — the same two §13 gives every falsifiable claim:

> ✅ **Attempt inline** — the tool call actually run, and what it returned. Not the reasoning; the *attempt*.
> `Leg 4 is unproven — no transcripts found (checked: search_files "Meet Recordings", 14 Jul, 0 results).`
>
> ✅ **The literal marker `unverified`** — an honest admission that no falsification was attempted.
> `Leg 4 is unproven — unverified; no search run.`
>
> ❌ **The naked third form — malformed.** A blocker asserted as fact with neither an attempt nor the marker.
> `Leg 4 is unproven, so the design gates on it.`

**Only the naked third form is malformed**, and the offload auditor (below) treats it as an **automatic flag** — no judgment call required, so it cannot be argued away.

**What the auditor does with each form** *(stated because this is the auditor's one no-judgment trigger, and an ambiguous trigger is no trigger):*

- **Attempt inline** → the auditor **re-runs it**. Does the named tool/file actually return what the entry says it returned?
- **`unverified`** → **legal, but the auditor runs the falsification the entry admits it skipped.** The marker buys honesty, not exemption: a blocker is a *decision*, and a decision resting on an un-run search is exactly what the auditor is for. If the search comes back non-empty, that is a finding.
- **Naked** → automatic flag, no reading required.

*Why `unverified` is legal here at all:* the alternative is a rule the model routes around by simply not writing the blocker down — which converts a hard, auditable surface back into the unenforceable speech class. **An honest naked-but-marked blocker is strictly better than a blocker that hides in the chat.**

*Why this surface and not "before speaking any claim of shape X":* a speech rule is unenforceable — there is no mechanical surface between token generation and the user's eyes inside the fence (§13). **The write is small, well-defined, and already instrumented.** This catches the mid-session assertions that *redirect work*, which is the class that does the damage.

*Accepted failure mode, named:* a blocker that steers a session **verbally** and is never written **escapes this rule entirely** — but such a blocker also **dies with the session**. Bounded harm, accepted deliberately. It is caught, if at all, by §13's chat-side tag, which is soft by construction.

---

## Access gate

Offload writes, so it needs write access (folder mounted + Available offline).
- **Write ✅** → save in place.
- **Read-only** → do **not** `create_file`. Stage the entries, tell the user, and prompt to mount. Save once access is restored.

## Freshness gate *(built 01 Jul 2026, hardened same day — pairs with `ORIENT.md` Step 0.6)*

Sync state can change mid-session, so the freshness check `/orient` ran at Step 0.6 is **not** assumed to still hold at offload — re-run it **for every file about to be written**, immediately before that write (not just the SSOT):
1. Re-compare the target file's cloud `modifiedTime` (primary, machine-comparable signal) against what was last read. The header-stamp text is a human cross-check only, never the primary comparison.
2. If the cloud moved ahead during the session (another machine wrote in the meantime) → **STALE.** Stop, tell the user, do not write on top of it — reconcile first (surface the cloud's newer content; don't silently overwrite).
3. **If the Drive-API check itself fails** (can't reach `get_file_metadata`) → treat as STALE, not fresh. Never write on the assumption that "couldn't check" means "must be fine."
4. If fresh → proceed to write as normal.
5. **Bash-sandbox writes *(added 03 Jul 2026)*:** the sandbox mount is a **third surface** that can be stale (NUL-padded or silently truncated) even when local and cloud agree, and a folder re-grant does not refresh it (02–03 Jul 2026 — `profile/LAB.md`). When the bash and file-tool views of a target file disagree, **the file tool wins** — bash writes to that file are unsafe until the mount is proven fresh (mount size == cloud `fileSize`). Prefer the native file tools for every SSOT write.

**After writing, confirm it landed:** re-read the file's cloud `modifiedTime`/header stamp via the Drive API and verify it reflects the change just made. **If the confirm fails** (cloud still shows the old state) — retry the write once; if it still doesn't land, **stop and tell the user explicitly** ("the write to [file] may not have persisted — check it manually before trusting it next session"). Never report a session as offloaded cleanly while a write is unconfirmed.

*Why: the 01 Jul 2026 staleness incident lost two sessions' worth of Decisions Log narrative between a write on one machine and a read on another, and it was never confirmed whether the write itself failed to persist or was simply never made. The first version of this fix (same day) was itself found to only cover two files when the incident had corrupted eight — hardened same session after a `/review` pass. Full incident: `profile/LAB.md`.*

## Demo mode

If the session is in demo mode (`profile/` empty + `profile-demo/` exists — see `ORIENT.md` Step 0.5): show the `🟡 DEMO MODE` banner, and **confine every write to `ROOT/profile-demo/`** — the demo Decisions Log and demo profile files only. Never touch `profile/`, the real project index, or any real project, in demo mode. Skip the mirror step and deepening (there is no real person to observe yet). Rules: `kernel/PROFILE-GENESIS.md → The DEMO branch`.

---

## What it writes

To `Decisions Log - [PROJECT].md` (via `kernel/SSOT-TEMPLATE.md` format), **edited in place**:

- **Decisions** — newest first, each with a `*Why:*` rationale line.
- **Open Actions** — add new next steps (small and actionable); update status; close completed ones.
- **Parked** — anything deferred, with what it's waiting on.
- **Session Log** — three branches, by what happened in *this* sitting (full rule + rationale: `kernel/SSOT-TEMPLATE.md → Session Log`): **(1)** `/orient` ran and opened a row this sitting → close it normally (end time, `~Hrs`, summary, running total). **(2)** `/orient` ran but Step 6 was deferred (a quick-question session) → mint Step 6 now, open-and-close in the same motion — still a new number. **(3)** `/orient` didn't run at all this sitting (continuing past a prior close, no fresh load) → never mint a new row; reopen the most recently closed one instead (extend end time, recompute `~Hrs`, append the new work as a marked continuation). Mirror whichever branch fired into the project's separate Session Ledger too, if it has one — the two must never drift apart. Numbering itself is `/orient` Step 6's job alone; `/offload` never invents a number, only decides which of the three branches applies.
- **Header stamp (required, every offload) — REPLACE, never append (file-diet rule, 0.7-2, 06 Jul 2026).** Update the Decisions Log's top `**Last updated:**` line to **one line only**: this session's date + session number + a single-sentence outcome. This line is the SSOT's **freshness stamp** — a reader (or a not-yet-synced Drive web view) compares it against the latest content to know if they're looking at a current copy. A stale stamp silently defeats that check, so it is never optional: if you wrote anything to the log this session, you bumped the stamp. **Never chain a `*(prior: ...)*` history onto this line** — that narrative already lives in `## Session Log` (this file, permanent) and the project's WARM `Session Ledger` (if it has one), so repeating it here is the exact triplication the file-diet decision named. If the stamp you're replacing already carries an accumulated chain, this offload is also the moment to prune it back to one line — don't extend a chain that's already too long. *Why: a header meant to answer "is this copy current?" ballooned into a second full narrative history at the top of the file, read in full on every single `/orient` regardless of whether the history was needed — pure token cost with no reader benefit the Session Log doesn't already provide.*

### Hot-zone maintenance — the compilation step *(built 14 Jul 2026 — SB Product 0.7-14)*

The SSOT is split into a **hot zone** (read by every `/orient`) and a **warm trail** below a marker line — full spec: `kernel/SSOT-TEMPLATE.md → The hot/warm split`. **Keeping the hot zone lean is offload's job, not orient's.** The cost of a tidy index is paid **once, at write time, by the session that has the context to do it** — instead of being paid again by every future session that has to read the mess.

Every offload, on the rows it touches:

- **Cap the row it writes at ~3 lines** — ID · one-line statement · owner/status · **a pointer** to the dated Decision entry or Build Brief holding the reasoning. Write the reasoning **into the Decisions entry** (warm, where it belongs and where it is read on demand); never into the row. An open row is an *index entry*, not the argument.
- **Move Done rows to `Archive - [PROJECT].md`, verbatim, at the offload that closes them** — not at the next ~250-line compaction. Their outcome already lives in the dated Decision entry that resolved them.
- **Never touch the marker line.** It is a fixed literal interface; if it is missing or duplicated, say so in the closeout rather than silently regenerating it.
- **Only the row you touched.** Offload is not a licence to re-cut the whole index — an over-long row that this session didn't touch is a flag, not a chore. *(A whole-file rewrite is also exactly what the shared-write rule forbids — kernel §4.)*

**This is net less writing, not more** — offload's own ≤30s bar is served by it, not taxed: the row it would have written long, it writes short, and the reasoning goes to the entry it was writing anyway.

**Lazy migration.** A project whose SSOT predates the split keeps working unchanged (`/orient` fails open and full-reads it, with a one-line flag). It restructures at **its own next offload** — the session that has the context, under the rules above. No big-bang, and no session ever restructures a project it cannot read (the mount wall).

### The metric — float up, then roll up *(built 12 Jul 2026 — SB Product 0.7-1)*

The system measures itself with three numbers: **projects · sessions · hours**. Two files, one direction of travel — **the project's counters float UP into the index; the Scoreboard only ever ROLLS UP the index.** No number is ever held in two places.

**Step 1 — float up (`profile/PROJECTS.md`, this project's row).** The offloading session is the *only* session that can read this project's log (the mount wall, kernel §7a), so it is the only one that may write these two cells:
- **`Sessions`** = this project's latest session number (the row just closed in the Session Log).
- **`Hours`** = the running sum of this project's **recorded `~Hrs` stamps only** — the durations actually written in its Session Log/Ledger. **Never elapsed clock time for an untimed session, never an estimate** (kernel §11). If some of its sessions were never timed, the cell carries `≥` + ⚠️ — a **floor, not a total** — and stays honest about it.
- A project whose cells read `*pending*` has not offloaded since the counters existed: **fill them now, from its own log.** Never fill another project's cells — a number written for a log you cannot read is a guess.

**Step 2 — roll up (`profile/Scoreboard.md`, ROOT).** Recompute the TOTALS block **from `PROJECTS.md`**: `Projects` = **Active rows** · `Sessions` = Σ the `Sessions` column **over ALL rows — Active *and* Paused/Closed** · `Hours` = Σ the `Hours` column, same all-rows scope (**carry the `≥` if any row is a floor *or* reads `*pending*`** — a project that hasn't reported yet is missing hours just as surely as an untimed session is, so the total is still a floor; dropping the `≥` the moment every *reporting* row is complete would publish a floor as if it were a total). **Work done is work done: closing a project must never make the totals go down.** *(Why this is stated explicitly: `Projects` is a count of what's live right now, so it may legitimately fall; `Sessions` and `Hours` are cumulative records of work that happened, so they are monotonic — a roll-up scoped to Active rows only would silently shrink the published number the first time a project closed, which is exactly the kind of undefendable figure the metric exists to replace (§11).)* The Scoreboard holds **no independent count** — it is a derived view, so the two files cannot drift. Then append the session's own row to its history table (`date · project · session # · ~Hrs · one-line outcome`) — that table is the time series, append-only, never rewritten.

**Discipline for both writes:** freshness-check each file and **re-read it immediately before writing, not once at session start** — ROOT is mounted in *every* session, so a concurrent session in another project is a live writer to both of these files. Write only your own project's row/cells. **Confirm each write landed** (cloud `modifiedTime`), same as any other offload write.

**Hours are a floor, never a total.** Sessions is a complete field (every session mints a number at `/orient` Step 6); hours is not (untimed sessions exist and are never back-estimated). Anywhere these numbers are published — a website, a pitch, a claim — the hours figure is **"hours tracked,"** never "hours worked," and the counts are stated raw: no multiplier, no invented without-the-system baseline (kernel §11).

It also updates the project's row in `profile/PROJECTS.md` — Status, Last session date, and the **Notes cell**. **Notes cell is REPLACE, never append: cap at ~2–3 sentences describing the single latest session's outcome** (file-diet rule, 0.7-2) — not a chained `*(prior:)*` history, which would triplicate the same narrative a third time (Decisions Log header + Session Log + this cell). If a reader needs more than the latest state, that's what opening the project's own Decisions Log is for — this index is a map, not an archive.

---

## Noticeboard pin — cross-project actions *(built 02 Jul 2026, pairs with `ORIENT.md` Step 2.5)*

If the session produced an action or decision **another project must act on**, never let it travel by memory or a verbal handoff — pin it to `profile/Noticeboard.md` (ROOT — the always-writable anchor) as part of offload:

- **Format, one note per line:** `- [DD Mon YYYY] #project-tag — note — source — status`. Terse — the note is a pointer; the full substance stays logged in **this** project's SSOT.
- **The tag comes from the target project's `Noticeboard tag` column in `profile/PROJECTS.md`** — stored, never guessed from the project name. No row or no tag → ask, don't invent (kernel §11).
- **One tag per line.** A note for N projects = N lines, one per target. *(The drain hard-deletes whole lines — a shared line would vanish for the second project the moment the first drains it.)*
- **Freshness-check `Noticeboard.md` immediately before the write** — same rule as every other offload write (above).
- **Own lines only, against a just-re-read board** *(shared-write rule, kernel §4 — added 13 Jul 2026)*: ROOT is mounted in every session, so another session may be writing the board at the same moment. **Add new lines freely; edit only lines this session owns** (its own project's pins, or updates to its own earlier notes); **never rewrite the whole file** — a whole-file write from a copy read earlier in the session silently destroys whatever another session pinned or drained in between (lost update). Confirm the write landed (cloud `modifiedTime`).
- The target's `/orient` Step 2.5 does the rest: appends to their SSOT `[from Noticeboard]`, confirms, deletes the line. The board is a channel, not a record — it stays short by design.

*Why: cross-project closes that traveled by memory have already failed silently (30 Jun 2026 — a git-LICENSE close never reached the SB record and re-surfaced as phantom work). The pin is the single write channel that crosses the mount/privacy wall.*

## Clear the session beacon *(built 14 Jul 2026 — NB-23; pairs with `ORIENT.md` Step 6)*

`/orient` Step 6 posted this session's beacon to `profile/Noticeboard.md → ## Session beacons`. **Offload's last write is to remove it** — its own line, and only its own line (the shared-write rule, kernel §4: re-read the board immediately before the edit, delete the one line this session owns, confirm the write landed).

- **If the delete fails, say so in the closeout** and move on — a stale beacon is *advisory only* and blocks nothing. It is presumed dead after **12 hours** by any reader.
- **Never clear a beacon belonging to another project**, however old it looks. A beacon past 12h is **ignored, not deleted**. The two clearers, and there are only two: **the session that set it** (here, at close), and **the next `/orient` in that same project**, which sweeps its own project's stale line if a previous session died without clearing. No session ever touches another project's line — that is the shared-write rule (kernel §4), and a beacon is not an exception to it.
- A session that ends without `/offload` leaves its beacon behind by design. That is the accepted cost of a mechanism that must never block work.
- **No beacon to clear? Say nothing and move on.** Session Log branch 3 (work continuing past a prior close, no fresh `/orient`) reaches offload with its beacon already cleared — that is not an error. Branch 2 (Step 6 minted late, at offload) posts and clears in the same motion, or simply skips both; either is fine, and neither is worth a line in the closeout.

---

## Rules

- **Supersede, never delete.** If the session reversed an earlier decision, do not erase it — mark it `superseded [date]` and add the new decision. The log is an audit trail; the reasoning history matters.
- **Edit in place.** Never re-create the log (that spawns a duplicate and breaks the SSOT).
- **Only log what was actually decided.** No fabrication, no inferred decisions — **and no assumed or default-filled values** (times, timezones, IDs, dates, names). Every value written traces to loaded profile/project data, this session's observation, or the user. If a needed value isn't known, **ask before writing** — never stamp a guess. (Kernel §11.)
- **Exactly one log per project.** If a duplicate or conflicted copy appears, flag it before writing.

---

## Compaction (live → cold)

Compaction governs the **warm narrative** (dated Decisions entries). *Closed **rows** are not its business — they leave the hot zone the moment they close, under the hot-zone maintenance step above.* When the Decisions Log passes **~250 lines**, compact as part of offload — **automatically, not as a to-do:**

1. Sort entries **by status, never by age**: spent (superseded decisions, Done actions, closed notes) vs standing (conventions and rules still binding — these never move).
2. **Auto-relocate the clearly-spent entries verbatim** to `Archive - [PROJECT].md` (create it on first compaction), newest-first; leave the marker in the live log: `*Compacted [date]: N entries → Archive*`.
3. **Report one line** in the closeout — `Compacted N spent entries → Archive` — so there's a trace. No approval gate.
4. **Ask only when an entry is genuinely ambiguous** (standing-or-spent unclear — e.g. a build record whose rule may still bind): surface just those few for a quick yes/no. Never hold up the clearly-spent moves waiting on a decision.
5. Never rewrite or summarize — relocation only. Moves are **verbatim and reversible** (the archive is one read away), which is exactly what makes auto-execution safe.

The archive is the SSOT's **cold layer** — same rules (exactly one, in place, supersede-never-delete), just outside `/orient`'s read path.

---

## Observed patterns (the mirror step)

The profile must stay a true reflection, not a curated self-image. At session close, surface **0–3 observations** about how the user actually worked this session — patterns, signals, contradictions with the existing profile. Rules:

- **Reflect, don't direct.** Describe what happened ("stalled at the action step after the plan was agreed"); never prescribe ("should start earlier"). The mirror records; it does not coach.
- **Factual and dated.** Only what was actually observed in the session — no inference stacking, no psychoanalysis.
- **Unflattering and flattering carry equal weight.** A mirror that only shows good days is a portrait.
- **Claude adds entries directly** to `profile/ABOUT-ME.md → Observed Patterns` (newest first) and surfaces them in the session closeout to inform the user — no approval gate. The mirror is for reflection, not permission.
- **No quota.** An unremarkable session writes nothing.
- **Promotion:** when a pattern has appeared **3+ times**, propose folding it into the profile Baseline — user approves; supersede, never delete, if it replaces an existing trait.

This is the loop that keeps the profile honest as the person changes — the system learns who the user *is*, not who they set out to be.

### Deepening — earned questions

Profile depth is **not** collected at genesis; it's earned at moments of relevance. Data quality ranks: **observed > self-reported in context > self-reported cold.** Genesis gathers the cold minimum; the mirror observes; *this* step asks.

When the session contained a charged or first-of-kind event — a first red alert, a crossed red line, a real win or loss, visible frustration with the work or the system — the closeout may ask **one** deeper question while the answer is still alive: *"What did that look like from inside?" "Where was the line, in hindsight?" "What would have made you not come back?"*

Rules: **one question, never a battery; only when an event earned it; never cold.** Record the answer to the relevant profile file (guardrails, red-alert settings, ABOUT-ME baseline) tagged `*(self-reported in context, [date])*` — it outranks genesis data and is itself outranked by observation. An unremarkable session asks nothing.

*This is how a thin day-one profile becomes a deep one without a 100-question interview: the system catches truths when events surface them.*

---

## Cross-project learnings — route, don't write *(reworked 02 Jul 2026 — supersedes the propose-upward rule)*

If the session produced something reusable beyond this project (a convention, a fix, a pattern), **do not write it into `profile/`, `STACK`, or the kernel from here** — ROOT-layer edits are deliberate system maintenance (kernel §10), never a side effect of ordinary project work. Route it instead:

- **System/infra investigation** (sync, mounts, write path, connectors, platform quirks) → log it in `profile/LAB.md`, its designed home. `STACK` carries only the settled summary.
- **Everything else** → pin it to the Noticeboard (section above), tagged for the project that owns that kind of change. No owning project in `profile/PROJECTS.md` → park it as a candidate in **this** project's SSOT and say so — never invent a tag (kernel §11).

The promotion itself happens in the owning project's own session, gated as usual. One channel crosses the mount wall; this section no longer grants a second. *(The mirror, deepening answers, and the `PROJECTS.md` row update are person-layer / structural writes with their own designed pipelines — they are not cross-project information and stay as they are.)*

---

## The offload auditor — cold, at close *(built 14 Jul 2026 — SB Product 0.7-15, kernel §13)*

The kernel's §13 guarantee — *no falsifiable claim survives its own session's offload unexamined* — is **this step**. It is the only hard back-stop in the system, and it exists because the enforcer **cannot be the writing model's own attention**: on 14 Jul 2026 the very session convened to fix this defect committed it again, mid-design, and was caught by an independent reader rather than by itself.

**Spawn a cold sub-agent** — no inherited context, and a **different model where one is available**. It gets the staged text and the file paths, nothing else.

### Order of operations

**Stage the writes → audit → write → confirm → closeout.** The auditor reads the diff *about to be written*, not the file after the fact — a flag it raises is cheaper to fix before the write than after.

**Scope is EVERYTHING this session wrote or is about to write — not just the closing diff.** Save-on-decision (above) means a session appends to the SSOT *throughout*, so by offload those entries are already in the file and would sit outside a diff-only audit. **They are in scope.** §13's guarantee is that no falsifiable claim survives **its own session's offload** unexamined — a claim written at 21:00 and audited against nothing at 23:00 would break that guarantee while appearing to honour it. Hand the auditor the session's **full set of writes**, mid-session appends included. *(Caught at the cold `/review` of this build — the first draft scoped the audit to the closing diff and would have let every save-on-decision write through unread.)*

### Its job — "is this true," not "was this checked"

It **cannot** see whether the parent verified anything, so it never asks. It **independently re-verifies falsifiable claims against actual state**: reads the source doc, searches the folder, opens the transcript.

**Scope — bounded, and the bound is binding:**

- the **SSOT diff** about to be written
- the **closeout's surfaced actions** (a §12 action listed as still-open is a *status* claim — this is exactly where the "delete the spec file he had already deleted" failure fired, and one `search_files` would have killed it)
- every **§13-cited** claim — does the named file actually say what the citation claims?
- every **blocker** entry — was a falsification actually attempted, or is the entry malformed?

**It does NOT adjudicate judgment, strategy, or the user's calls.** It has authority over nothing that isn't decidable against a reachable file. **It reports; it never rewrites** — same posture as `/review`. Its findings come back to the parent, which fixes them *before* the write and says so in the closeout.

### Fail-open, loudly — the `unaudited` flag

Sub-agent spawn loss is **~1 in 6** *(observed 13 Jul 2026 — **one** leg lost out of a six-spawn batch. Stated as a rate, but the evidence is a single sample of six: treat it as "spawn loss is real and non-rare," not as a calibrated probability.)* A fail-**closed** audit would block roughly one offload in six, which is indefensible against the adoption red line.

**On spawn loss or auditor failure: the offload proceeds** — and it says so, in **two** places:

1. the **session closeout**, as a visible line: `⚠️ unaudited — the offload auditor did not run; nothing in this session's writes was independently re-verified`
2. the **Session Log row**, as the literal marker `unaudited`

**Roughly one session in six will ship unaudited, and every one of them must say so out loud.** A silent skip would re-create the exact shape this whole issue exists to kill — *an automated check reported nothing, and the human supplied the missing evidence.* Silence is never evidence of a clean audit.

**It does not propagate to `profile/Scoreboard.md`** — the Scoreboard counts work, not confidence.

### ⛔ No auditor at `/orient`

Deliberate, and it stays that way. The spawn floor plus the 1-in-6 loss rate means an agent at the orient door **adds latency to every session** against a live ≤30s acceptance bar. Orient's §12 verification stays **in-parent tool calls**, bound to custom doors by the README-first rule. *(The first draft of this design put an auditor at orient — contradicting a call made the previous night. A cold reader caught it. Noted here so nobody re-proposes it without knowing that.)*

---

## The offload completeness manifest — deterministic, always-on *(built 20 Jul 2026 — 0.7-16)*

The auditor above asks *"is what was written TRUE?"* Nothing asked *"did offload write everything it OWED?"* — and that gap is exactly what fired, repeatedly, before this existed, in SB Product's own ledger (the project that first surfaced it): two sessions each closed with a missing Session Ledger row, caught only a session later; one offload missed **two** writes at once (no Ledger row **and** a stale `**Totals:**` line); a later session found the `**Totals:**` line stale-by-one again at its own orient. **Truth and completeness are different questions, and this build answers the second one — the cold auditor stays exactly what it was, for the first.**

**Why this is deterministic-in-parent, not folded into the cold auditor:** the auditor is fail-**open** — spawn loss is real (~1 in 6, one sample of six, not a calibrated rate) — and if completeness rode on it, that same fraction of offloads would get **no** completeness check, reintroducing the exact silent-skip this exists to kill. Completeness is cheap and mechanical, so it **always** runs, in-parent, regardless of whether the auditor spawns. **Two checks, two reliability classes, on purpose:** completeness = deterministic/always · truth = cold auditor/fail-open.

**Where it sits: after the writes land, before the closeout is allowed to say "clean."** Stage the writes → auditor reviews the diff *about to be written* → write → **completeness manifest re-reads each owed target to confirm it actually landed** → closeout. The completeness pass and the freshness gate's per-write "confirm it landed" step (above) are the same discipline — this section is that discipline, enumerated once instead of applied ad hoc.

**The owed writes — the mechanically checked set** *(every write with a fixed target a re-read can confirm; canonical here — the Build Brief that designed it is a project-layer record of the reasoning, not a file this list depends on)*:

- the SSOT header `**Last updated:**` stamp
- the Session Log row's **close** (end time · `~Hrs` · summary) and **the `**Totals:**` line update** — the exact write that has failed live before, more than once
- the project's Session Ledger row, **if the project keeps one**
- `profile/PROJECTS.md`'s **Sessions** + **Hours** cells, **and** its **Status / Last-session date / Notes cell**
- `profile/Scoreboard.md`'s roll-up totals **and** its appended history row
- the session beacon **clear** — *conditional*, only if this sitting's `/orient` posted one (a branch-3 continuation, or any sitting that reaches offload with no beacon of its own, has nothing to clear — see `Clear the session beacon`, below, and this is not a miss)
- **Done rows moved to `Archive - [PROJECT].md`** — *conditional*, only if a row closed this offload
- **Noticeboard pins** — *conditional*, only if this session produced a cross-project action

**What it does NOT check — the auditor's domain, on purpose, no overlap:** the *content* of decisions / open-actions writes, and the *content* of the Evidence manifest (that it exists is a mechanical write; whether it's *right* is truth). Completeness confirms **the mechanical write landed**; the auditor confirms **it's true**.

**Report one line, always:**
- Clean → `✅ manifest: N/N writes confirmed` — the visible antidote to a bare "offloaded cleanly," one notch louder than the auditor's exceptions-only `unaudited` pattern, deliberately: positive confirmation is the whole point here.
- A miss → **expand and name exactly which owed write is missing or unconfirmed, and refuse to report the offload as clean.** Never report "offloaded cleanly" while any owed item sits unconfirmed — same fail-loud posture as the auditor's `unaudited` flag.

**Honest bound (kernel §13 — this section binds its own description):** this detects a missing write and stops it from *surviving unremarked* — it does not prevent the omission from happening in the first place, and it does not recover a value that was never captured (a start time never taken stays gone; the row is marked `unverified`/a floor, not reconstructed). Same guarantee §13 makes for false claims, extended here to missing writes.

---

## Session closeout *(what the user sees)*

After writing to the SSOT, present a short closeout — the human-facing half of offload. Three parts:

1. **What we did** — a tight summary of the session's work; the wins, banked.
2. **Next step(s)** — the specific, *smallest* concrete first action for next session, pulled from the SSOT Open Actions. Obvious and small enough to start without re-deciding.
3. **A human note** — calibrated to the user's profile and current state. **Earned warmth, not glazing** (read `profile/PROFILE.md` for tone and watch-fors). If the user is frustrated or facing something heavy, steady them — name the single foothold so returning feels small, not like a mountain. Match the profile's tone, not a generic pep talk.

This is what makes the system human-centric: the user leaves clear on what was accomplished and what's next, and comes back oriented and willing.

---

## Relationship to ORIENT

`/offload` writes the SSOT that `/orient` reads. Offload at session end → next session's orient surfaces exactly where you left off. Together they are the system's memory across sessions and machines.
