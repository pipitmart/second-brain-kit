# PRE-FLIGHT — four things to have ready before we start

*This takes about ten minutes and it removes the snags that eat the first twenty minutes of most setups. Do it **before** the call (or before you paste the bootstrap prompt). Don't just tick the boxes from memory — actually open each thing and look. Every item below is something you can **see on your screen**, not something you think is probably fine.*

---

## 1. The Claude desktop app — installed, open, on a paid plan

**Check it:** Claude is open as its **own app window** on your computer — not a browser tab. Then open **Settings** and confirm your plan is **Pro, Max, or Team** — not Free.

**You're ready when:** you're looking at the Claude app (not claude.ai in a browser) and your plan is a paid one.

**If not:** download the desktop app from `claude.ai/download`, install it, sign in. Upgrade your plan in **Settings → Billing** if you're on Free.

*Why it matters: the browser version can't do the file work this system needs. A paid plan is required — there's no free tier that runs it.*

---

## 2. Google Drive for Desktop — installed, signed in, and syncing

**Check it:** look for the **Google Drive icon** in your menu bar (Mac, top-right) or system tray (Windows, bottom-right). Click it — it should say something like **"Up to date,"** not stuck part-way. Then open Finder/Explorer and confirm you can see a **Google Drive location** (Mac: "Google Drive"; Windows: usually a **G:** drive).

**You're ready when:** the Drive icon is present, says it's synced, and you can see your Google Drive as a place on your computer.

**If not:** download it from `google.com/drive/download`, install, and sign in.

*Why it matters: this is the piece that syncs your files to every machine and lets Claude save your work into Drive. Without it, nothing is saved where Claude can reach it.*

---

## 3. The SAME Google account on both — this is the big one

**Check it — two places, and the emails must be identical:**
- **Drive for Desktop:** click the Drive icon → note the **email address** shown at the top.
- **Claude:** **Settings → Connectors → Google Drive** → note the **connected email**.

**You're ready when:** those two email addresses are **exactly the same**.

**If not:** reconnect Claude's Google Drive using the *same* account your desktop Drive is signed into (or switch the desktop Drive to match). Pick one account and make both sides use it.

*Why it matters: if the two don't match, Claude opens an **empty folder** and nothing works — and it looks like the system is broken when really it's just pointed at the wrong Drive. This is the single most common first-minute failure, and it's silent. If you have more than one Google login, assume this is your risk and check it twice.*

---

## 4. The kit — downloaded and **unzipped** on your computer

**Check it:** you have a folder named **`ROOT`** sitting on your computer, and opening it shows **three folders inside — `kernel/`, `profile-templates/`, `skills/`**. Not a `.zip` file. Not still sitting zipped in your Downloads.

**You're ready when:** you can open `ROOT` and see those three folders with files inside them.

**If not:** find the **`.zip`** you were sent and unzip it — double-click it (Mac), or right-click → **Extract All** (Windows) — so you get the `ROOT` folder. **Don't drag it into Drive yet** — we do that together, once, in the right way. (Copying a folder *inside* the Drive website doesn't work cleanly; unzip-on-your-computer-then-drag is the reliable path.)

*Why it matters: a half-unzipped or double-nested kit is the other silent failure — it looks installed but breaks the moment we run it.*

---

## You're cleared for takeoff when…

All four are true, checked **with your own eyes**:

- [ ] Claude **desktop app** open, on a **paid** plan
- [ ] **Google Drive for Desktop** installed and showing "up to date"
- [ ] The **same Google account** on Drive *and* on Claude's Drive connector
- [ ] The **`ROOT`** folder unzipped on your computer, three folders inside

*Two small things we'll handle together during setup, so don't worry about them now: choosing the faster model (Sonnet), and marking `ROOT` "available offline" so Claude can edit inside it. You'll be walked through both.*

---

*Next: open `START-HERE.md` and follow it. It has the one prompt you paste into Claude — and from there, Claude does the rest.*
