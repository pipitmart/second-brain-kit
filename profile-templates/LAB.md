# LAB.md — System Lab Log
*Part of: ROOT / profile*
*Scope: Profile. The cross-project running log for system / infrastructure investigations — issues that span projects (Drive sync, Cowork mounts, the write path, connectors, platform quirks) and belong to no single project's Decisions Log. Loaded **on demand** when doing infra work; `STACK.md` carries the settled one-paragraph summary + a pointer here.*

> **Why this exists.** ROOT is the only folder mounted into *every* Cowork project, so a cross-project investigation must live in ROOT to be reachable from any project. Division of labour: **STACK = settled facts** (reference, loaded every session), **LAB = the active investigation** (process, loaded on demand), **project Decisions Logs = project-scoped work**. When an investigation produces a durable fact, copy the one-line settled version into STACK and leave the detail here.

> **How to use.** One section per investigation, newest first. Each carries: **Status** (OPEN · cause-found · RESOLVED), the **question**, **hypotheses** (mark each tested / killed / leading, with the evidence), the **verdict**, and any **SOP or action** it produced. **Supersede, never delete** — killed hypotheses stay visible so they are not re-litigated.

---

## Investigations

### [Short title of the issue] — [OPEN · CAUSE FOUND · RESOLVED]
*Opened [date]. [Status note — e.g. cause confirmed [date]; fix pending.]*

**Question:** [What's broken or unexplained, and what would count as resolved.]

**Symptom / evidence:**
- [What was observed — be specific: error strings, which folders/machines/sessions, what still works.]

**Hypotheses:**
- **"[hypothesis]"** — TESTED / KILLED / LEADING ([the evidence that moved it]).

**Verdict:** [The confirmed cause, once known — or "still open" with the next discriminating test.]

**SOP / action:** [The repeatable fix or the next step, and who owns it.]

---

*(Add new investigations above this line, newest first.)*
