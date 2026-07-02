# PROFILE-GENESIS.md — Building a New User's Profile
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic. **Live** runs once per user — when `profile/` is empty or a new person takes over a system. **Demo** is repeatable and touches nothing real.*
*Triggered by: `/genesis`, or automatically when `/stress-test` finds an empty profile.*

> Builds a **sufficient** profile fast — not a complete one. The living mirror (`OFFLOAD.md → Observed patterns`) corrects and deepens the profile from real behavior over time, so genesis only has to get the system *started well*. Principle: **ask little, observe much.** Day-one answers are self-report — the least reliable data the system will ever hold. Never mistake them for the person.

---

## Phase 0 — Why this exists *(60 seconds, before any questions, both branches)*

Before asking the user anything, answer the question **they** are silently asking: *"why am I spending time on this?"* Plainly, in two or three sentences, no jargon:

> Claude remembers you now — a general sense of who you are, auto-summarized across chats. But that memory is a black box: you can't inspect it, point it at a specific project, or trust it with what you decided and why. **It's also stuck to whatever's in that auto-summary** — no structured record, nothing that follows you the same way to another computer. **Decisions still scatter** — what you agreed last week lives in some chat you'll never find. This system fixes that: it puts your context in *your* Drive — structured, auditable, yours to control — so it's the same brain on every machine, and every project gets one trusted page where decisions go to *live*, not die.

If the user asks "isn't this just a skill?" (they will): *the skills are thin switches — the value is the memory and structure they switch on. Remove the Drive layer and the skills have nothing to point at.* One-line positioning if useful: **"Cowork gives Claude hands for one session on one machine. This gives it memory and continuity across every session, machine, and project."**

**Then offer the fork — one simple question:**

> *"Two ways to start. **Demo** — I show you the whole thing working on sample data, takes ten minutes, nothing you do is permanent. **Live** — we build your real profile now, about fifteen minutes of honest questions. Most people like the Demo first. Which one?"*

- **Demo** → run the Demo branch below. Repeatable, disposable, never consumes the real once-per-user run.
- **Live** → run Phases 1–5 below (the original genesis). Once per user.
- If `profile/` already has content, there is no fork — genesis already ran; only offer Demo (or profile repair via `/help-me`).

---

## The DEMO branch — taste first, zero risk

**What it is:** a guided ten-minute run of the real system on a fictional persona, so a brand-new user *feels* the value before investing anything. It is also a quiet diagnostic: every beat exercises one fragile piece of their setup (skills fire, ROOT readable, write path works) while Claude is watching — failures surface here, guided, not three days later, alone.

**The one moment that matters:** Claude picking up a *specific, structured decision* across a chat boundary — not a vague auto-summary, but the exact thing you decided and why, ready to act on. That is what plain Claude's built-in memory visibly cannot do. Every beat exists to set up that moment; never cut it for time.

### Hard guardrails (sandbox by location)

- Demo writes **only** inside `ROOT/profile-demo/` — a folder created for the demo and deleted after it. It never touches `profile/` (which stays **empty**, so stress-test's empty-profile trigger and the real Live run remain intact) and never touches any real project.
- The demo project lives **inside** `profile-demo/` — a deliberate, explicit exception to the projects-outside-ROOT rule, so the whole sandbox is one deletable folder that can never be mistaken for real work.
- **Demo-mode detection** (used here and by `/orient` + `/offload`): `profile/` empty or missing **and** `profile-demo/` exists → demo mode; show the banner `🟡 DEMO MODE — sample data, nothing here is yours yet` on every orient/offload. If `profile/` has real content and `profile-demo/` still exists, the real profile wins — flag the leftover demo folder and offer to delete it.
- The user types every command themselves. Claude never fakes a step, never stages a fake error.

### The six beats

1. **Set up the sandbox** *(Claude, ~1 min)*: copy `profile-templates/demo/` → `ROOT/profile-demo/` with the native file tools. Tell the user what was just created: a sample person ("Sam, runs a small online homeware shop") with a project mid-flight.
2. **Cold open** — user types `/orient`. It loads in demo mode and Claude talks like it already knows Sam: who Sam is, the project, what's open, what was decided last week — *things this chat was never told.* Then name it: "Everything I just said came from files, not this conversation. That's the memory. In Live mode, that's you."
3. **One small decision** — Claude surfaces the demo project's open action (two packaging suppliers, pick one) and the user decides. Real work, fake stakes.
4. **Save it** — user types `/offload`. The decision lands in the demo Decisions Log; show them the actual line in the actual file — it's on their Drive, they can open it on their phone.
5. **The boundary** *(never cut this)* — tell the user: *"Close this chat. Open a brand-new one. Type `/orient`. If Claude asks for access to a folder, say yes — that's normal, it's the new chat picking up the key."* The new chat comes back knowing Sam's decision from beats 3–4. Name the aha plainly: "A normal Claude chat could never do that. This is the loop: **orient to load, offload to save.** That's the whole daily routine."
6. **The reflex + the exit** — have them type `/help-me` once, see the support channel exists, and tell them: anything ever looks broken — empty folder, command does nothing, weird error — that's the one thing to type. Then the exit ramp: *"Everything you saw was sample data. Run `/genesis` again and pick **Live** to build yours — the demo folder gets deleted when Live starts (or say 'delete the demo' any time)."*

**Cleanup:** Live's Phase 5 (and any user request) deletes `profile-demo/` entirely. If native delete isn't available on the machine, have the user delete the one folder in Drive — it's a single drag to trash.

---

## The LIVE branch — the original genesis (Phases 1–5)

## Definition of sufficient (the gate)

The profile is sufficient when Claude can, without guessing:

1. **Address the user in their style** — directness, pushback tolerance, formality.
2. **Name the user's core friction** — in words the user has confirmed.
3. **Name the first project** — and what a win on it looks like to the user.
4. **Operate practically** — timezone, date format, tools, machines.

Question count is irrelevant. When all four hold, stop asking and start working.

**Time-box: target ~15 minutes, hard stop 25.** Going long *is* the failure mode — a user whose first hour is an interrogation never comes back for the second.

---

## Phase 1 — The three openers

Not an open conversation — too slow, scope too broad. But **don't dump all three at once** either: it overwhelms a new user, and it breaks the one-topic-at-a-time rule below. Ask them **one at a time, in plain language, and offer a worked example with each** so the user knows the *kind* of answer you want. (Field-tested lesson: new users freeze on abstract questions and unfreeze the moment they hear a concrete example.) Frame the start simply: *"A few honest questions and we can start — one at a time."*

1. **"What do you do, and what are you trying to get done in the next 3 months?"**
   *Offer an example: "Like — 'I run a family-office advisory and I'm trying to land my first client.'"*
   *(→ context + goals; their language here also reveals communication style — register, length, precision.)*
2. **"What's the usual bottleneck — the thing that most often stops you getting the important work done?"**
   *Offer an example: "Like — 'I know what to put in the proposal but I never actually send it.'"*
   *(→ friction symptoms. Symptoms, not self-diagnosis — see Phase 2. Avoid idioms like "where work goes to die" — new users don't parse them.)*
3. **"Two weeks from now, what's one thing that — if it got done — would make you say 'okay, that was worth it'?"**
   *Offer an example: "Like — 'I got the proposal written and out the door.'"*
   *(→ the first win; this seeds the first project. Keep it to the one question. If "what recurring pain do you most want killed" doesn't surface naturally, ask it separately — never bolt two questions together.)*

## Phase 2 — Branch: uncover the friction

**Do not assume the user knows their own friction.** Most people don't introspect their way to it — they report symptoms. Claude's job is to diagnose from the symptoms in answer 2, probe one level deeper where the answer is thin, and then **restate the diagnosis for confirmation** in plain words (no idioms): *"So it sounds like things usually stall at [X] for you — is that right?"*

Friction taxonomy (diagnose into one or two, not all):

| Friction | Symptom sounds like | The system's lever |
|----------|--------------------|--------------------|
| **Start** | "I don't know where to begin" / plans never leave the page | decompose to a first tiny step |
| **Finish** | "I start everything, complete nothing" | open actions + visible progress |
| **Focus** | "too many things, all urgent" | one SSOT per project, parked list |
| **Trust** | "I write things down and never find them again" | one source of truth, orient ritual |
| **Consistency** | "great for a week, then it collapses" | offload ritual + small standing steps |

Adaptive rule: **go deeper or move on based on the answer.** A rich answer needs zero follow-ups; a thin one earns one or two targeted probes — never a battery. If the user confirms a restatement, it's diagnosed. If they push back, their correction is better data than the guess.

Close Phase 2 with **the adoption red line** (one question, kept concrete — the abstract phrasing confuses new users): *"One more, it helps me later: if you ever stop using this thing, what'll the reason most likely be — too slow, too confusing, or just not helping enough?"* Record the answer in the profile guardrails. (Red-line rules: `kernel/HELP-ME.md → Red Lines`.)

## Phase 3 — Prompt for data, scope the first win

If the user's goal or pain implies **data they already hold**, ask for it — don't design in the abstract:

> User says "I need to get my finances in order" → ask for the finance data (statements, tracker exports, whatever exists) → scope a concrete candidate project from the real data → **propose it: "Want to build this?"**

The user saying yes to a project scoped from their own data is the strongest possible start — it's the win-in-the-call. If no data exists, scope from the Phase 1 answers instead. Either way genesis ends by handing one candidate project to `/new-project`.

## Phase 4 — Quick-fire practicals (last, least effort)

Timezone · date format · tools they actually use (and which are connected) · machines. Factual, fast, no discussion. Fill `profile/STACK.md` skeleton.

## Phase 5 — Write, read back, tag

0. **If `profile-demo/` exists, delete it now** (or have the user drag it to trash) — Live begins clean; demo data must never linger beside a real profile.
1. Draft the profile files **live, during the call**, copying from `profile-templates/` into `profile/` and filling them in: `PROFILE.md` (style, guardrails, anchor), `ABOUT-ME.md` (thin baseline + empty Observed Patterns section), `PROJECTS.md` (empty registry), `STACK.md`, `LEARNING-ME.md` (kernel default; refined by observation later), `LAB.md` (copied **blank** — the system-lab log fills only when the first cross-project infra issue appears), `Noticeboard.md` (copied **blank** — the cross-project channel; fills the first time one project pins a note for another).
2. **Read the profile back**: "Here's what I heard — true?" Corrections in their words beat drafts in Claude's.
3. **Tag every genesis entry** `*(self-reported, [date])*`. The mirror's observed entries outrank self-report: **when observation contradicts a self-reported trait, observation wins** — propose the correction to the user.
4. **Set the expectation:** tell the user, plainly — *"Your profile starts thin on purpose. As we work, I'll occasionally ask one deeper question when something happens that's worth understanding. Answer honestly and the system gets better at working with you."* Genesis data is the lowest grade the system will ever hold; everything after is upgrade (`OFFLOAD.md → Deepening`).

Then run `/new-project` on the candidate from Phase 3. Genesis is done when the user has a profile *and* a live first project in the same sitting.

---

## Rules

- One topic at a time. Never present the full scope of the interview upfront.
- Plain language throughout — no system jargon during genesis; the user hasn't read the glossary yet.
- The profile belongs to the user. Nothing in it ships anywhere; the kernel/profile split is also a privacy wall.
- **Live** genesis is once. After it, the profile grows only by the mirror loop (observed) or the user's own edits (deliberate). **Demo** is repeatable and disposable — it can never consume, overwrite, or contaminate the Live run.
