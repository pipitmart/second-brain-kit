# SSOT-TEMPLATE.md — Decisions Log Format
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic. The one format every project's Decisions Log uses.*

> The **Decisions Log** is the SSOT (single source of truth) for a project. **Exactly one per project**, named `Decisions Log - [PROJECT].md`. `/new-project` creates it from this template, `/orient` reads it, `/offload` writes to it. Always **edited in place** — never re-created.

---

## The template

```
# Decisions Log - [PROJECT]
*Owner: [name] · Created: [DD Mon YYYY] · Folder: [project folder] · README: README-[PROJECT].md*
**Last updated:** [DD Mon YYYY] · Sess.[N] — [one-sentence outcome, nothing chained beneath it]

## Open Actions
| # | Action | Owner | Status | Opened |
|---|--------|-------|--------|--------|
| 1 | [smallest concrete next step] | [name] | Open | [DD Mon YYYY] |

## Decisions  (newest first)
- **[DD Mon YYYY]** — [the decision]. *Why:* [rationale].

## Parked / Pending
- [item] — waiting on [what / whom]

## Session Log
*[N] sessions · ~[H] hours since [DD Mon YYYY]*
*Hours are approximate — a session closed without `/offload` isn't time-stamped, so it counts toward sessions but not hours. A sense-of-progress number, not a timesheet.*

| # | Date | Start–End (local) | ~Hrs | What happened |
|---|------|-------------------|------|---------------|
| 1 | [DD Mon YYYY] | [HH:MM]–[HH:MM] | [n.n] | [one-line summary] |
```

---

## Rules

- **Exactly one per project.** Duplicates or conflicted copies break the SSOT — `/orient` flags them and stops rather than guessing which is canonical.
- **Open Actions are small and actionable** — each is a concrete next step a person could start immediately, not a theme or goal. *How small to chunk is a profile preference* (e.g. decompose to the smallest unambiguous step).
- **Decisions are newest-first**, each with a `*Why:*` line — rationale travels with the decision.
- **Parked** holds anything deferred, with what it's waiting on, so nothing is silently dropped.
- **Write as you go.** Append a decision the moment it's made — the autosave behaviour `/offload` enforces — not only at session end. Requires write access; if read-only, stage it and prompt to mount.
- **The `**Last updated:**` header stamp is ONE line, always replaced, never chained** *(file-diet rule, 0.7-2, 06 Jul 2026)* — date + session number + a single-sentence outcome, nothing else. It exists purely as the freshness check (does this copy match the latest cloud version?); the actual history of what happened lives once, in `## Session Log` below (and a project's WARM `Session Ledger`, if it has one). Full behavior + why: `kernel/OFFLOAD.md → What it writes`.
- **The SSOT owns all living state — the README does not.** Open actions, decisions, status, and anything that changes session to session belong here, in exactly one place. `README-[PROJECT].md` is the **charter** (objective, scope, constraints, key people, conventions, red line) and changes only when the charter itself changes. A README must **never** carry its own open-items / to-do list — two homes for the same state guarantees drift.

---

## Live and cold — the SSOT's two layers

The SSOT is **one truth in two layers**:

| Layer | File | Holds | Read when |
|-------|------|-------|-----------|
| **Live** | `Decisions Log - [PROJECT].md` | Open Actions, **standing decisions**, recent history | Every session (`/orient`) |
| **Cold** | `Archive - [PROJECT].md` | **Spent** history: superseded decisions, Done actions, closed notes, old context that no longer factors into current decisions (an ended sub-effort, a failed proposal) | On demand only, when history actually matters |

The cold layer carries the same guarantees as the live one: exactly one per project, edited in place, supersede-never-delete. It is created **on first compaction**, not at project setup — a new project has no spent history to hold.

### Compaction rules (how entries move live → cold)

- **By status, never by age.** Only spent entries move. **Standing decisions** — conventions, scope, rules still binding — stay in the live log no matter how old they are. Age says nothing; status says everything.
- **Verbatim relocation, no rewriting.** Entries move whole, newest-first in the archive. Nothing is summarized away — quality is preserved because this is relocation, not compression.
- **Leave a marker** in the live log where entries left: `*Compacted [DD Mon YYYY]: N entries → Archive - [PROJECT].md*`
- **Trigger is size, not time** — when the live log passes ~250 lines, `/offload` compacts.
- **Auto-execute, notify, ask only on ambiguity.** Offload relocates the clearly-spent entries itself and reports a one-line notice (`Compacted N entries → Archive`) — no approval gate. It pauses for a yes/no **only** when an entry's status is genuinely unclear (standing-or-spent ambiguous). Moves are verbatim and reversible, which is what makes auto-by-default safe; the one-line notice preserves the trace.

---

## Session Log — the build ledger

A countable record of every session on the project: how many, roughly how many hours, and a one-line trail of what each one did. Its job is **motivation made visible** — the user can see the project being built across time — and a lightweight history alongside the decisions.

- **`/orient` opens the row** at session start: append a new line with the date and start time (user's local timezone), end left blank.
- **`/offload` closes the row** at session end: fill the end time, compute `~Hrs`, write the one-line summary, and update the running total at the top of the section.
- **Oldest-first — append at the bottom.** This is a deliberate exception to the newest-first convention everywhere else: the ledger reads as a build history, and the last row number *is* the session count.
- **Hours are approximate, by design.** A session closed without `/offload` never gets an end stamp — leave its `End` and `~Hrs` as `—` and don't count its hours (still count the session). Never present the total as precise; it's a sense-of-progress number, not a timesheet.
- **The running total is never reset.** On compaction, old rows relocate to the archive verbatim but the `N sessions · ~H hours` total carries forward in the live log.
- **Reopen, don't renumber** *(built 06 Jul 2026 — fixes a live mislabeling: a session that ran `/offload`, then kept going in the same sitting with no fresh `/orient`, got split into two numbered rows).* **A session number is minted in exactly one place — `/orient` Step 6.** Nowhere else ever invents one. The test is not "is the last row's End blank" (that can't be read off the file alone — a genuinely new sitting that deferred Step 6 also shows the last row already closed). The test is **what happened in *this* sitting**, so it has three branches, not two:
  1. **`/orient` ran this sitting (Steps 0–5, the load) and Step 6 opened a row** → close that row normally at offload: end time, `~Hrs`, summary, running total.
  2. **`/orient` ran this sitting but Step 6 was deferred** (a quick-question session under the Load-first rule) → at offload, **mint Step 6 now**: open a new row using this sitting's best-known start (or `—` if genuinely unrecorded) and close it in the same motion. This *is* a new session — just a belated write, still a fresh number.
  3. **`/orient` did not run at all this sitting** (work continuing past a prior close with no fresh load in between) → **never mint a new row.** Reopen the most recently closed row instead: extend its `End` to now, recompute `~Hrs` across the full merged span, and append the new work to its summary as a marked continuation (e.g. `+ cont. HH:MM–HH:MM: …`). The row's `Date` and original `Start` never change. **If that row was already relocated to the Archive by compaction** (no live-log target to reopen), don't reach into the archive — open a fresh row instead, carrying the running total forward (+1), and say so in the summary (`cont. of an archived row, #N`) so the count stays honest without disturbing the cold layer.
  - *Why branch 2 exists:* without it, every deferred-Step-6 session (Sess.41 in SB Product's own ledger is a real example) would wrongly fold into branch 3 and merge two unrelated sessions' hours together.
  - *Applies to any file that keeps its own numbered session rows* — including a project's separate WARM ledger (e.g. `Session Ledger - [PROJECT].md`), if one exists. Both files move together: whichever branch fires for the Decisions Log's Session Log fires identically for the ledger, so the two never drift out of step.
  - *Why this is the fix, not a workaround:* a "session" (per `GLOSSARY.md`) is one continuous chat — it is bounded by when the sitting actually starts and ends, not by how many times `/offload` happens to run inside it. Tying the number exclusively to whether `/orient` loaded this sitting (branches 1–2) vs. didn't (branch 3) makes that true instead of assumed.

## Status vocabulary (Open Actions)

| Status | Meaning |
|--------|---------|
| Open | Not started |
| In progress | Being worked |
| Blocked | Stuck — note the blocker in the Action cell |
| Done | Completed — clear out, or fold the outcome into Decisions |

---

## Who touches it

| Protocol | Action |
|----------|--------|
| `/new-project` | Creates it from this template, seeds Open Actions with the session's next steps |
| `/orient` | Reads it; verifies exactly one; surfaces Open Actions; appends any `[from Noticeboard]` rows drained by Step 2.5; **opens a Session Log row (start time) and shows the running total** |
| `/offload` | Appends decisions and updates Open Actions in place; **closes the Session Log row (end time, ~hrs, summary) and updates the total**; auto-compacts (one-line notice, asks only on ambiguity) when the live log passes ~250 lines |
