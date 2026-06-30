# REVIEW.md — Independent Review Gate
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic. Runs on demand inside a project session, and as a pre-ship gate before any kit/git release.*
*Triggered by: `/review`*

> Purpose: red-team an artifact against a fixed bar before it ships or is trusted. The maker can't reliably check its own work — `/review` is the independent pass that grounds every claim against real state, reports what it could and couldn't verify, and flags problems for a human to fix. It never rewrites. Written to be run by any model.

---

## What /review is (and is not)

- **Is:** an on-demand critic + a pre-ship release gate. It reads a target, checks it against ground truth, and returns severity-ranked findings.
- **Is not:** an auto-fixer. It flags; the human (or a follow-up build) decides and edits. *(detect + surface, human decides — never auto-mutate; same stance as §10 and §12.)*
- **Relation to the kernel's standards:** §11 (data integrity) and §12 (action integrity) are the *standards*; `/review` is the *tool that checks them*. If the planned loop maker/checker (critic) work is later codified, it converges here — **don't build a second, parallel critic.**
- **Relation to `/orient`:** orient runs a light consistency check at load (Steps 3–4). `/review` is the deeper, deliberate pass you invoke when something is about to ship or be trusted.
- **Profile-layer counterpart:** a user's overlay may run a cross-project review (e.g. COS at `/close`). That is profile, not kernel. Kernel `/review` works inside one project (ROOT + the mounted project).

---

## The bar — five checks

Every finding ties to one of these. Each check is **grounded** — verified against real state, never against the document's own claims.

1. **Factual accuracy** — every factual claim checked against ground truth: does the file/folder exist (Read/Glob), does the value match the sibling SSOT / PROFILE / git, are dates / IDs / counts real? A claim that can't be grounded is reported **UNVERIFIED**, never assumed true.
2. **Staleness** — was-true-once, not-now: completed work still written as open, superseded version numbers, "current" pointers that moved, totals / counts that drifted, last-updated stamps behind reality.
3. **Consistency** — internal (no self-contradiction) + cross-doc (agrees with the SSOT, CHANGELOG, PROFILE, README). The same fact in two places must match.
4. **Dangling references / stranded to-dos** — pointers to files or sections that don't exist, "see X" where X is gone, open actions with no owner or status, leftover TODO / placeholder markers.
5. **Clarity** — communication: ambiguity, unexplained jargon / acronyms, convolution. Read through the intended reader's lens (for shipped kit files: the non-technical ICP).

---

## Grounding discipline (the core rule)

A check is **passed only if grounded against real state.** If the target asserts "X is done" or "file Y exists," `/review` confirms it directly — it does not take the document's word. Anything it cannot reach (an external action, an off-Drive repo, another machine) is reported **UNVERIFIED**, with the reason. This is why `/review` exists: it is immune to the artifact's own confidence.

---

## Output

Never a blanket "all clear." Always three parts:

1. **Coverage** — what was reviewed, what it was checked against, and **what could not be verified and why** (the unverified surface is always named).
2. **Findings** — severity-ranked; each carries: location · what's wrong · the grounded evidence · a suggested fix (flagged, not applied).
   - 🔴 **Blocking** — factual error, broken reference, data/action-integrity breach. Holds a ship.
   - 🟡 **Should-fix** — staleness, inconsistency, missing owner/status.
   - 🔵 **Polish** — clarity, wording, minor.
3. **Verdict** — for a pre-ship gate: **ship / hold** (hold if any 🔴 stands). For on-demand: the findings stand on their own. *Advisory — the human decides to act or override, same as every other surface; `/review` never blocks by force, it reports.*

**Output discipline:** lead with the highest-severity must-fix as the one foothold; group the rest by severity; never bury the blocker under a pile of 🔵 nits. Enumerate fully — but ordered so the first thing read is the thing that matters.

---

## Two modes

- **On-demand:** `/review [target]` — a named doc, this session's edits, or "the kit." Returns coverage + findings.
- **Pre-ship gate:** before any git / kit release, run `/review` over the release surface (kernel files + CHANGELOG + skills). A 🔴 holds the release until cleared or consciously overridden. *The maker cannot reliably check its own work — this gate is the point.*

---

## How to run it

1. **Identify the target + its ground truth** — the artifact, and what reality to check it against (Drive files via Read/Glob, the project SSOT, CHANGELOG, PROFILE, git state if reachable, sibling SSOTs).
2. **Run the five checks**, grounding each. Mark every claim Verified / Stale / Wrong / Unverified.
3. **Rank findings** by severity; attach grounded evidence + a suggested fix to each.
4. **Write the coverage statement** — including the unverified surface.
5. **Report. Do not edit the target.** Hand findings to the human (or the follow-up build) to action.

---

*`/review` is the independent reviewer the maker can't be for its own work. It checks against reality, names what it couldn't reach, and never edits — it hands you a grounded findings list and a verdict you're free to act on or override.*
