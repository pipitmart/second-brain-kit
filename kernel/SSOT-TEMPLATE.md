# SSOT-TEMPLATE.md — Decisions Log Format
*Part of: ROOT v2.2 / kernel*
*Version: 1.1 | 11 Jun 2026*
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
```

---

## Rules

- **Exactly one per project.** Duplicates or conflicted copies break the SSOT — `/orient` flags them and stops rather than guessing which is canonical.
- **Open Actions are small and actionable** — each is a concrete next step a person could start immediately, not a theme or goal. *How small to chunk is a profile preference* (e.g. decompose to the smallest unambiguous step).
- **Decisions are newest-first**, each with a `*Why:*` line — rationale travels with the decision.
- **Parked** holds anything deferred, with what it's waiting on, so nothing is silently dropped.
- **Write as you go.** Append a decision the moment it's made — the autosave behaviour `/offload` enforces — not only at session end. Requires write access; if read-only, stage it and prompt to mount.

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
- **Trigger is size, not time** — when the live log passes ~250 lines, `/offload` proposes compaction.
- **Propose, then move.** Claude shows the move list in plain language; the user approves before anything relocates. Never silent.

---

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
| `/orient` | Reads it; verifies exactly one; surfaces Open Actions |
| `/offload` | Appends decisions and updates Open Actions in place; proposes compaction when the live log passes ~250 lines |
