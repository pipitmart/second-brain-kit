---
name: offload
description: Save session work to the project's Decisions Log (SSOT). Trigger on /offload, "offload", "save session", "log today's progress", "update the vault", or at session end when unsaved decisions exist. Also governs save-on-decision behaviour throughout a session. Replaces v2.1's update-vault.
---

# /offload — thin pointer

The logic lives in the kernel, not here.

1. **Locate ROOT:** among the mounted folders, find the one containing `kernel/CLAUDE-KERNEL.md`.
2. **Read `kernel/OFFLOAD.md` fully.**
3. **Execute it exactly as written** — access gate, write to the SSOT in place, update the project index, mirror step (observed patterns + earned question if the session warrants one), then the three-part human closeout.
