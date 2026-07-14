# START HERE — Your Second Brain on Claude

*You were sent this because someone thinks it will save you hours every week. This page is the only file you need to read. Everything else, Claude reads for you.*

---

## Why this exists — read this before anything else

Claude remembers you now — but not your *work*. Its built-in memory gives it a general sense of who you are across chats: an automatic summary, managed on Anthropic's servers, that you can't inspect, edit, or point at a specific project. What it still doesn't do: tell you what you decided last Tuesday, why you decided it, what you tried that didn't work, or what you committed to doing next. And Cowork gives Claude *hands* (files and tools), but only for **one session on one machine**.

Second Brain fills four gaps that still exist:

- **Its memory is a black box** → SB puts your context in *your* Drive: your profile, your decisions, your rationale — structured, auditable, and yours to control. Nothing is summarised away or hidden from you.
- **It's trapped on one machine** → SB lives in your Google Drive, so it's the *same brain* on every computer you sign into.
- **Your decisions scatter across chats** → SB keeps one source of truth per project, so nothing gets re-litigated or lost.
- **It waits to be told** → SB makes Claude *drive the work* — surfacing your next step instead of sitting idle.

**What "drives the work" means today** (no magic, no overpromise): at the start of a session it tells you where you left off and the next open action; when a task feels too big to start, it breaks it into the one step you can take right now; when you stop, it names and saves the next step so next time begins clean.

**Before and after:**
- *Without SB:* Claude has a vague sense of past conversations, but no idea what you decided on a specific project, why you ruled something out, or what the one thing you committed to doing next actually was. You still spend the first few minutes re-orienting — if you even remember yourself.
- *With SB:* you type `/orient`. Claude knows exactly where you left off — the open decisions, the why behind them, and what's next. You're working inside the first minute.

**Is it worth the cost?** Loading your context spends a little of Claude's budget each session — but it buys back the re-briefing, the decisions you'd otherwise lose, and the "what was I even doing?" restart tax. The bar SB is built to clear: **it must pay back more than it costs, every single session.** If it ever feels like more work than it saves, it isn't doing its job.

**"Isn't this just a skill? Isn't Cowork enough?"** No. The commands you'll use (`/orient`, `/offload`, and the rest) are thin switches — the value is the memory and momentum they switch *on*. Strip out the Drive layer and the skills have nothing to read. The clean way to hold it: **Cowork is the body; Second Brain is the memory and the drive.** Think of it as a chief of staff — someone who remembers everything and always has your next move ready — except it's yours, private, on every machine and every project.

That's why it's worth one hour: you set it up once, never brief Claude from scratch again, and it keeps the work moving instead of waiting on you.

It has two halves: the **kernel** (the engine — same for everyone, you never touch it) and your **profile** (who you are — built *with* you, stays private, belongs to you).

## What you need

1. **Claude** — a paid plan with the Claude desktop app (Cowork mode).
2. **A Google account** — with the Google Drive desktop app installed and signed in.
3. **About one hour** — most of it is Claude working, not you.

## Privacy, upfront

Your profile — your working style, your projects, your patterns — lives in **your** Google Drive and is read by **your** Claude account. It is never sent to whoever gave you this kit, never uploaded anywhere else, and the system is designed so the personal layer and the shareable layer can never mix. The person who sent you this cannot see anything you build.

**Terms, briefly:** this Kit is yours to use for your own work, and you're welcome to pass it free to a friend — but it's not for resale or repackaging into a paid course or product without permission. Full terms in `LICENSE.md`.

## Setup — pre-flight first, then three steps

**0. Clear `PRE-FLIGHT.md` first** — four things checked with your own eyes (about ten minutes). It removes the snags that eat the first twenty minutes of most setups. Come back here when all four boxes are ticked.

1. **Install both apps** (Claude desktop, Google Drive desktop) and sign in to each. **Use the same Google account for both** — the account signed into Google Drive on your desktop must match the one connected to Claude (Claude → Settings → Connections → Google Drive). If they don't match, Claude opens an empty folder and nothing works. This is the single most common first-minute snag for anyone with more than one Google login.
2. **Get the kit into your Google Drive.** The kit is the file named **`second-brain-kit-v0.7.zip`** — *not* the "Source code (zip)" that sits next to it on the release page; that one unpacks into the wrong shape and is the most common snag we see. Unzip it, then **drag the unzipped `ROOT` folder into your Google Drive on your computer** — the folder the Google Drive app shows on your machine (on Windows that's usually `G:\My Drive`; on Mac, `Google Drive` in Finder). **There is only ever one `ROOT` — if you end up with two, or a `ROOT` inside a `ROOT`, delete the extra.** *Don't try to "copy" a folder inside the Google Drive website — Drive can't copy folders cleanly, and that's where most people get stuck. Unzip-and-drag is the reliable way.* You don't need to download your whole Drive or change any settings — the system runs fine on normal streaming.
3. **Open Claude (Cowork), give it access to that folder** when asked, then **paste the bootstrap prompt below**. Claude takes it from there — it will check your setup, then offer you a choice: a **ten-minute Demo** on sample data (watch the whole thing work before anything is yours), or going straight to building your real profile (~15 minutes of honest questions) and your first project in the same sitting. Most people enjoy the Demo first.

## The bootstrap prompt — copy everything in the box

```
I've received a Second Brain kit (a ROOT folder with kernel/, profile-templates/,
and skills/ inside it). It's in my Google Drive and I want you to set it up for me,
end to end.

1. Ask me to mount/select the ROOT folder if you can't see it yet.

2. FIRST — before installing anything — check the kit arrived in one piece.
   Open ROOT and confirm it has exactly three folders inside: kernel/,
   profile-templates/, and skills/ — each with its files — and that ROOT isn't
   accidentally sitting inside another ROOT folder.
   - If it's all there: say "kit looks good" and continue.
   - If anything is loose, missing, or doubled up: STOP. Tell me in plain words
     what's wrong and the ONE thing to fix it (usually: delete this from Drive,
     unzip the kit again, then drag the ROOT folder in once — don't open it,
     don't rename it). Don't go further until I've fixed it and you've re-checked.
   - If ROOT reads as EMPTY or can't be found at all: the most likely cause is
     that Google Drive on this computer and Claude's Google Drive connector are
     signed into DIFFERENT Google accounts. Say exactly that, in plain words,
     and walk me through checking both emails (Drive icon on my computer, and
     Claude Settings -> Connectors -> Google Drive) before trying anything else.
     Don't treat this as a broken kit - it's almost always the account.

3. Read kernel/CLAUDE-KERNEL.md to understand the system.
4. Walk me through installing the .skill files from the skills/ folder
   (Claude Settings → Capabilities) — one at a time, tell me exactly what to click.
5. Run the protocol in kernel/STRESS-TEST.md to verify my environment.
6. My profile is empty — run kernel/PROFILE-GENESIS.md. Start with why this
   exists, then offer me the Demo (sample data) or Live (my real profile).
   Plain language, no jargon, one thing at a time.
7. Finish by setting up my first real project (kernel/NEW-PROJECT.md) so I leave
   this session with something working.

If anything fails, use kernel/HELP-ME.md to troubleshoot it with me instead of
giving up. Treat me as smart but completely new to this system.
```

## After setup — your whole routine

| You type | What happens |
|----------|--------------|
| `/orient` | Start of every session — Claude loads everything and shows you where you left off |
| *(just work)* | Claude saves decisions as you make them |
| `/offload` | End of session — work saved, next step named |
| `/help-me` | Anything stuck or broken — or you, in a rut |

That's the entire learning curve. Three commands and honesty.

**One thing that can look like a bug but isn't:** when you open one of your files on the Google Drive *website*, you're seeing a snapshot from the moment you opened the tab — it doesn't refresh itself. If Claude just saved something and the website still shows the old version, the change is on its way up (or already there) — just **close the tab and reopen the file** to see the current version. Every Decisions Log has a "Last updated" line at the very top; if it shows an older date than your latest work, you're looking at a stale tab, not lost work. Nothing Claude saves is ever gone.

## Where your stuff lives

`ROOT` holds the engine and your private profile — you never edit inside it. **Your projects live as their own folders *next to* ROOT, never inside it.** Claude sets each one up for you in the right place; you don't have to think about it.

One thing Claude will ask you to do for each project: **connect two folders to it — the project's own folder and `ROOT`.** That's because the system saves your project's decisions in the project folder *and* updates your profile in `ROOT` every time. Two folders per project, every time — Claude walks you through it and double-checks it at the start of every session, so you can't get it wrong for long. *(If you're ever tempted to just connect "everything at once" to save clicks — don't; Claude works one project at a time on purpose, and connecting them all makes it lose track of which is which.)*

**Tip for a tidy Drive:** keep `ROOT` and all your project folders together inside one parent folder (call it anything — `Brain` works). That just makes them easy to *find* as your Drive fills up; you still connect them to Claude two at a time as above.

```
Your Google Drive/
└── Brain/                   ← optional tidy home — keeps it all in one place
    ├── ROOT/                ← the system (you never touch this)
    ├── [Your first project]/   ← each project its own folder, beside ROOT
    └── [Another project]/
```

---

*Sending this to a friend? (Free to pass on — just not for resale.) Send them the kit (the `.zip`) plus this message: "This gives Claude a proper memory of your work — what you decided, why, and what's next — so it can actually drive your projects instead of waiting to be told. You need a paid Claude plan, a Google account, and an hour. Unzip it, drag the `ROOT` folder into your Google Drive, open `START HERE`, and do what it says. Call me if you get stuck — but you probably won't need to."*
