---
name: stress-test
description: Instance setup and repair. Trigger ONLY on /stress-test, "stress test", a new machine, a fresh Claude install, or when something is broken (MCPs failing, files not found, context stale). NOT for daily session starts — that's /orient.
---

# /stress-test — thin pointer

The logic lives in the kernel, not here.

1. **Locate ROOT:** among the mounted folders, find the one containing `kernel/CLAUDE-KERNEL.md`. If nothing is mounted yet (fresh machine), guide the user: install Google Drive desktop → set the ROOT folder Available offline → mount it into Cowork. Then continue.
2. **Read `kernel/STRESS-TEST.md` fully.**
3. **Execute it exactly as written.** If `profile/` is empty, run `kernel/PROFILE-GENESIS.md` first. On any Gate 0 failure, diagnose and escalate to /help-me.
