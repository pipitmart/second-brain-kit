# Scoreboard.md — the metric
*Part of: ROOT / profile*
*Scope: Profile. Written by `/offload` (rolled up at every session). Read on demand — not part of the daily `/orient` load.*

> **What this is.** Your system's one honest metric, in one place: **projects · sessions · hours**. Raw counts of work the system already recorded — never a multiplier, never a comparison to an imagined "without the system" baseline (kernel §11).
>
> **This file is a DERIVED VIEW, not a second ledger.** The per-project counters live in **`profile/PROJECTS.md`** (`Sessions` + `Hours` columns), written by each project at its own `/offload` — the only session that can read that project's log. This file just **rolls them up**. No number is held twice, so the two cannot drift. *(If they ever disagree: `PROJECTS.md` wins. Recompute, don't reconcile.)*
>
> **You do nothing.** It fills itself from your first `/offload` onward.

---

## TOTALS — as of [date]
*Rolled up from `profile/PROJECTS.md`. Recomputed at every `/offload`; never estimated between.*
*Scope rule: **`Projects` = Active rows only** (a count of what's live — it may legitimately fall). **`Sessions` and `Hours` sum ALL rows — Active *and* Paused/Closed** — because they record work that happened. Closing a project must never make these two go down.*

| Metric | Count | Confidence |
|--------|-------|------------|
| **Projects** | 0 | — |
| **Sessions** | 0 | — |
| **Hours tracked** | 0 | — |

*Hours is a **floor, not a total**: only sessions with a recorded `~Hrs` stamp count. Untimed sessions are never estimated (§11) — they are absent, not guessed. Publish this number as **"hours tracked."***

---

## Session history — one row per `/offload`
*Append-only time series. Never rewrite a past row. (The totals above do **not** come from this table — they come from `PROJECTS.md`. This is the audit trail and the raw material for any future chart.)*

| Date | Project | Session # | ~Hrs | One-line outcome |
|------|---------|-----------|------|------------------|
| *(none yet)* | | | | |
