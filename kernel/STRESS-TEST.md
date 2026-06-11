# STRESS-TEST.md — Instance Setup & Repair
*Part of: ROOT v2.2 / kernel*
*Version: 1.1 | 11 Jun 2026*
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
Install the thin-pointer skills from ROOT `skills/` via Claude Settings → Capabilities. Each skill just reads its kernel `.md`. Confirm each is active.

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
| Folder mounted (write) | edit a ROOT file in place | edit succeeds |
| Drive read/search | search ROOT for `CLAUDE-KERNEL` | returns, not error |
| Other MCPs (per STACK) | one cheap call each | returns, not error |

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
