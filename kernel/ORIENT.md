# ORIENT.md — Session Orientation
*Part of: ROOT v2.2 / kernel*
*Version: 1.1 | 11 Jun 2026*
*Scope: Kernel. Person-agnostic. Runs at the start of every session, in every project.*
*Triggered by: `/orient`*

> Purpose: get Claude fully up to speed before any work — confirm it can read and write, load the system and the project, and surface what's open. Person-specific context comes from `/profile`; this protocol never names a person. Written to be run by any model.

---

## Step 0 — Access check *(do this first, every time)*

Before loading anything, confirm Claude can actually operate:

1. **Read access** — can the ROOT folder be reached (via mounted folder or Drive)? Try to read `kernel/CLAUDE-KERNEL.md`.
2. **Write access** — is the ROOT/project folder **mounted locally** for in-place editing?

| Result | Meaning | Action |
|--------|---------|--------|
| Read ✅ + Write ✅ | Full access | Proceed to Step 1 |
| Read ✅ + Write ❌ | Read-only (folder not mounted) | Say so. Offer to mount now. **Do not write or `create_file`** until mounted. |
| Read ❌ | Can't reach ROOT | Stop. Drive not synced or no access — direct user to `/stress-test`. |

*Why Step 0 exists: a read-only session must never fall back to `create_file` — that re-creates the duplicate-file problem the write-path rule was built to kill.*

---

## Step 1 — Load the system + daily profile *(silent)*

Read, in order:
- `kernel/CLAUDE-KERNEL.md` — engine rules
- `profile/PROFILE.md` — who the user is, working style, guardrails
- `profile/PROJECTS.md` — the project index: what projects exist and where
- `profile/LEARNING-ME.md` — how the user learns
- `profile/STACK.md` — the user's tools and machines

`profile/ABOUT-ME.md` (deep profile) loads on `/stress-test`, not daily. `kernel/GLOSSARY.md` is referenced on demand.

---

## Step 2 — Load project context

Locate the active project in `profile/PROJECTS.md` — flag if it has no row (unregistered) or if its row points at a folder/SSOT that can't be found anywhere in Drive (ghost row). *A project that exists in Drive but isn't mounted on this machine is an **access gap**, not a ghost — prompt to mount, don't flag the row.* Then name-search for `README-[PROJECT].md` in the active project's folder.
- Read it fully.
- Confirm the project folder is identified and recorded in the README.
- Be ready to surface the project instructions (so they can be pasted into Cowork project settings on a fresh machine if needed).
- Flag if the README is missing or out of date.

---

## Step 3 — Load the Decisions Log (SSOT)

Name-search for `Decisions Log - [PROJECT].md` in the project folder.
- **Integrity check:** confirm there is **exactly one**. Flag any duplicate or `conflicted copy` immediately and ask which is canonical — do not proceed on an ambiguous SSOT.
- Read it fully; confirm it's readable and not empty.
- Extract all open actions.
- Flag if missing, unreadable, empty, or stale (no recent updates while work is active).
- If the log carries compaction markers, confirm `Archive - [PROJECT].md` exists (name-search). **Do not read the archive** — it is the SSOT's cold layer, loaded only on demand when history is actually needed.
- **Gap check:** compare today against the project's Last session (in `profile/PROJECTS.md`). If the gap exceeds the profile's threshold *while Open Actions sit untouched*, ask one question — "It's been [X] days — what happened?" — and triage per `kernel/HELP-ME.md` Branch B. Benign answer (trip, rest, life): log one line, move on. Loaded answer: shift to the red-alert flow before starting work.

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

## Output

Report clearly:

| Check | Status | Notes |
|-------|--------|-------|
| Access (read + write) | ✅ / ⚠️ / ❌ | Read-only? Name it. |
| System + profile loaded | ✅ / ❌ | |
| Project in index (PROJECTS.md) | ✅ / ❌ | Flag ghost / unregistered rows |
| Project README found | ✅ / ❌ | |
| Decisions Log — exactly one | ✅ / ❌ | Flag duplicates / conflicted copies |
| Open actions identified | ✅ / ❌ | List them |
| Gap check | ✅ / ⚠️ | Days since last session; ⚠️ triggers the one question |
| Instructions consistent | ✅ / ❌ | Flag contradictions |
| Key people known | ✅ / ❌ | |

**All green** → state what's loaded and what's open. Work begins.
**Any red** → fix before proceeding. Do not start work on a partial orientation.

---

## On a fresh machine

If the project isn't set up in Cowork yet:
1. Run Steps 0–5.
2. Surface the full project instructions from `README-[PROJECT].md`.
3. Prompt: "Paste these into your Cowork project settings, then re-run `/orient`."

The Drive files themselves need no copying — they sync via Drive. Only the Cowork project settings are per-machine.
