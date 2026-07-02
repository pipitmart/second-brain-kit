# GLOSSARY.md — Terms & Commands
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic. Plain-language definitions so any user — technical or not — knows what a term means and why it matters here.*

> If you hit a word in any kernel file you don't recognise, it's defined here. Each entry says **what it is** and **why it matters**.

---

## Core layers

**Kernel** — The engine of the system: the rules and protocols that make it run (boot, orientation, file handling, project setup). *Why it matters:* the kernel is identical for every user. Yours is the same as anyone else's. Nothing personal lives here.

**Profile** — The person-specific layer: who you are, how you work, how you like to learn, and your tools. *Why it matters:* this is the only part that changes from person to person. Set the system up for someone new by swapping their profile — the kernel stays untouched.

**ROOT** — The single Drive folder that holds the whole system (`kernel/`, `profile/`, `skills/`). *Why it matters:* it's the one fixed starting point. Everything is found relative to it.

**Fingerprint** — How the system finds the live ROOT at boot: it's the folder containing `kernel/CLAUDE-KERNEL.md` that sits beside your project folders (never inside one) — whether that's at the Drive top level or inside a shared container that also holds the projects. No folder ID is hardcoded anywhere. *Why it matters:* IDs go stale when folders are renamed or replaced; the fingerprint survives any rename, any version, any machine. Exactly one ROOT is live — copies inside project folders or marked archived are snapshots, never the system. *(Your profile may note ROOT's folder ID as a convenience pointer; if it ever disagrees with the fingerprint, the fingerprint wins.)*

---

## How work is organised

**Instance** — A machine or a Claude install (your Windows PC, your office Mac, your laptop). *Why it matters:* each instance is set up once with `/stress-test`.

**Session** — A single chat. *Why it matters:* every session starts with `/orient` to reload context.

**Project** — A defined scope of work, with its own folder, README, and Decisions Log. *Why it matters:* keeps separate work from bleeding into each other.

**SSOT (Single Source of Truth)** — The one file that holds the truth for a project: its **Decisions Log**. *Why it matters:* if it's written down, it's in the Decisions Log — nowhere else. No second copy, no scattered notes.

**Decisions Log** — `Decisions Log - [PROJECT].md`. The SSOT file for a project's decisions and open actions. *Why it matters:* exactly one per project; it's what `/orient` reads to tell you where things stand.

**Project Index** — `profile/PROJECTS.md`. The registry of all your projects: one row each, with folder, Decisions Log name, and status. *Why it matters:* it's the map. A fresh session or new machine reads it to know what projects exist and where they live — without it, projects are only discoverable by memory. It is machine-agnostic: every machine sees the same full list; what differs per machine is which folders are mounted.

**Noticeboard** — `profile/Noticeboard.md`. The shared cross-project channel. Sessions see only ROOT + their own project folder, so a note meant for another project is pinned here (project-tagged, at `/offload`) and drained by that project's next `/orient` (Step 2.5) into its own Decisions Log, then deleted. One tag per line; tags live in the Project Index's `Noticeboard tag` column. *Why it matters:* it's the only path a signal has across the mount/privacy wall — without it, cross-project actions travel by memory and get silently lost.

**Live / Cold (SSOT layers)** — The SSOT is one truth in two layers. **Live** is the Decisions Log: open actions, standing decisions, recent history — read every session. **Cold** is the Archive: spent history read only on demand. *Why it matters:* keeps daily orientation fast without ever deleting the reasoning trail.

**Archive** — `Archive - [PROJECT].md`. The cold layer of a project's SSOT: superseded decisions, completed actions, closed notes — moved there verbatim by compaction. Created on first compaction, not at setup. *Why it matters:* history is preserved in full, but stops costing attention every session.

**Living mirror (Observed Patterns)** — A dated, append-only section of `profile/ABOUT-ME.md` where `/offload` records how you actually worked — approved by you, flattering and unflattering alike. Repeated patterns get proposed for promotion into your profile baseline. *Why it matters:* it keeps the profile a true reflection of who you are, not a portrait of who you meant to be. It reflects; it never directs.

**Earned question (Deepening)** — One deeper question asked at session close, only when an event made it answerable — a first red alert, a real win or loss, a crossed line. *Why it matters:* deep truths about a person can't be mined by questionnaire; they surface in context. This is how a thin day-one profile becomes a deep one. Data quality ranks: observed > self-reported in context > self-reported cold.

**Compaction** — Moving **spent** entries (superseded, done, closed) from the live Decisions Log to the Archive, verbatim, leaving a marker. Triggered by size (~250 lines), proposed by Claude, approved by you — never silent. **Standing decisions** (rules and conventions still in force) never move, no matter how old. *Why it matters:* compaction by age would eventually archive a rule you still live by; by status, never.

---

## Files & storage

**File ID** — The hidden code Drive gives every file. *Why it matters:* opaque and person-specific, so the kernel does **not** use them — it finds ROOT by fingerprint and everything else by name. Any ID written in a profile file is a convenience pointer, never the source of truth.

**Name search** — Finding a file by its name within ROOT or a project folder, instead of by ID. *Why it matters:* portable (works in anyone's system) and human-readable. Your job is to keep file names correct.

**Mount** — Granting Cowork a "key" to one specific folder on your computer so Claude can read and edit files inside it (and nothing outside it). *Why it matters:* without a mount, Claude can't open or change your files. You grant it once per machine.

**Available offline** — A Drive-desktop setting that keeps the actual file contents on your hard drive instead of streaming them on demand. *Why it matters:* in-place editing needs the real file present, not a placeholder. Set it once per machine.

**In-place edit (write path)** — Changing a file directly instead of making a new copy. *Why it matters:* this is how the system avoids duplicate files. Edits sync across all your machines via Drive.

**Conflicted copy** — A duplicate Drive makes when the same file is edited on two machines before syncing. *Why it matters:* it breaks the "one source of truth" rule. Avoid it by letting Drive finish syncing before switching machines.

**Drive MCP** — Claude's connection to Google Drive for **reading and searching** files in the cloud. *Why it matters:* it can read but should never be used to *create* a file that already exists (that's what caused duplicates). Writing is done by in-place edit instead.

**Access check** — A quick test at the start of `/orient`: can Claude read ROOT, and is the folder mounted so it can write? *Why it matters:* if it's read-only, Claude must ask you to mount the folder rather than silently making duplicate files.

---

## Commands

**`/stress-test`** — Run **once on a new machine**. Checks the environment is set up (Drive synced, folders mounted, skills installed) and that Claude is fully oriented before any work. *Not for daily use.*

**`/orient`** — Run **at the start of every session**. Starts with an access check, then loads ROOT + the active project + its Decisions Log, and surfaces what's open. This is the everyday "get Claude up to speed" command.

**`/new-project`** — Run **when starting a new piece of work**. Walks from a rough objective to a set-up, verified project.

**`/genesis`** — How the system starts for someone new (auto-triggered when `/stress-test` finds an empty profile). Opens with *why this exists*, then forks: **Demo** — a repeatable ten-minute guided run on sample data (a fictional shop owner) that shows the memory loop working across a brand-new chat, touching nothing real; **Live** — builds *your* profile, **once per user**: three honest opening questions, friction diagnosis, a first project scoped from your own data. *Why it matters:* Demo lets you feel the value before investing anything; Live makes it yours — sufficient on day one, then self-correcting via the living mirror.

**`/help-me`** — Run **when you're stuck** — or when the system flags that you might be. Two branches: if the *task or system* is broken, Claude snapshots your work, fixes the problem, and restores you to where you stopped; if *you* are in a loop (work stalled, a red line crossed), it walks the red-alert flow — one question, honest triage, one small re-entry step. *Why it matters:* it's the support channel. There is no architect to call.

**Red line** — An **observable, pre-agreed** event that means stop, pause, or ask for help — set when you're calm (`/genesis` asks for your system-abandonment tell; `/new-project` asks for each project's failure line). *Why it matters:* the system can only quote your own words back to you in the storm if you wrote them in the calm. Feelings don't count — days, numbers, and events do. Revisable in calm only.

**Red alert** — The flow that runs when a loop is detected: a session gap past your threshold with actions untouched, a repeating avoidance pattern in the mirror, or a crossed red line. One question first ("what happened?"), honest triage (rest is not a loop), human-first response, one foothold back. *Why it matters:* flagging it is respect; the pile is what built the loop, so the pile is never the re-entry.

**`/goal`** — Run **any time in a learning session**. Restates the session goal and shows the running checklist — what's mastered vs what's still open. *Why it matters:* keeps long sessions honest about progress.

**`/eli5` · `/eli14` · `/eli1`** — "Explain Like I'm..." Ask Claude to explain something at a chosen level: 5 (simplest, analogies), 14 (curious teenager), 1 (smart first-week intern). *Why it matters:* lets you dial complexity up or down on demand.
