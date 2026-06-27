# SSOT-TEMPLATE.md — Decisions Log Format
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic. The one format every project's Decisions Log uses.*

> The **Decisions Log** is the SSOT (single source of truth) for a project. **Exactly one per project**, named `Decisions Log - [PROJECT].md`. `/new-project` creates it from this template, `/orient` reads it, `/offload` writes to it. Always **edited in place** — never re-created.

---

## The template

```
# Decisions Log - [PROJECT]
*Owner: [name] · Created: [DD Mon YYYY] · Folder: [project folder] · README: README-[PROJECT].md*

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
| `/orient` | Reads it; verifies exactly one; surfaces Open Actions; **opens a Session Log row (start time) and shows the running total** |
| `/offload` | Appends decisions and updates Open Actions in place; **closes the Session Log row (end time, ~hrs, summary) and updates the total**; auto-compacts (one-line notice, asks only on ambiguity) when the live log passes ~250 lines |
