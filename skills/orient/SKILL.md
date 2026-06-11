---
name: orient
description: Session-start orientation. Trigger on /orient, "orient", or any session start in any project. Loads the system (kernel + profile), the active project, and surfaces open actions. Do NOT trigger for new-machine setup — that's /stress-test. Do NOT trigger for new project creation — that's /new-project.
---

# /orient — thin pointer

The logic lives in the kernel, not here.

1. **Locate ROOT:** among the mounted folders, find the one containing `kernel/CLAUDE-KERNEL.md`. If no folder is mounted, try the Drive MCP (read-only). If ROOT is unreachable both ways, say so explicitly and stop — do not guess.
2. **Read `kernel/ORIENT.md` fully.**
3. **Execute it exactly as written, all steps in order** — starting with Step 0 (access check).
