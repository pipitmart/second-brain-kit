# PROFILE-GENESIS.md — Building a New User's Profile
*Part of: ROOT v2.2 / kernel*
*Version: 1.0 | 11 Jun 2026*
*Scope: Kernel. Person-agnostic. Run once per user — when `profile/` is empty or a new person takes over a system.*
*Triggered by: `/genesis`, or automatically when `/stress-test` finds an empty profile.*

> Builds a **sufficient** profile fast — not a complete one. The living mirror (`OFFLOAD.md → Observed patterns`) corrects and deepens the profile from real behavior over time, so genesis only has to get the system *started well*. Principle: **ask little, observe much.** Day-one answers are self-report — the least reliable data the system will ever hold. Never mistake them for the person.

---

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

Not an open conversation — that takes too long and the scope is too broad. Open with exactly three questions, asked together, framed as: *"Answer these three honestly and we can start."*

1. **"What do you do, and what are you trying to get done in the next 90 days?"**
   *(→ context + goals; their language here also reveals communication style — register, length, precision.)*
2. **"Where does work go to die for you? What keeps not happening, restarting, or piling up?"**
   *(→ friction symptoms. Symptoms, not self-diagnosis — see Phase 2.)*
3. **"If this system gave you one win in the next two weeks, what would make you say 'this was worth it'? And what recurring pain would you most want it to kill?"**
   *(→ incentive + punishment. This seeds the first project.)*

## Phase 2 — Branch: uncover the friction

**Do not assume the user knows their own friction.** Most people don't introspect their way to it — they report symptoms. Claude's job is to diagnose from the symptoms in answer 2, probe one level deeper where the answer is thin, and then **restate the diagnosis for confirmation**: *"It sounds like your work dies at [X] — is that true?"*

Friction taxonomy (diagnose into one or two, not all):

| Friction | Symptom sounds like | The system's lever |
|----------|--------------------|--------------------|
| **Start** | "I don't know where to begin" / plans never leave the page | decompose to a first tiny step |
| **Finish** | "I start everything, complete nothing" | open actions + visible progress |
| **Focus** | "too many things, all urgent" | one SSOT per project, parked list |
| **Trust** | "I write things down and never find them again" | one source of truth, orient ritual |
| **Consistency** | "great for a week, then it collapses" | offload ritual + small standing steps |

Adaptive rule: **go deeper or move on based on the answer.** A rich answer needs zero follow-ups; a thin one earns one or two targeted probes — never a battery. If the user confirms a restatement, it's diagnosed. If they push back, their correction is better data than the guess.

Close Phase 2 with **the adoption red line** (one question): *"And when this system itself stresses you — what does 'about to abandon it' look like for you?"* Record the answer in the profile guardrails. (Red-line rules: `kernel/HELP-ME.md → Red Lines`.)

## Phase 3 — Prompt for data, scope the first win

If the user's goal or pain implies **data they already hold**, ask for it — don't design in the abstract:

> User says "I need to get my finances in order" → ask for the finance data (statements, tracker exports, whatever exists) → scope a concrete candidate project from the real data → **propose it: "Want to build this?"**

The user saying yes to a project scoped from their own data is the strongest possible start — it's the win-in-the-call. If no data exists, scope from the Phase 1 answers instead. Either way genesis ends by handing one candidate project to `/new-project`.

## Phase 4 — Quick-fire practicals (last, least effort)

Timezone · date format · tools they actually use (and which are connected) · machines. Factual, fast, no discussion. Fill `profile/STACK.md` skeleton.

## Phase 5 — Write, read back, tag

1. Draft the profile files **live, during the call**, copying from `profile-templates/` into `profile/` and filling them in: `PROFILE.md` (style, guardrails, anchor), `ABOUT-ME.md` (thin baseline + empty Observed Patterns section), `PROJECTS.md` (empty registry), `STACK.md`, `LEARNING-ME.md` (kernel default; refined by observation later).
2. **Read the profile back**: "Here's what I heard — true?" Corrections in their words beat drafts in Claude's.
3. **Tag every genesis entry** `*(self-reported, [date])*`. The mirror's observed entries outrank self-report: **when observation contradicts a self-reported trait, observation wins** — propose the correction to the user.
4. **Set the expectation:** tell the user, plainly — *"Your profile starts thin on purpose. As we work, I'll occasionally ask one deeper question when something happens that's worth understanding. Answer honestly and the system gets better at working with you."* Genesis data is the lowest grade the system will ever hold; everything after is upgrade (`OFFLOAD.md → Deepening`).

Then run `/new-project` on the candidate from Phase 3. Genesis is done when the user has a profile *and* a live first project in the same sitting.

---

## Rules

- One topic at a time. Never present the full scope of the interview upfront.
- Plain language throughout — no system jargon during genesis; the user hasn't read the glossary yet.
- The profile belongs to the user. Nothing in it ships anywhere; the kernel/profile split is also a privacy wall.
- Genesis is once. After it, the profile grows only by the mirror loop (observed) or the user's own edits (deliberate).
