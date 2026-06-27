# START HERE — Your Second Brain on Claude

*You were sent this because someone thinks it will save you hours every week. This page is the only file you need to read. Everything else, Claude reads for you.*

---

## Why this exists — read this before anything else

Plain Claude has two gaps. **It forgets** — every new chat starts cold: you re-explain who you are, what you're building, what you decided last time. And **it waits** — it answers what you ask, then sits there; keeping the work moving is on you. Cowork gives Claude *hands* (files and tools), but only for **one session on one machine**. Close it and the memory's gone.

Second Brain fixes four things plain Claude and Cowork can't:

- **It forgets you** → SB gives Claude a durable memory of you and your work — your profile, plus a decisions log for every project.
- **It's trapped on one machine** → SB lives in your Google Drive, so it's the *same brain* on every computer you sign into.
- **Your decisions scatter across chats** → SB keeps one source of truth per project, so nothing gets re-litigated or lost.
- **It waits to be told** → SB makes Claude *drive the work* — surfacing your next step instead of sitting idle.

**What "drives the work" means today** (no magic, no overpromise): at the start of a session it tells you where you left off and the next open action; when a task feels too big to start, it breaks it into the one step you can take right now; when you stop, it names and saves the next step so next time begins clean.

**Before and after:**
- *Without SB:* "Hi Claude, I'm building X, last week I decided Y, the constraints are Z, where were we…" — ten minutes of re-briefing, every session, and you *still* have to work out what's next.
- *With SB:* you type `/orient`. Claude already knows X, Y and Z, and opens with *"Here's where you left off — the next step is ___."* You're working inside the first minute.

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

## Setup — three steps

1. **Install both apps** (Claude desktop, Google Drive desktop) and sign in to each. **Use the same Google account for both** — the account signed into Google Drive on your desktop must match the one connected to Claude (Claude → Settings → Connections → Google Drive). If they don't match, Claude opens an empty folder and nothing works. This is the single most common first-minute snag for anyone with more than one Google login.
2. **Get the kit into your Google Drive.** It comes as a **`.zip`**. Unzip it, then **drag the unzipped `ROOT` folder into your Google Drive on your computer** — the folder the Google Drive app shows on your machine (on Windows that's usually `G:\My Drive`; on Mac, `Google Drive` in Finder). *Don't try to "copy" a folder inside the Google Drive website — Drive can't copy folders cleanly, and that's where most people get stuck. Unzip-and-drag is the reliable way.* You don't need to download your whole Drive or change any settings — the system runs fine on normal streaming.
3. **Open Claude (Cowork), give it access to that folder** when asked, then **paste the bootstrap prompt below**. Claude takes it from there — it will check your setup, then offer you a choice: a **ten-minute Demo** on sample data (watch the whole thing work before anything is yours), or going straight to building your real profile (~15 minutes of honest questions) and your first project in the same sitting. Most people enjoy the Demo first.

## The bootstrap prompt — copy everything in the box

```
I've received a Second Brain kit (ROOT folder with kernel/, profile-templates/, and
skills/). It's in my Google Drive and I want you to set it up for me, end to end.

1. Ask me to mount/select the ROOT folder if you can't see it yet.
2. Read kernel/CLAUDE-KERNEL.md to understand the system.
3. Walk me through installing the .skill files from the skills/ folder
   (Claude Settings → Capabilities) — one at a time, tell me exactly what to click.
4. Run the protocol in kernel/STRESS-TEST.md to verify my environment.
5. My profile is empty — run kernel/PROFILE-GENESIS.md. Start with why this
   exists, then offer me the Demo (sample data) or Live (my real profile).
   Plain language, no jargon, one thing at a time.
6. Finish by setting up my first real project (kernel/NEW-PROJECT.md) so I leave
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

*Sending this to someone else? Send them the kit (the `.zip`) plus this message: "This sets up Claude to actually remember you and your work. You need a paid Claude plan, a Google account, and an hour. Unzip it, drag the `ROOT` folder into your Google Drive, open `START HERE`, and do what it says. Call me if you get stuck — but you probably won't need to."*
