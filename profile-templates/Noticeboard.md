# Noticeboard.md — Cross-project noticeboard
*Part of: ROOT / profile*
*Scope: Profile. The shared cross-project channel. Pinned by `/offload`, drained by `/orient` Step 2.5. Stays short by design — the substance always lives in a project's Decisions Log, never here.*

> **What this is (plain version).** Every Claude session can see only two folders: ROOT and the one project it's working in. Projects can't see each other — locked rooms. ROOT is the one room every session has a key to. So to pass an action from project A to project B, you don't slip a note under B's door — you pin a B-tagged note on this shared board, and B picks it up at its next `/orient`.

> **How a note moves.** `/offload` in the source project pins a line here, tagged for the project that must act on it. The target project's `/orient` (Step 2.5) reads the board, appends any notes tagged for it into its own Decisions Log's Open Actions (`[from Noticeboard]`), confirms the write, then hard-deletes the line. The audit trail lives in the SSOT — the board stays short.

> **Format:** `- [DD Mon YYYY] #project-tag — note — source — status` — one note per line, **one tag per line** (a note for several projects = one line per project). Tags come from the `Noticeboard tag` column in `profile/PROJECTS.md` — never invent one.

---

## Session beacons
*Live sessions. `/orient` Step 6 posts its line here; `/offload` clears it. **Advisory only — a beacon never blocks work.** Another project's beacon means: re-read before every shared-ROOT-file write, prefer append, never delete a line you don't own. A beacon older than **12 h is presumed dead** (a session that crashed without `/offload`) and ignored — never delete another project's, stale or not. The drain never touches this section.*

*(none — no session currently open)*

## Standing notes
*Read by every session; the drain must NOT delete these. Only the project that pinned one retires it.*

*(none yet)*

## Open notes

*(none yet — the first time one project needs another to act, `/offload` pins it here)*
