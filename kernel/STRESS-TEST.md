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

The `.skill` files live in ROOT `skills/` — **the folder listing is the authoritative set; never assume a fixed count** (new skills ship with releases, and a hardcoded number in this file has gone stale before). List the folder, then install **every** `.skill` file present through the Claude **desktop app → Settings → Capabilities → Skills → upload**, then pick the `.skill` file. Walk the user **one at a time**: name the file, tell them exactly what to click, and confirm it shows as active before moving to the next.

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

## After Gate 0 — Project-defined instance setup (optional)

*(Runs here, not earlier: it needs the project folder confirmed mounted and its README readable — both proven by Gate 0.)* Some projects define their own per-instance setup in their README — most commonly a **local scheduled-task backstop** (a Cowork scheduled task, stored on this one machine, does not sync via Drive and does not follow the user to a new machine or a recreated project). If the active project's README has a section naming a scheduled-task or other local-only setup, offer to (re)create it now. **Prefer a calendar-anchored reminder over a local scheduled task where the project supports one** — a recurring calendar event is durable across machines and instances; a Cowork scheduled task is not and should be treated as an optional convenience layered on top, never the sole backstop.

---

## Gate 0.5 — Kit version check

`/stress-test` runs rarely, so it **always checks** (it ignores `/orient`'s 7-day guard) and it **always updates the shared stamp**:

- Read `profile/STACK.md`'s `SB kit:` stamp (absent → pre-0.6 install). Raw-fetch the repo `CHANGELOG.md`, parse the top released version (rule in `kernel/UPDATE.md`), and **write today into the stamp's `last update-check`**.
- Behind → surface it and offer the update run (`kernel/UPDATE.md`); a *safety*-tier gap is named as a health finding, not buried. Fetch failure → note it and proceed (a broken network is not a broken instance).
- Current → one green line ("kit is up to date, vX.Y").

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
