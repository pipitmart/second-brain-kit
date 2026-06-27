# SKUNKWORKS-TEMPLATE.md — the project idea inbox
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic — ships unchanged to anyone. A project-layer template, like `SSOT-TEMPLATE.md`.*

> Copy this into a project folder as `Skunkworks - [PROJECT].md` when a project starts generating more ideas than its roadmap/backlog should hold. Optional — a project only needs one when stray ideas start piling up.

---

## What a Skunkworks file is

An **idea inbox** for one project. Raw, half-formed, ungated ideas land here the moment they appear, so they survive without cluttering the things that carry committed work.

**What it is NOT:** a source of truth. Decisions, open actions, and live state belong in the project's **Decisions Log (SSOT)** — never here. Skunkworks holds only ideas that haven't been decided on yet.

## Why it's a separate file (not a section of the SSOT)

`/orient` reads the whole Decisions Log every session. Parking raw ideas *inside* the SSOT makes every session more expensive. A separate Skunkworks file sits **outside orient's read path** — so ideas cost nothing per session until they're promoted. Keeping ideas here keeps the SSOT lean by construction.

## How to use it (the one rule that keeps it clean)

**One-way promotion.** Skunkworks → triage → Backlog (or discard). An idea never stays here once it's been acted on:

1. **Capture** — drop the idea in `## Open ideas` as: `[date] hook — why it might matter — status`. Terse, lossless (keep the *why*; that's the part you can't reconstruct later). Ask to expand any line.
2. **Review on a schedule** — at every **release cut** and every **roadmap review**, read this file and triage. (Make this a standing convention in the project's "How we track" / release checklist.)
3. **Promote or discard** — a ripe idea moves into the gate-grouped Backlog in the Decisions Log (where committed, version-gated work lives); a dead one is struck. Nothing lingers undecided forever.

This is the same shape as a feedback-inbox → backlog pipe: a holding pen that feeds the system of record, never a second system of record.

---

## Format

```
# Skunkworks - [PROJECT]
**What:** idea inbox. Raw, ungated ideas before they're roadmap-shaped.
**Discipline:** NOT an SSOT. One-way — idea → triage into the Backlog (Decisions Log) or discard. Never store decisions/state here.
**Why separate:** orient never loads this; keeps the SSOT lean.
**Review protocol:** read + triage at every release cut and roadmap review.

## Open ideas
**[date] hook** — why it might matter — *Status: where it's headed (gate / watch / discard).*
```
