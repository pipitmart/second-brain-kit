# FEEDBACK.md — User → Maker feedback loop
*Part of: ROOT / kernel*
*Scope: Kernel. Person-agnostic — ships unchanged to every user. The channel an installed SB uses to send feature requests and problems back to the maker.*
*Triggered by: `/feedback`*

> Purpose: let any user send a **structured** feature request or problem about SB itself back to the maker — without typing a form. Claude does the articulating; the user reviews one pre-filled link and submits. Responses land in the maker's private sheet, never visible to other users. This is how the maker learns what to build next from real use, instead of guessing.

---

## When it fires

- `/feedback`, or the user expresses a wish/complaint **about SB itself** ("I wish SB could…", "this is annoying", "can it…").
- **A `/help-me` Branch A close-out** — something broke, it got fixed, and the maker should know. See **Mode B** below.
- This goes to the **maker**, not into the user's own project — confirm intent in one line before sending.

**Two modes, same form.** **Mode A — a request** (a wish, a feature, an annoyance). **Mode B — a problem report** (something actually broke). The difference is only what fills the two free-text fields; the send discipline is identical.

---

## Mode A — a request

1. **Gather two things** (pull from the conversation if already said; otherwise ask once, briefly — never an interview):
   - **The request** — one line.
   - **The why / what they'd do with it** — the use-case. This is the part that makes the request triageable; don't skip it.
2. **Fill the rest automatically:**
   - **User + version** — the user's name from `profile/PROFILE.md` + the current SB version (from `CHANGELOG.md`). The maker assigns serials on their end; the name is enough to identify.
   - **Urgency** — infer from tone, or ask one quick pick.
3. **Build the pre-filled link** — URL-encode each value into the template below (spaces → `+`; the urgency value must match an option string **exactly**).
4. **Hand the user ONE link.** They click, review, Submit. **Claude never submits for them** — the click is the consent + a human accuracy check.
5. **Confirm** it's sent. If the request also relates to the user's own work, offer to log a copy in their project SSOT.
6. **Then — and only on a request that read as positive — consider the stars ask.** See *The stars ask* below.

---

## Mode B — a problem report *(the `/help-me` Branch A hand-off — 0.7-3)*

**Where it comes from:** `HELP-ME.md` Branch A ends by offering to send the problem to the maker. The user says yes; Claude arrives here **already holding the diagnosis** — it just fixed the thing. Nothing is re-asked.

1. **Draft the report from what this session actually observed** — never from what Claude assumes happened (kernel §11). Four parts, terse:
   - **Symptom** — what the user saw, in their words where possible.
   - **Repro** — the steps that produced it, as far as they're known. *If the trigger was never established, write `trigger not established` — do not invent a plausible one.*
   - **Environment** — OS, Drive mode (Streaming/Mirror), and the SB version from `CHANGELOG.md`. Observed only.
   - **Outcome** — fixed / worked around / still broken.
2. **Privacy scrub — binding, and it happens before the user ever sees the draft:**
   - **No file contents. No *user* file names. No project names. No client, company, or person names.** A user's project names leak their clients and ventures — describe structurally instead ("a project's Decisions Log," "one of the user's project folders").
   - **Kit file names ARE fine and usually essential** — `kernel/ORIENT.md`, `Step 2.5`, `feedback.skill`, `PRE-FLIGHT.md`. They are person-agnostic and ship identically to every install, so they carry no private information — and a repro that can't name the file it broke in is a report the maker cannot act on. *(The scrub protects the user's world, not the kit's.)*
   - No paths that carry a real name (`/Users/jane.doe/…` → `[user home]`).
   - When in doubt, cut it. A report that is too thin is a nuisance to the maker; a report that leaks a client's name is a breach of the promise the kit makes on its front page.
3. **Show the user the draft and say plainly what will be sent.** They can edit or drop it. **A problem report is never sent on the user's behalf without them reading it.**
4. **Map it onto the form:** `Request` = the symptom line · `Why / use-case` = repro + environment + outcome, compact · `Urgency` = the honest read (`Blocking me` only if they are actually still blocked).
5. **Hand over ONE link.** Same rule as Mode A — they click Submit, Claude never does.
6. **Never ask for a star on a problem report.** See below.

---

## The stars ask *(0.7-4)*

The repo's visibility helps other people find SB. Asking is fine — asking at the wrong moment is not.

**Fire ONLY when all four are true:**
1. The feedback was **positive** (a wish or a compliment, freely given). **Never on a Mode B problem report** — not even a fixed one. Someone whose system just broke is not who you ask for a favour.
2. The feedback has actually been **sent** (the ask rides after the confirm, never before).
3. **It has never fired on this install.** The stamp is a line in `profile/STACK.md`: `SB stars-ask: DD Mon YYYY`. **No line = never asked.** Write the line the moment the ask fires — asked, whether or not they starred (this is a once-ever ask, not a once-until-they-comply ask).
4. It fits in **one line.** No follow-up, no second mention, no reminder in a later session.

> *"If SB is earning its keep, a star on the repo helps other people find it: https://github.com/pipitmart/second-brain-kit — no obligation."*

**Never:** repeat it, nag, gate anything on it, or bring it up again after a decline. A decline is not recorded as anything but the stamp — there is no state to chase.

*Why the fences: the whole system's adoption red line is "SB fails when it costs more than it pays back." A promotional ask that fires twice, or fires while a user is annoyed, is SB taking from the user instead of giving — which is the failure mode, not a growth tactic.*

---

## The form — SB Feedback form v0.1

Base form + field map:

| Field | entry ID | Filled with (Mode A — request) | Filled with (Mode B — problem) |
|---|---|---|---|
| User + SB version | `entry.1340849794` | `Name · vX.X` | `Name · vX.X` |
| Request (one line) | `entry.152593079` | the request | the **symptom**, one line, prefixed `PROBLEM:` |
| Why / use-case | `entry.815746520` | the why | **repro · environment · outcome**, compact |
| Urgency | `entry.2022246250` | `Nice to have` \| `Would really help` \| `Blocking me` (exact match) | same options; `Blocking me` only if still blocked |

*The form has four fields and needs none added — a problem report is a request whose "why" is a diagnostic. Keep the free text short: it travels inside a URL.*

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
