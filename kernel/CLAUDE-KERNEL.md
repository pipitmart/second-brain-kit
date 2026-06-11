# CLAUDE-KERNEL.md — System Engine
*Part of: ROOT v2.2 / kernel*
*Version: 1.1 | 11 Jun 2026*
*Scope: Kernel. Person-agnostic — this file ships unchanged to any user. Nothing here names a specific person. Who the user is lives in /profile.*

> **Read this first.** It is the entry point. It explains how the system is laid out, how to find files, how to write without breaking things, and where to load the person-specific context (the Profile). If a term is unfamiliar, see `kernel/GLOSSARY.md`.

---

## 0. What this system is

A second brain that gives Claude persistent memory of **(a)** who the user is and how they work (the **Profile**) and **(b)** what they're working on (the **Projects**) — so the user stops re-briefing Claude every session, and decisions live in one trusted place.

The machinery that runs this — boot, orientation, file rules, project setup — is the **Kernel**. The Kernel is identical for every user. Only the Profile changes from person to person.

---

## 1. Architecture

```
ROOT  (one folder in the user's Drive — the only hardcoded anchor; its ID is recorded in the Profile)
├── START-HERE.md       → the only file a new human reads; contains the bootstrap prompt
├── kernel/             → the engine (this folder; person-agnostic, ships to anyone)
├── profile/            → who the user is, how they work, their tools (regenerated per person; NEVER ships)
├── profile-templates/  → blank skeletons /genesis copies into profile/ and fills in
└── skills/             → thin pointer skills (source + installable .skill files); each reads its kernel .md

Project folders  (elsewhere in the user's Drive)
├── README-[PROJECT].md         → project context + instructions
└── Decisions Log - [PROJECT].md → SSOT for that project's decisions and open actions
```

---

## 2. Nomenclature

| Term | Meaning |
|------|---------|
| Instance | A machine or Claude install |
| Session | A single chat within a project |
| Project | A defined scope of work |
| Global / Kernel | Applies to every user, instance, session, project |
| Profile | The person-specific layer — swapped per user |
| Anchor | The one hardcoded ROOT folder ID (in the Profile) |
| SSOT | Single source of truth — the project's Decisions Log |

Full definitions: `kernel/GLOSSARY.md`.

---

## 3. File discovery

- The **ROOT folder is the only hardcoded anchor.** Its folder ID is recorded in `profile/PROFILE.md`.
- Everything else is found by **name search** within ROOT or the relevant project folder. Never hardcode any other file ID.
- Always **search before referencing** a file. If Drive is unavailable, say so explicitly — never guess a file ID or silently fail.

---

## 4. Write path *(verified cross-machine, 10 Jun 2026: Windows → Mac)*

- **Edit ROOT and project docs IN PLACE** via Cowork local Edit on the Drive-synced folder. Drive desktop syncs the change to every machine.
- **Never use the Drive MCP `create_file` on a doc that already exists** — it spawns a duplicate and breaks the SSOT. The Drive MCP is for **read/search only**.
- **Per machine, one-time:** set the synced ROOT/project folder to **Available offline** so Edit reads real bytes, not stream placeholders.
- **Discipline:** let Drive finish syncing (no spinner) before switching machines; never edit the same file on two machines at once, or Drive makes a conflicted copy.

*This rule is model-agnostic — it binds whether Claude runs as Opus, Sonnet, or any other model.*

---

## 5. Decisions Log (SSOT)

- Lives **only at the project layer** — never in ROOT.
- Naming: `Decisions Log - [PROJECT].md`. **Exactly one per project.**
- It is the **single source of truth** for that project's decisions, open actions, and pending items.
- On orientation, verify there is exactly one, that it is current, and **flag any duplicate or conflicted copy.**
- **Before writing to it, confirm edit access** (the folder is mounted locally for in-place Edit). If access is read-only, do **not** fall back to `create_file` — prompt the user to mount the folder first.

---

## 6. Commands

| Command | What it does |
|---------|-------------|
| `/stress-test` | Instance setup check — run once on a new machine |
| `/orient` | Session-start orientation — load ROOT + project, surface open actions |
| `/new-project` | Set up a new project from a rough objective |
| `/genesis` | Build a new user's profile — once per user; auto-runs when stress-test finds an empty profile |
| `/help-me` | Support channel, two branches: troubleshoot a stuck system/task (snapshot → fix → restore), or walk the red-alert flow when the *person* is in a loop |
| `/eli5` `/eli14` `/eli1` | Explain at a chosen level of simplicity |
| `/goal` | Restate the session goal + show the learning checklist |

The Profile may define personal aliases for these. Full definitions: `kernel/GLOSSARY.md`.

---

## 7. Boot sequence

- **New instance** (once per machine): `/stress-test` → install skills → mount the ROOT folder into Cowork + set Available offline → environment check.
- **New session** (every time): `/orient` — **starts with an access check** (can I read ROOT? is the folder mounted for in-place writes?), then loads the active project and surfaces open actions.
- **New project:** `/new-project`.

---

## 8. Profile linkage — the separation rule

**The Kernel never names a person.** At orientation, load the Profile for everything person-specific:

- `profile/PROFILE.md` — who the user is, working style, guardrails
- `profile/PROJECTS.md` — the project index: every project, its folder, SSOT, and status
- `profile/ABOUT-ME.md` — deep profile + the living mirror (load on stress-test, not daily; `/offload` appends observed patterns)
- `profile/LEARNING-ME.md` — the user's teaching standard and learning patterns
- `profile/STACK.md` — the user's tools and machines

**To set the system up for a different person, swap the `profile/` folder. The Kernel stays untouched.** This is the whole point of the kernel/profile split.

---

## 9. Model use

Protocols are written to be executed by **any** model. Use a lighter model (e.g. Sonnet) for execution — applying conventions, logging, edits — and a stronger model (e.g. Opus) for design and stress-testing. **Authorisation, not model strength, gates changes.**

---

## Kernel file index

| File | Role | Status |
|------|------|--------|
| `CLAUDE-KERNEL.md` | This file — entry point and engine rules | ✅ built |
| `GLOSSARY.md` | Defines every term and command | ✅ built |
| `ORIENT.md` | Session-start orientation protocol | ✅ built |
| `STRESS-TEST.md` | Instance setup + repair; escalates to help-me | ✅ built |
| `NEW-PROJECT.md` | New project flow (merges CW + Chat) | ✅ built |
| `SSOT-TEMPLATE.md` | The Decisions Log format every project uses | ✅ built |
| `OFFLOAD.md` | Session-close + autosave: write decisions/actions to the SSOT | ✅ built |
| `PROFILE-GENESIS.md` | Builds a new user's profile — 3 openers, friction diagnosis, first win | ✅ built |
| `HELP-ME.md` | Troubleshooter + red alert: task branch & person branch, red lines | ✅ built |
| `TEACHING-LOOP.md` | Teaching scaffold — mastery standard, the loop, onboarding-as-teaching | ✅ built |
