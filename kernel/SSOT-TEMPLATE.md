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

## Open Actions            ← HOT · OPEN rows only · ~3-line cap per row
| # | Action | Owner | Status | Opened |
|---|--------|-------|--------|--------|
| 1 | [smallest concrete next step] — [pointer: the dated Decision entry or Build Brief holding the full reasoning] | [name] | Open | [DD Mon YYYY] |

## Evidence            ← HOT · what exists and where · one line each · added at /offload
- [source]: [what it contains, in one line] — [where it lives]

## Next session            ← HOT · optional · the agenda flags, a few lines
- [what the next session opens on]

## Session Log             ← HOT: this header, the totals line, and the table's column header. The ROWS are warm.
**Totals:** [N] sessions · ~[H] hours since [DD Mon YYYY]
*Hours are approximate — a session closed without `/offload` isn't time-stamped, so it counts toward sessions but not hours. A sense-of-progress number, not a timesheet.*

| # | Date | Start–End (local) | ~Hrs | What happened |
|---|------|-------------------|------|---------------|
<!-- ═══ WARM BELOW — /orient reads above this line ═══ -->
| [N] | [DD Mon YYYY] | [HH:MM]–[HH:MM] | [n.n] | [one-line summary] |   ← newest first: `/orient` Step 6 inserts here, directly under the marker
| 1 | [DD Mon YYYY] | [HH:MM]–[HH:MM] | [n.n] | [one-line summary] |

## Decisions  (newest first)
- **[DD Mon YYYY]** — [the decision]. *Why:* [rationale].

## Parked / Pending
- [item] — waiting on [what / whom]
```

---

## The hot/warm split — the read boundary *(built 14 Jul 2026, SB Product 0.7-14)*

**One file, two zones, one marker line.** The SSOT stays **exactly one file** — a second file claiming truth about open state is the duplication disease the file diet (0.7-2) exists to kill. What changes is *how much of it `/orient` reads*.

```
<!-- ═══ WARM BELOW — /orient reads above this line ═══ -->
```

**The marker is a load-bearing interface** — a fixed literal line, like the CHANGELOG's version header. It is grep-able and unambiguous; it is never reworded, decorated, or duplicated. `/orient` Step 3 reads **down to it and stops**.

| Zone | Holds | Who reads it |
|------|-------|--------------|
| **HOT** (above the marker) | `Last updated:` header · the **current** status paragraph, if the project versions its work (e.g. a roadmap's `◀ CURRENT` block) · **Open Actions / Active Issues as a true index — OPEN rows only** · the **Evidence manifest** · the next-session agenda · the Session Log **header + totals line** | **Every `/orient`, in full** |
| **WARM** (below the marker) | All dated **Decisions** entries · Session Log **rows** · Backlog · Parked · closed-version one-liners · compaction markers | **On demand only** — when a session actually needs that history |

### The three rules that keep the hot zone hot

1. **A hot row is an index entry, not an argument.** Cap each open row at **~3 lines**: ID · one-line statement of the issue/action · owner + status · **a pointer** to the dated Decision entry or Build Brief that holds the full reasoning. *The reasoning is never deleted — it is relocated to where reasoning lives.* **Before cutting a row, verify its reasoning exists somewhere else** — if the row is the *sole* home of it, move the reasoning into a dated Decision entry first, then cut the row (kernel §11: nothing is lost to a tidy-up).
2. **Closed rows leave the hot zone at close.** A row that reaches Done/Closed moves **verbatim to `Archive - [PROJECT].md`** at that offload — not at the next ~250-line compaction. Supersede-never-delete is untouched: the Archive is the destination, not deletion.
3. **Compilation is paid at write time, not read time.** Keeping the hot zone lean is **`/offload`'s** job, every session — see `kernel/OFFLOAD.md`. `/orient` does no maintenance; it just reads less. *(This is the pattern every high-adoption memory system converged on — compiled layer, append-only trail, tiny boot index. Evidence: `Research - 0.7-14 Token Architecture - SB Product.md`.)*

### The Evidence manifest — fix the epistemics, not the discipline *(built 14 Jul 2026, SB Product 0.7-15 · M3)*

A **compiled index of what exists and where**, maintained at every `/offload`, living in the hot zone — so it is in context on **every** `/orient`, at zero marginal cost.

```
## Evidence
- Meet Recordings/: 5 install transcripts (Samantha 29 Jun → Gunawan 10 Jul), verbatim + timestamped
- Alfa NDA: Shannon = Paramount (Introduction to Alfa Medicus - Brand Deck.pdf, same folder)
```

**Why it exists.** On 14 Jul 2026 a design was gated on a workflow leg declared *"unproven"* — while **five fully-transcribed install calls had been sitting in the mount for weeks.** The claim was not a reasoning failure. **The fact was simply nowhere in context**, and checking it required a search nobody thought to run. A rule telling the model to search harder does not fix that; **a line already in front of it does.** An absence-claim then collides with the evidence *passively, at read time*, before it is ever spoken.

**Rules:**

- **One line per source.** What it is, what it contains, where it lives. It is a **map, not a copy** — never paste the evidence itself.
- **Written at `/offload`, by the session that learned the fact.** Same discipline as every other hot-zone row: the compilation cost is paid once, at write time, by the session with the context (kernel `OFFLOAD.md`).
- **It lives or dies on the hot-zone budget.** The manifest counts against the **≤8K hot-zone ceiling** like everything else above the marker. If it grows past its keep, it is not earning its slot — cut it back to what a session would actually be wrong without.
- **It covers presence and absence far better than attribution.** Stated plainly because the failure it is weakest against is real: `profile/People-web.md` **already held** the "Shannon = Paramount" fact on 14 Jul, and **was not consulted.** A map only helps if it is loaded — which is precisely why this one lives hot instead of one read away.

### Guarantees that survive the split

- **Nothing parked is lost.** The Backlog moves warm, but its surfacing rule is unchanged: it is read deliberately at an explicit event (a gate opening, a roadmap review), and the hot status paragraph carries the cue. Warm ≠ forgotten; warm = read when it matters.
- **Fail open, never halt.** Marker missing, duplicated, or hot zone malformed → **read the whole file exactly as before** and flag it in one line in the orientation report. Never a halt, never a silent self-repair (kernel §10 — detect and surface). A dangling index pointer → flag that row, don't block the session.
- **The gates are untouched.** Access, freshness, and the Noticeboard drain run exactly as specified in `ORIENT.md` — this split changes *how much is read*, never *what is verified*.
- **Mid-session, read what you need.** A session that needs history reads that section (search / offset), not the whole file by reflex.

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
- **Closed rows go on close, not on the threshold** *(added 14 Jul 2026, 0.7-14).* An Open Action / Active Issue row that reaches **Done** moves to the Archive **at that same offload**, verbatim — it does not sit in the hot zone waiting for a compaction to notice it. Its outcome lives on in the dated Decision entry that resolved it, which is where a reader looks anyway. *(Why: the hot zone is the boot index. A Done row costs a read on every future `/orient` forever and answers no question the Decisions entry doesn't answer better. The ~250-line trigger governs the **warm** narrative; closed rows are governed by their own status.)*
- **Auto-execute, notify, ask only on ambiguity.** Offload relocates the clearly-spent entries itself and reports a one-line notice (`Compacted N entries → Archive`) — no approval gate. It pauses for a yes/no **only** when an entry's status is genuinely unclear (standing-or-spent ambiguous). Moves are verbatim and reversible, which is what makes auto-by-default safe; the one-line notice preserves the trace.

---

## Session Log — the build ledger

A countable record of every session on the project: how many, roughly how many hours, and a one-line trail of what each one did. Its job is **motivation made visible** — the user can see the project being built across time — and a lightweight history alongside the decisions.

- **The section straddles the marker** *(0.7-14).* Its **header, `**Totals:**` line, and the table's column-header + separator rows sit HOT** (so every `/orient` sees "N sessions · ~H hours" without reading a single row, and so Step 6 has a well-formed append target inside its read window); the **data rows sit WARM**, immediately below the marker. Step 6 anchors its insert on the separator row + the marker — both of which it has read — and writes the new row directly beneath the marker.
- **`/orient` opens the row** at session start: insert a new row directly under the marker with the date and start time (user's local timezone), end left blank.
- **`/offload` closes the row** at session end: fill the end time, compute `~Hrs`, write the one-line summary, and update the `**Totals:**` line.
- **Newest-first — insert directly below the marker** *(changed 14 Jul 2026, 0.7-14; supersedes the old "oldest-first, append at the bottom" rule).* **Why it changed:** the old rule put the append target at the very bottom of the file — the one place a hot-zone read can never see — so Step 6 could not open a session without reading the whole log, which is the exact cost this split exists to remove. Newest-first puts it at the boundary, one line below the marker. **The consequence to know:** the last row number is no longer the session count — **the `**Totals:**` line is** (and it is hot, so it is always in hand). `/offload`'s "reopen the most recently closed row" branch means the **topmost** row. *(Caught at the cold `/review` of this very build: the first version of the split left both ordering rules standing and would have inserted a row above the table header — malformed markdown on the first post-split orient of every install.)*
- **The `unaudited` marker** *(0.7-15).* If the offload auditor did not run (spawn loss — ~1 in 6), the row carries the literal marker `unaudited` in its summary. It is never omitted to keep the row tidy: a row that doesn't say it was unaudited is claiming it was audited. *(It does not propagate to `profile/Scoreboard.md` — that counts work, not confidence.)*
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
| `/orient` | Reads the **hot zone** (down to the marker; full-read fallback + flag if the marker is broken); verifies exactly one; surfaces Open Actions **tagged per §13**; appends any `[from Noticeboard]` rows drained by Step 2.5; **opens a Session Log row (start time) and shows the running total**; posts the session beacon. Does **no** maintenance. |
| `/offload` | Appends decisions (warm) and updates Open Actions in place (hot, ~3-line cap); **updates the Evidence manifest** (hot); **closes the Session Log row (end time, ~hrs, summary, `unaudited` if the auditor didn't run) and updates the totals line**; **moves Done rows to the Archive**; keeps the marker intact; auto-compacts (one-line notice, asks only on ambiguity) when the warm narrative passes ~250 lines; clears the session beacon |
