---
name: new-project
description: Start a new project from a rough objective. Trigger ONLY on /new-project or an explicit request to set up a new project. NOT for session starts (/orient) or machine setup (/stress-test).
---

# /new-project — thin pointer

The logic lives in the kernel, not here.

1. **Locate ROOT:** among the mounted folders, find the one containing `kernel/CLAUDE-KERNEL.md`.
2. **Read `kernel/NEW-PROJECT.md` fully** (it uses `kernel/SSOT-TEMPLATE.md` and `profile/PROFILE.md`).
3. **Execute it exactly as written, no skipped phases** — access gate, objective clarification (including the red line), build in place, register in `profile/PROJECTS.md`, verify with /orient.
