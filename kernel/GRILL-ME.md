# GRILL-ME.md — Interview / Design-Tree Stress-Test
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic. Runs on demand inside a project session, before committing to a plan or build.*
*Triggered by: `/grill-me`, "grill me on this," "interview me on this plan," "stress-test this before we build," or any explicit request to be interviewed about a design before building it.*

> Purpose: reach a genuinely shared understanding of a plan before building it, by interviewing the user branch-by-branch down the design tree instead of accepting the first framing. Adapted from Matt Pocock's `grilling` skill (github.com/mattpocock/skills) — credited origin, reshaped to this kernel's conventions. Written to be run by any model.

---

## What /grill-me is (and is not)

- **Is:** a structured interview that walks the design tree of a plan one decision at a time, each carrying Claude's own recommended answer, until every dependency is resolved and both sides hold the same picture.
- **Is not:** a generic Q&A dump, a checklist to blast through in one message, or a substitute for `/review` — `/review` red-teams something already built or written; `/grill-me` runs *before* building, to shape what gets built in the first place.
- **Relation to the kernel's other stances:** same "detect + surface, human decides" posture as §10/§12 — Claude proposes a reasoned default, the human confirms or overrides. Never the reverse, and never a bare open question with no opinion attached.

---

## The method

1. **One question at a time.** Never bundle multiple open decisions into one message — asking several at once is bewildering and buries the dependency order. Wait for the answer before asking the next.
2. **Resolve dependencies in order.** Before asking, identify which open decisions block which. Ask the blocking one first, so later questions build on a settled answer instead of being asked into a vacuum.
3. **Every question carries a recommended answer**, reasoned from what's actually knowable — existing SSOT/kernel/profile content, an established convention, or a first-principles trade-off — never a bare "what do you want?" A question with no opinion attached is the interviewer doing no work.
4. **Explore before asking.** If a question is answerable by reading the codebase/Drive/SSOT — a file exists or doesn't, a convention is already set, a prior decision already covers it — go read it and state the finding. Don't spend a turn asking something you could have looked up.
5. **Don't pre-draft the rest of the tree.** A "no," a third option, or a scope change on this answer can reshape every question after it — decide the next question only once the current one is actually settled.
6. **Stop when the tree is resolved, not when you run out of curiosity.** Once every dependency that actually blocks building is settled, say so plainly and offer to close out (write the spec, hand to `/offload`, or start building) rather than manufacturing further branches for their own sake — low-stakes, reversible edges don't need the same scrutiny as load-bearing ones.

---

## Output discipline

- **Recap the full settled spec before it's written anywhere permanent** — the SSOT, the kernel, a build. This is the last chance to catch a wrong turn before it becomes record.
- **Name real tensions plainly.** Where the interview surfaces a conflict against something already decided — a kernel principle, a standing SSOT decision, an existing convention — say so directly and let the human resolve it. Don't silently comply with something that contradicts an established system rule, and don't silently overrule the human either.
- **Stress-test the frame, not just the details.** The highest-leverage pushback is often on scope or ownership (where does this live, whose job is it) before a single implementation branch is walked — a wrong frame invalidates every downstream answer.

---

## How to run it

1. **Identify the plan and its scope boundary** — what's genuinely open to design here vs already decided elsewhere. Check `PROJECTS.md` / the active SSOT / the kernel before assuming something is undecided; a prior decision that already covers a branch means that branch is a finding, not a question.
2. **Ask the first, most dependency-blocking question**, with a recommended answer.
3. **On each answer:** update the working picture, resolve any sub-branch it immediately unlocks, then ask the next single question.
4. **When the tree is resolved:** recap the full spec, then ask whether to build now or log it as a build-ready spec (per kernel §10 — an experiment or spec is either parked with a clear next step or built; it never sits ambiguous).

---

*`/grill-me` exists because the first framing of a plan is rarely the right one, and a plan the human hasn't been walked through branch-by-branch is a plan they'll only find the holes in after it's already built.*
