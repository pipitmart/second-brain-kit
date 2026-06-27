# TEACHING-LOOP.md — How the System Teaches
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic. The reusable scaffold for teaching the user anything — a skill, a concept, or the system itself.*
*Used by: any session where the user is learning; `/genesis` and onboarding (the subject is the system); `/goal` (shows the checklist).*

> Two learning loops run in this system and must not be confused. **The system learns the user** — that's the living mirror (`OFFLOAD.md → Observed patterns`). **The user learns** — that's this file. They are separate layers that intersect in one place: when the subject being taught *is the system itself*. Both must coexist; neither replaces the other.

---

## The standard (default — the profile overrides)

A topic is **mastered** when the user can do BOTH:
1. **Explain it in their own words** — not echoing the words Claude used.
2. **Generate a fresh example** — their own, non-trivial, correct.

The second test is the real one: explanation can be parroting; a self-generated example is proof. A wrong, partial, or reskinned example = not learned — stay on it, do not advance. "Makes sense" is never proof.

`profile/LEARNING-ME.md` holds the user's personal bar, patterns, and depth preferences. If it's empty (new user), use this default and let the mirror refine it.

---

## The loop (per topic)

1. **Probe first.** Before explaining anything, ask the user to restate their current understanding. This reveals where they actually are. Build from what they hold; don't re-teach it.
2. **Decompose.** Small, sequenced steps. Never present the whole mountain — one idea at a time.
3. **Calibrate depth on demand.** `/eli5` (simplest, analogies) · `/eli14` (curious teenager) · `/eli1` (smart first-week intern, no context). Match exactly.
4. **Verify to standard** — explain back + fresh example. Both, every gate.
5. **Quiz to confirm, never to rush.** Open-ended or multiple choice; vary the correct answer's position; never reveal until the user submits. 
6. **Gate advancement.** Next step only when the current one meets the standard.
7. **Keep a running checklist** — what must be understood vs what's demonstrably mastered. On `/goal`: restate the session goal and show it. A teaching session doesn't end until the list is verified or explicitly parked.

**Affirmation is tied to demonstrated understanding, not effort.** Honest "not yet" is the respectful move.

## The three layers of any subject

Cover all three, and the *whys* at each — don't rush to layer 2 before layer 1 is genuinely held:
1. **The problem** — what it is, why it exists, what the options were.
2. **The solution** — why it was resolved this way, the design decisions, the edge cases.
3. **The broader context** — why it matters, what it impacts downstream.

## Watch-fors (generic; profile adds personal ones)

- **Convolution = exit risk.** If the explanation is getting tangled, simplify immediately — that's the moment users disengage.
- **"Looking fine" before solid.** Don't accept a nod. The fresh example is the honest check.
- **Understand ≠ do.** When a topic moves from knowing to doing, make the first action tiny and unambiguous.

---

## The intersection: teaching the system itself

Onboarding (genesis, first calls, new-recipient handoffs) **is a teaching session whose subject is the system.** Apply this loop with that subject, and the mastery standard becomes concrete:

> The user is onboarded when they can run a full day unaided — `/orient` → work → `/offload` — **and explain in their own words what each step is for.**

That is the operational meaning of "doesn't need a second call." Use the checklist: orient (what it checks, why), the SSOT (what goes in it, why one), offload (when to save, what the mirror does), help-me (when to call it). Quiz before the call ends. A user who can only *follow* the steps has memorized, not mastered — they'll abandon at the first deviation.

Meanwhile the other loop is already running: from the first genesis question, the mirror is learning the user. The system and the user are learning each other simultaneously — that's not a coincidence, it's the design.
