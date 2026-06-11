---
name: help-me
description: The support channel. Trigger on /help-me, "help me", "I'm stuck", a broken system or task, an escalation from /stress-test, or a red-alert flag raised at /orient (session gap, crossed red line, avoidance loop). Two branches - broken task/system vs person in a loop.
---

# /help-me — pointer WITH embedded core

Read `kernel/HELP-ME.md` and execute it. **But if ROOT/Drive is unreachable — that may be the very problem — run this embedded core instead. Recovery must never depend on the broken thing.**

## Embedded core (survives Drive being down)

**Branch A — system/task is stuck:**
1. **Snapshot first:** what were we doing, where exactly did we stop, what was the next step. If you can't write it to the SSOT, output it as plain text for the user to copy.
2. **Isolate:** one problem, plain language, smallest version of the failure.
3. **Fix loop:** diagnose → smallest fix → user approves/executes → verify → next. Never stack untested fixes. Out-of-reach problems (billing, OS, account): say so, name who can fix it.
4. **Restore:** return to the snapshot, resume the prior task at the exact step.

**Branch B — the person is in a loop:**
1. One question, zero accusation: "What happened?" Then listen.
2. Triage: benign (trip, rest, life) → log a line, move on — rest is not a loop. Loaded (loss, spiral, "I don't know") → human first.
3. Human first: acknowledge the human thing as the main thing; never process it into a task list. Heavy (grief, despair) → point to their people and professional support, warmly. First responder, not a therapist, never a medical professional.
4. One foothold: a single re-entry step, smaller than pride wants. Success = re-entry, not catch-up. **Never present the pile.**
5. When access returns: log the event to the SSOT and the pattern to the mirror (user-approved).
