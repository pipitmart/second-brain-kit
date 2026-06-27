# STRESS-TEST.md — Instance Setup & Repair
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic. Run once per machine, or to repair a broken instance.*
*Triggered by: `/stress-test`*

> The instance's "restart and self-check." It sets up — or diagnoses and repairs — a machine so it's fully oriented before any work. It does **not** run daily; that's `/orient`.

---

## When to run
- New machine or fresh Claude install
- Something is broken — MCPs not responding, files not found, context stale

## When NOT to run
- Daily sessions → use `/orient`

---

## Step 1 — Install skills

The six `.skill` files live in ROOT `skills/`. Install each through the Claude **desktop app → Settings → Capabilities → Skills → upload**, then pick the `.skill` file. Walk the user **one at a time**: name the file, tell them exactly what to click, and confirm it shows as active before moving to the next. The six: `orient`, `offload`, `stress-test`, `new-project`, `genesis`, `help-me`.

- **Streaming gotcha (do this first):** on a streaming Drive the `.skill` files are online-only placeholders, so the upload dialog may show *nothing to select*. Have the user open the `skills/` folder in their file browser once (or right-click `skills/` → Available offline) so Drive downloads the real files before uploading.
- Each skill is a thin pointer — it just reads its kernel `.md`. If an upload is rejected, it's almost always the placeholder issue above, not a bad file.

## Step 2 — Load ROOT (full)
Read, in order:
- `kernel/CLAUDE-KERNEL.md`, `kernel/GLOSSARY.md`
- `profile/PROFILE.md`, `profile/ABOUT-ME.md` *(deep profile — loaded here, not daily)*, `profile/LEARNING-ME.md`, `profile/STACK.md`

The ROOT anchor ID is recorded in the profile.

**If `profile/` is empty or missing** (new user, fresh kit): run `kernel/PROFILE-GENESIS.md` before anything else — there is no system without a profile.

---

## Gate 0 — Environment check

Run every check. A listed MCP that errors on first call is **not** connected — a visual "connected" status is not proof.

| Check | How | Pass |
|-------|-----|------|
| Drive synced + folder offline | folder visible on disk | real files, not placeholders |
| **Both folders mounted (write)** | edit a ROOT file **and** a project file in place | both edits succeed |
| Drive read/search | search ROOT for `CLAUDE-KERNEL` | returns, not error |
| Other MCPs (per STACK) | one cheap call each | returns, not error |

> **Folder rename rule:** renaming a folder that is connected to a Cowork project breaks the connection — the folder appears empty in the next session. Drive preserves the folder ID through a rename, but Cowork holds the path. **Any time a connected folder is renamed, reconnect it in Cowork before the next session.** Same discipline as switching machines. If a folder reads empty and was recently renamed, reconnect first before diagnosing anything else.
>
> **Two-folder rule (the mounting model — not optional):** a working project needs **exactly two** folders connected to its Cowork project — **that project's folder AND ROOT** — and nothing else. Cowork scopes write access to connected folders, and a normal session writes to both (the project's Decisions Log + ROOT's `profile/PROJECTS.md` and mirror). Connecting only one is the most common setup miss. **One Cowork project = one SB project + ROOT.**
>
> **Don't mount a whole container of projects into one Cowork project.** It looks tempting (one mount covers everything), but it breaks per-project orientation — `/orient` and `/offload` assume a session works *one* project, and a session that can see every project has to guess which is active and where to write, risking the wrong SSOT. It also drops the privacy wall. *(A container folder holding ROOT + your projects is still a good idea — but for **finding** things on a messy Drive, not for mounting. Organize with a container; mount per project. Full reasoning: `kernel/DESIGN-RATIONALE.md`.)*

**On any Gate 0 failure: diagnose — name the failing check and the likely fix — then escalate to `/help-me` to walk the repair, and re-run.** Do not proceed on a broken environment.

---

## Orientation check — can Claude answer these unaided?

1. **Who is the user?** (profile loaded)
2. **What project/domain?** (project README)
3. **What's decided and open?** (Decisions Log)
4. **Where does everything live?** (ROOT anchor, project folder, key services)
5. **What tools are wired up?** (MCP IDs verified live, not assumed)
6. **What are the conventions?** (boot sequence, commands, file-discovery, write-path, SSOT)

---

## Score & report

| Result | Action |
|--------|--------|
| Gate 0 + all 6 | ✅ Instance ready → run `/orient` |
| Gate 0 + 5/6 | ⚠️ Name the gap; confirm or fill it |
| Gate 0 + ≤4/6 | ❌ Not ready; name every gap |
| Gate 0 fail | 🔴 Environment broken → diagnose → `/help-me` |

---

## After pass
Instance is ready. Not repeated unless `/stress-test` is run again or something breaks. Next: `/orient` for the active project.
