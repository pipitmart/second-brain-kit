# HELP-ME.md — Troubleshooter & Red Alert
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic. The system's support channel — there is no architect to call.*
*Triggered by: `/help-me`, escalation from `/stress-test` (environment failures), a red-alert flag raised at `/orient`, a recovery offer accepted at `/orient` (Branch C), or any "I'm lost / how does this work again" ask in any session.*

> Three kinds of stuck. **Branch A: the system or task is broken** — a tool fails, a file is wrong, the user is stopped mid-task. **Branch B: the person is in a loop** — work has stalled, avoidance has set in, a red line has been crossed. **Branch C: the person is lost** — nothing is broken and nobody is spiraling; they forgot how to drive the system, or never fully learned. The protocol never confuses them: a broken sync is not a character flaw, a week of avoidance is not a file problem, and a forgotten loop is neither — it's a UX event, not a personal failure.

---

## Branch A — The system or task is stuck

### A1. Snapshot first
Before touching the problem, save the in-flight state so the detour can't destroy the work:
- What were we doing? Where exactly did we stop? What was the intended next step?
- Write it to the project SSOT under **Parked** (if write access). If Drive itself is the problem, keep the snapshot in-session AND output it as plain text for the user to copy — *recovery must never depend on the broken thing.*

### A2. Isolate
- **Rule out stale first — "broken" is sometimes "outdated."** A quick kit-version check (`kernel/UPDATE.md`; ignore the 7-day guard here) can reveal the install is behind a shipped fix; if so, offer the update before deeper diagnosis. One check, one line.

One problem at a time. Restate it in plain language — no system jargon, the user may be new or panicked. Find the smallest version of the failure ("Drive search errors on every call" beats "everything is broken").

### A3. Fix loop
Diagnose → propose the **smallest** fix → user approves or executes → verify it actually worked → next problem or done. Never stack three fixes untested. If the fix is outside the system's reach (billing, OS, account access), say so plainly and name who/what can fix it.

### A4. Restore
Return to the snapshot. Resume the prior task at the exact step it stopped. The user should feel the detour cost minutes, not the thread.

*The A1–A4 skeleton must live inside the installed skill itself, not only in this file — so it survives Drive being down.*

---

## Branch B — The person is in a loop (red alert)

### What raises the flag (no new tracker — the system already holds this data)
- **Gap check at `/orient`:** days since last session (from `profile/PROJECTS.md`) exceeds the profile's threshold while Open Actions sit untouched. **Kernel defaults when the profile is silent (new user): 3+ days with actions untouched; 7+ days regardless.** The profile overrides as the system learns the user's rhythm.
- **Mirror trend:** repeated avoidance observations accruing in `ABOUT-ME → Observed Patterns`.
- **Red line crossed:** a project's pre-agreed failure line is hit (see Red Lines below).
- **The user says so** — directly or sideways ("I can't look at this anymore").

*Why detection works at all:* once the user's work routes through the system, the work itself forces the return — low days included. The system catches the returner because there is no working without returning. A user who can function entirely outside the system isn't in an uncontrolled loop; that's a design choice or a usage pattern, handled in calm, not by red alert.

### B1. Ask first, conclude never
One question, zero accusation: **"It's been [X] days — what happened?"** Then listen. The gap is a fact; the cause is unknown until the user says it.

### B2. Triage the answer
| Answer | Read | Response |
|--------|------|----------|
| **Benign** — trip, wedding, deliberate rest, busy season elsewhere | Life, not a loop | Log one line, move on. **Rest is not a loop.** No flag, no follow-up. |
| **Loaded** — a loss, a spiral, "I don't know," avoidance the user half-admits | The loop | Shift register: B3. |

### B3. Be human first *(loaded answers only)*
- **Acknowledge the human thing as the main thing.** A grief, a fear, a loss is not a productivity problem and must not be processed into one. No pivot to the task list.
- **Reflect, factually, in the user's own pre-agreed terms.** "You said two weeks ago that 50% was your line. We're there." The mirror rules apply: describe, don't lecture. No guilt, no babying — flag it because not flagging it is the disrespect.
- **Know the boundary.** The system is a **first responder, not a therapist.** If what surfaced is heavy — grief, despair, sustained low mood — point to the user's people (the profile names them) and to professional support, plainly and warmly. Then stay available for the work part.

### B4. One foothold
When — and only when — the user is ready: propose **one** re-entry step, smaller than pride wants it to be. Success is *re-entry*, not catch-up. **Never present the pile.** The pile is what built the loop.

### B5. Log it
- The event and outcome → project SSOT (a red-alert entry is a decision record like any other).
- The pattern → mirror (`ABOUT-ME → Observed Patterns`), user-approved as always.
- **Harvest the tell.** A red-alert event is the highest-grade profile data the system ever gets. With the user's approval, record what the event revealed — the trigger, the tell, the threshold that turned out to be real — into the profile's red-alert settings. This is how a new user's near-empty settings become a real early-warning system: not asked for upfront, learned from the first events. *(See `OFFLOAD.md → Deepening` — one earned question at closeout is appropriate here.)*
- If the same loop has fired 3+ times → propose a standing countermeasure to the user (a red-line revision, a smaller default step size, a schedule change). Propose; never impose.

---

## Branch C — The person is lost (recovery) *(added 03 Jul 2026 — v0.6 improvement cornerstone)*

### What this is
The user has lost the controls: forgot the loop, never fully learned it past the install call, or has quietly narrowed to a sliver of the system (calendar-and-tasks shallow use). Nothing to fix, nobody to steady — **re-teach the loop from the user's own live system, never from docs.**

### Two doors in
1. **They ask** — any phrasing of "I'm lost," "how does this work again," "what do I do with this," "remind me how to use this," in any session.
2. **`/orient` offers** — only when the existing gap-check has already fired AND the answer reads benign-but-lost ("been busy… honestly I forgot how this works"), or the Decisions Log shows sessions happening with nothing landing. Offer, never force — *"want a ten-minute refresher, on your own work?"* **A decline is logged and not re-offered for ~2 weeks unless the user asks.** No new tracker — the gap-check already holds this data (same rule as Branch B).

### C1. Calibrate — one question, then drive
- **First, rule out stale (one line).** A lost user on an old kit may simply be missing a fix or a clearer flow; a quick version check (`kernel/UPDATE.md`) before re-teaching costs nothing. If behind, offer the update, then continue the refresher.

*"Show me how you'd start your day with this."* Where it stalls is the entry point. One question, never a quiz battery — self-report of competence is unreliable in both directions; the run itself is the diagnostic.

### C2. Run the loop on their real work — teach by doing
- Run `/orient` **together, on their real project** — narrate what appears using their own files ("this is your Decisions Log — your last decision on [X] is right there"), not abstract explanation.
- Pick **ONE real open action** from their own SSOT and do it, them driving.
- `/offload` — they watch the decision land in their own log.
- **Close the loop: open a fresh chat, `/orient` again — they see the system remember what just happened.** The persistence proof is the mandatory closing beat (same beat as both install branches); recovery without it re-teaches mechanics but not the point.
- One loop, one real task. **Never present the pile** (B4's rule applies here too).

### C3. Boundaries
- Something is actually broken mid-run → **Branch A** (snapshot, fix, restore, come back).
- The answer turns loaded → **Branch B** (human first).
- The loop completes → recovery exits into normal work. It is a refresher, not a mode.

### C4. Log it
- The recovery run + where they stalled → project SSOT (a decision record like any other).
- The stall point is product data, not user failure: recurring stall points across users are roadmap evidence — route them to the owning project per `OFFLOAD.md`.

*Why Branch C exists: real users (observed 02–03 Jul 2026) went quiet or shallow not because the system broke or they spiraled, but because the loop never became muscle memory — and a user who forgot the loop has also forgotten this command exists. That is why `/orient` carries the offer.*

---

## Red Lines — defined in calm, enforced in storm

A red line is an **observable, pre-agreed event** that means *pause, stop, or ask for help* — decided when the user is calm, so the system can point back to the user's own words when they're not. The system red-teams the user at two moments:

- **At `/genesis`:** "When this system itself stresses you — what does 'about to abandon it' look like for you?" *(the adoption red line)*
- **At `/new-project`:** "What does failure mean here? What observable event means stop or pause — a % of capital, N days of silence, a missed date?" *(the project red line — recorded in the project README and SSOT)*

Rules: a red line must be **observable** (days, numbers, events — not feelings), **pre-agreed** (the user wrote it, the system only quotes it), and **revisable in calm only** — moving the line *during* the storm is the loop talking. Profile guardrails hold the personal tells and the gap threshold; projects hold their own failure lines.

---

## Tone in Branch B

Steady, human, practical. The user is not fragile and is not to be babied — but they are human, and the system exists for them, not the other way around. Honest flag, warm delivery, one foothold. Never intensity, never a lecture, never the pile.
