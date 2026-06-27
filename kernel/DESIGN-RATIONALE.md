# DESIGN-RATIONALE.md — Why the system is built this way
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic. The settled "why" behind non-obvious architecture decisions.*

> **Purpose.** Users *will* ask "why is it done this way and not [some simpler-looking alternative]?" — and they'll ask in environments no one controls. This file holds the answers to the ones already reasoned through (often the hard way), so Claude can explain the decision instead of relitigating it or, worse, talking the user into a known mistake.
>
> **How to use it.** If a user proposes an alternative covered below, explain the trade-off from here and hold the line — these were paid for in real mistakes. If they raise something genuinely *not* covered, reason it openly, decide with them, and **add a new entry** (newest first) so the next person inherits the answer. Don't treat an entry as unchallengeable dogma: it records *why* a call was made, so a future change is made with eyes open, not by accident.

---

## Entries *(newest first)*

### Mounting model — one SB project + ROOT per Cowork project (the "two-folder" / "Option A" rule)

**The rule.** Each Cowork project connects **exactly two** Drive folders: that one SB project's folder, and `ROOT`. Nothing else.

**Why two folders at all.** Cowork scopes write access to the folders connected to a project. A normal working session writes to *both* layers: the project's own Decisions Log (in the project folder) **and** ROOT (`profile/PROJECTS.md` on every `/offload`, the living mirror in `profile/ABOUT-ME.md` occasionally). Connect only the project folder and the save half-fails silently — the index and mirror never update. So both must be connected. `/orient` Step 0 detects a missing ROOT mount and prompts, but the rule is to connect both up front, not lean on the self-heal.

**Why NOT mount one big container of everything ("Option B").** Tempting because it's one mount that covers ROOT + every project at once. Rejected because it **breaks per-project orientation**: `/orient` and `/offload` are built on the assumption that a session works *one* project. A session that can see every project has to guess which one is active and which Decisions Log to write to — and a wrong guess corrupts an SSOT, the one thing the whole system exists to protect. It also collapses the **privacy wall** (every project, and any non-SB folder in the container, becomes visible to every session) — fatal the moment the kit is shared. The failure is invisible until you have more than one project, which is exactly when people reach for the shortcut. *(With a single project and nothing else, container-mount and the two-folder rule are identical — the divergence, and the breakage, begin at project number two.)*

**The container is still recommended — for a different job.** Keeping `ROOT` + all project folders inside one parent folder (e.g. `Brain`) is good practice: on a busy or messy Drive it keeps everything **findable in one place**. That's an *organization* benefit, not a mounting strategy. The two are orthogonal axes: **organize with a container; mount per project.** Holding them apart is what lets you have both tidiness and clean scoping.

**Non-SB folders are a non-issue under this rule.** A Drive will always be a mix — some folders are SB projects (README + Decisions Log + `memory/`), some are just stuff (a wedding folder, a tax dump). You can't prevent that and shouldn't try. Because mounting is per-project, a non-SB folder simply never gets `ROOT` and never gets a Cowork SB project — so the structured/unstructured mix never has to be reconciled. It can even live inside the same container; it makes no difference.

*Decided 13 Jun 2026, after adopting a container on a real multi-project Drive surfaced the mixed structured/unstructured reality and the "which project is active?" ambiguity. Supersedes the earlier framing of container-mount as an "advanced opt-in alternative" — it is a documented dead-end, kept here with its reason so it isn't rediscovered.*
