# FEEDBACK.md — User → Maker feedback loop
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic — ships unchanged to every user. The channel an installed SB uses to send feature requests and problems back to the maker.*
*Triggered by: `/feedback`*

> Purpose: let any user send a **structured** feature request or problem about SB itself back to the maker — without typing a form. Claude does the articulating; the user reviews one pre-filled link and submits. Responses land in the maker's private sheet, never visible to other users. This is how the maker learns what to build next from real use, instead of guessing.

---

## When it fires

- `/feedback`, or the user expresses a wish/complaint **about SB itself** ("I wish SB could…", "this is annoying", "can it…").
- This goes to the **maker**, not into the user's own project — confirm intent in one line before sending.

---

## What Claude does

1. **Gather two things** (pull from the conversation if already said; otherwise ask once, briefly — never an interview):
   - **The request** — one line.
   - **The why / what they'd do with it** — the use-case. This is the part that makes the request triageable; don't skip it.
2. **Fill the rest automatically:**
   - **User + version** — the user's name from `profile/PROFILE.md` + the current SB version (from `CHANGELOG.md`). The maker assigns serials on their end; the name is enough to identify.
   - **Urgency** — infer from tone, or ask one quick pick.
3. **Build the pre-filled link** — URL-encode each value into the template below (spaces → `+`; the urgency value must match an option string **exactly**).
4. **Hand the user ONE link.** They click, review, Submit. **Claude never submits for them** — the click is the consent + a human accuracy check.
5. **Confirm** it's sent. If the request also relates to the user's own work, offer to log a copy in their project SSOT.

---

## The form — SB Feedback form v0.1

Base form + field map:

| Field | entry ID | Filled with |
|---|---|---|
| User + SB version | `entry.1340849794` | `Name · vX.X` |
| Request (one line) | `entry.152593079` | the request |
| Why / use-case | `entry.815746520` | the why |
| Urgency | `entry.2022246250` | `Nice to have` \| `Would really help` \| `Blocking me` (exact match) |

Template (substitute the four `{…}`, URL-encoded):

```
https://docs.google.com/forms/d/e/1FAIpQLSfffGtHsdM-adzMULpaMSrGexW5XTm-0Fcbj_-fgNuFLt0oZQ/viewform?usp=pp_url&entry.1340849794={USER+VER}&entry.152593079={REQUEST}&entry.815746520={WHY}&entry.2022246250={URGENCY}
```

---

## Notes

- The form **collects no email** — users are identified only by what Claude fills, and no user can see another's responses. State this if a user asks where it goes.
- If the form URL or a field ID ever changes, **update it here only** — the `/feedback` skill is a thin pointer that reads this file live, so no repackage is needed for a URL change.
- **Maker side:** responses collect in the form's linked sheet; triage them into the project's Issues Log / backlog. The sheet is a **read-only inbox** — Claude reads responses but triage/decisions live in the markdown SSOT (the source of truth).
- **One-time form setup (maker):** the form must be **Published** and Responders set to **"Anyone with the link."** On a Google Workspace account this defaults to org-only, which silently blocks every external tester with "This document is not published." Fix before sharing.
