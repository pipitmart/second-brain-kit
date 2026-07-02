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
- **Session Log** — close the row `/orient` opened: fill the **end time** (user's local timezone), compute `~Hrs`, write a one-line summary of what the session did, and update the running total (`N sessions · ~H hours`). Hours are approximate — if no start time was recorded, leave `~Hrs` as `—` and count the session but not the hours. (Format + rules: `kernel/SSOT-TEMPLATE.md → Session Log`.)
- **Header stamp (required, every offload).** Update the Decisions Log's top `**Last updated:**` line to this session's date + cont-number + a one-line summary. This line is the SSOT's **freshness stamp** — a reader (or a not-yet-synced Drive web view) compares it against the latest content to know if they're looking at a current copy. A stale stamp silently defeats that check, so it is never optional: if you wrote anything to the log this session, you bumped the stamp.

It also updates the project's row in `profile/PROJECTS.md` — Status and Last session date — so the index stays a live map.

---

## Noticeboard pin — cross-project actions *(built 02 Jul 2026, pairs with `ORIENT.md` Step 2.5)*

If the session produced an action or decision **another project must act on**, never let it travel by memory or a verbal handoff — pin it to `profile/Noticeboard.md` (ROOT — the always-writable anchor) as part of offload:

- **Format, one note per line:** `- [DD Mon YYYY] #project-tag — note — source — status`. Terse — the note is a pointer; the full substance stays logged in **this** project's SSOT.
- **The tag comes from the target project's `Noticeboard tag` column in `profile/PROJECTS.md`** — stored, never guessed from the project name. No row or no tag → ask, don't invent (kernel §11).
- **One tag per line.** A note for N projects = N lines, one per target. *(The drain hard-deletes whole lines — a shared line would vanish for the second project the moment the first drains it.)*
- **Freshness-check `Noticeboard.md` immediately before the write** — same rule as every other offload write (above).
- The target's `/orient` Step 2.5 does the rest: appends to their SSOT `[from Noticeboard]`, confirms, deletes the line. The board is a channel, not a record — it stays short by design.

*Why: cross-project closes that traveled by memory have already failed silently (30 Jun 2026 — a git-LICENSE close never reached the SB record and re-surfaced as phantom work). The pin is the single write channel that crosses the mount/privacy wall.*

---

## Rules

- **Supersede, never delete.** If the session reversed an earlier decision, do not erase it — mark it `superseded [date]` and add the new decision. The log is an audit trail; the reasoning history matters.
- **Edit in place.** Never re-create the log (that spawns a duplicate and breaks the SSOT).
- **Only log what was actually decided.** No fabrication, no inferred decisions — **and no assumed or default-filled values** (times, timezones, IDs, dates, names). Every value written traces to loaded profile/project data, this session's observation, or the user. If a needed value isn't known, **ask before writing** — never stamp a guess. (Kernel §11.)
- **Exactly one log per project.** If a duplicate or conflicted copy appears, flag it before writing.

---

## Compaction (live → cold)

When the Decisions Log passes **~250 lines**, compact as part of offload — **automatically, not as a to-do:**

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

## Session closeout *(what the user sees)*

After writing to the SSOT, present a short closeout — the human-facing half of offload. Three parts:

1. **What we did** — a tight summary of the session's work; the wins, banked.
2. **Next step(s)** — the specific, *smallest* concrete first action for next session, pulled from the SSOT Open Actions. Obvious and small enough to start without re-deciding.
3. **A human note** — calibrated to the user's profile and current state. **Earned warmth, not glazing** (read `profile/PROFILE.md` for tone and watch-fors). If the user is frustrated or facing something heavy, steady them — name the single foothold so returning feels small, not like a mountain. Match the profile's tone, not a generic pep talk.

This is what makes the system human-centric: the user leaves clear on what was accomplished and what's next, and comes back oriented and willing.

---

## Relationship to ORIENT

`/offload` writes the SSOT that `/orient` reads. Offload at session end → next session's orient surfaces exactly where you left off. Together they are the system's memory across sessions and machines.
