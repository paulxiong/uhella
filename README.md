<div align="center">

<img src="https://github.com/user-attachments/assets/b34cd72d-176b-44dd-bb46-093375b3bd2e" alt="Uhella — free up your iCloud space" width="420" />

# Uhella

### Stop paying for iCloud storage — keep every photo, on your own Mac.

[![Download](https://img.shields.io/github/v/release/paulxiong/uhella?label=Download&color=0A84FF)](https://github.com/paulxiong/uhella/releases/latest)
![macOS 13+](https://img.shields.io/badge/macOS-13%2B-black?logo=apple)
![Apple Silicon and Intel](https://img.shields.io/badge/Mac-Apple%20Silicon%20%7C%20Intel-555)
![100% local AI](https://img.shields.io/badge/AI-100%25%20local-34C759)
![Free](https://img.shields.io/badge/Price-Free-34C759)

**[⬇️ Download for Mac](https://github.com/paulxiong/uhella/releases/latest)** &nbsp;·&nbsp; **[▶️ Watch the demo](https://www.youtube.com/watch?v=K22UKcL3UnM)** &nbsp;·&nbsp; **[中文说明](README_zh.md)**

</div>

---

## What is Uhella?

Uhella is a free Mac menu-bar app that solves the "iCloud storage full" problem — without deleting a single memory.

It safely moves your **older photo originals out of iCloud and onto your own Mac or external drive**, and only removes anything from iCloud after a verified copy exists on your disk. On top of that, it gives you **private AI photo search** — ask for *"lions"*, *"Nairobi 2025"*, or *"sunset at the beach"* from your phone, and the AI that answers runs entirely on your Mac.

On your phone, use the free **[Uhella iPhone app](https://apps.apple.com/us/app/uhella/id6737429973)** — it finds your Mac on your Wi-Fi by itself — or a Telegram bot. Your choice.

## Why Uhella?

|  | Cloud photo services | **Uhella** |
|---|---|---|
| Your photos uploaded to someone's servers | Yes | **Never — they stay on your Mac** |
| AI search works without internet | No | **Yes — 100% local AI** |
| Monthly fee | $3–$12 / month, forever | **Free** |
| Who owns the storage | Them | **You** |

iCloud 2 TB costs about **$120 every year**. A hard drive you probably already own holds the same photos — forever, for free. Uhella is not a replacement for iCloud or Google Photos; it's a companion that sits on top of what you already use and keeps the bill small.

## How it works

1. **Local AI learns your library.** A vision model on your Mac looks at every photo and writes a description; nothing is uploaded anywhere.
2. **Originals are archived to your drive.** Full-quality originals are continuously exported into monthly folders (`~/uhella_archives/2026-07/ …`) on the disk you choose, and each file is **verified** against the archive.
3. **You free iCloud space — your way.**
   - Tap the **Move** button when Uhella tells you photos are safely archived, or
   - Set **Auto Archive** ("keep at most N photos in iCloud") and let it run itself, or
   - Drag photos into the **Move Now** album to archive them immediately.

> **Safety first 🛡️**
> Nothing is ever removed from iCloud until a verified copy exists on your drive. Photos in your **Keep Forever** album are never touched. And every archived photo can be **restored** back into Photos later — with its original date.

## Features

- 🔍 **Search by memory, not keywords** — "kids at the beach last summer" just works, powered by local Gemma + vision models
- 📱 **iPhone app included** — the free [Uhella app](https://apps.apple.com/us/app/uhella/id6737429973) connects straight to your Mac, zero setup on your home Wi-Fi
- 💬 **Telegram bot (optional)** — prefer chat? Search, browse, and free space from any Telegram app
- 🗄️ **Auto Archive** — keep iCloud at a fixed size, oldest photos move off automatically
- ⚡ **Move Now / Keep Forever albums** — full manual control with two drag-and-drop albums
- 📚 **Multiple Photos libraries** — switch between libraries from the menu bar
- 💾 **Any drive, movable anytime** — archive to an external disk and migrate later with *Copy Storage to New Drive*
- ♻️ **Restore anytime** — bring archived photos back into Photos with original dates
- 🔄 **Auto-updates** — checks every 6 hours, updates itself

## Install (about 10 minutes — most of it is waiting)

**You need:** a Mac running **macOS 13+** (Apple Silicon or Intel), ~8 GB free disk for the AI models, and ideally a Mac that stays on — a **Mac mini** is perfect.

1. **[Download the DMG](https://github.com/paulxiong/uhella/releases/latest)** — pick `…_arm64.dmg` for Apple Silicon (M-series) or `…_x86_64.dmg` for Intel Macs.
2. Open it and **drag Uhella Backend to Applications**, then launch it.
3. A **step-by-step setup window** takes over: it downloads the local AI models (~7 GB, one time, with progress bars). No terminal, no config files.
4. When macOS asks, allow **Photos access**, **Full Disk Access**, and **Accessibility** — Uhella shows you exactly where to click for each.

That's it — Uhella keeps itself updated automatically from then on, and the iPhone app already works over your home Wi-Fi with zero extra setup.

> **Remote access (below) is entirely optional.** You don't need to touch it — Uhella works fully at home either way, and **Auto Archive and everything else running on your Mac keep working regardless of whether you ever set up remote access.** Set it up only if you also want to reach your photos in the iPhone app when you're away from home.

## Using it

Uhella lives in your **menu bar**:

<img src="menu.png" alt="Uhella menu bar" width="360" />

- **Auto Archive** — set the "keep at most N photos" limit
- **Photo Library** — switch libraries, reset the index, clean caches
- **Telegram Bot / Configure Telegram** — set up phone access
- **Check for Updates**, **Start at Login**, **Keep Awake**

### On your phone

**At home, on the same Wi-Fi as your Mac, the [Uhella iPhone app](https://apps.apple.com/us/app/uhella/id6737429973) just works** — open it and it **finds your Mac automatically**, nothing to configure. (You can also type your Mac's name or IP once, if you prefer.)

Want to reach your photos when you're **away from home** too? That's what remote access is for. Pick whichever of these fits you — or skip all of them and keep using Uhella at home only:

**① Tailscale — the full app, from anywhere.** Install the free **[Tailscale](https://tailscale.com)** app on both your iPhone and your Mac and sign in with the same account on each — that's the whole setup. The Uhella app then reaches your Mac securely from anywhere, not just at home.

**② Telegram — chat access, from anywhere** (great for chat lovers, ~4 minutes): create a bot with **@BotFather**, paste the token into *Configure Telegram…* in the menu bar app, and start chatting:

> `lions` · `Nairobi 2025` · `how many photos from Madagascar?` · `more` · `similar`

Full walkthrough: **[Telegram setup guide](uhella_telegram_guide.md)** · Demo: **[YouTube](https://www.youtube.com/watch?v=K22UKcL3UnM)**

**③ Static IP / local address — a manual fallback, home Wi-Fi only.** In the menu bar app, open **Remote Access → Local Network (Static IP)…** to see your Mac's name and address, and type it into the iPhone app's server picker if auto-discovery ever doesn't find it on its own.

## FAQ

<details>
<summary><b>Is it safe? Could I lose photos?</b></summary>

A photo becomes eligible for removal from iCloud only after its original has been exported to your drive **and verified**. Photos in the **Keep Forever** album are never removed. Anything archived can be restored back into Photos, with its original capture date.
</details>

<details>
<summary><b>What data leaves my Mac?</b></summary>

Your photos are **never uploaded to any AI or cloud service** — all models run locally. Photos leave your Mac only when *you* send them through your own Telegram bot. (For place names, Uhella sends GPS coordinates — never photos — to OpenStreetMap.)
</details>

<details>
<summary><b>Do I need Telegram?</b></summary>

No. The **Uhella iPhone app** talks directly to your Mac — on your home Wi-Fi it connects with zero setup. Telegram is just an alternative if you prefer chatting.
</details>

<details>
<summary><b>Does it cost anything?</b></summary>

No. The app is free, the AI models are free and run on your hardware. There is no account and no subscription.
</details>

<details>
<summary><b>Can I move the archive to a bigger drive later?</b></summary>

Yes — *Copy Storage to New Drive…* in the menu bar migrates everything, and you can plug or unplug archive drives anytime.
</details>

---

<div align="center">

**Get the Uhella app** — search your library and enjoy an AI photo discovery feed, right from your phone:
[iOS / iPadOS](https://apps.apple.com/us/app/uhella/id6737429973) · [macOS](https://apps.apple.com/my/app/uhella/id6737429973?platform=mac)

Questions or problems? **[Open an issue](https://github.com/paulxiong/uhella/issues)**

</div>
