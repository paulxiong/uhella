# Uhella — Your Private Photo Memory

Your entire photo library. Searchable by memory, not just keywords. Accessible from anywhere, privately.

---

## Why Not Just Use Google Photos or iCloud?

You probably already use one of those. Here's where they fall short — and what Uhella does differently.

| | Google Photos | iCloud | **Uhella** |
|---|---|---|---|
| Search by memory ("that beach trip where it rained") | Limited | Very limited | ✅ Yes |
| AI understands what's *in* the photo | Basic | Basic | ✅ Deep visual understanding |
| You can correct or teach it | ❌ No | ❌ No | ✅ Yes — just reply |
| Your photos uploaded to their servers | ✅ Always | ✅ Always | ❌ Never — stays on your Mac |
| Works without internet | ❌ No | ❌ No | ✅ Yes (local AI) |
| Monthly subscription | $3–$10/mo | $1–$10/mo | Free after setup |
| Proactively resurfaces forgotten memories | ❌ No | ❌ No | ✅ Yes |
| Access from phone via familiar app | Google Photos app | Photos app | ✅ Telegram |

Google Photos and iCloud are great for storage and basic search. But they work on **their** terms — your photos live on their servers, their AI decides what matters, and you can't teach them anything. The moment you cancel, your access gets complicated.

Uhella is different: the AI runs on your Mac, your photos never move, and **you** train it over time by just chatting.

---

## Why a Mac Mini?

Uhella needs a computer that's always on, always home. A Mac Mini is the perfect fit:

- **Always awake** — it sits quietly on your desk or shelf, running 24/7
- **Silent and small** — about the size of a thick paperback book, no noise
- **Low power** — uses roughly as much electricity as a light bulb
- **Your Mac, your rules** — the AI model runs entirely on it, no cloud needed
- **Works with your existing Photos library** — no migration, no copying, no changes

You don't need to do anything special. As long as the Mac Mini is on and connected to the internet, your Telegram bot works from anywhere in the world — at home, traveling, wherever.

If you already have a Mac that's always on, that works too. A Mac Mini is simply the most practical dedicated device for this.

---

## Installation — How Hard Is It?

Honest answer: **about 10 minutes**, and most of it is waiting, not doing.

Here's the full process:

1. **Download** the Uhella app from [github.com/paulxiong/uhella/releases/tag/1.1.8](https://github.com/paulxiong/uhella/releases/tag/1.1.8) — one `.dmg` file, drag it to Applications, open it
2. **Grant Photos access** — macOS will ask, click Allow
3. **Create a Telegram bot** — Telegram has a built-in "BotFather" account; send it one message, copy the token it gives you
4. **Paste the token** into Uhella's settings window
5. **Wait** — Uhella quietly reads and describes your entire library in the background (a few hours for large libraries, nothing you need to watch)
6. **Open Telegram and start chatting**

That's it. No terminal, no configuration files, no developer tools required.

> If you're comfortable installing a Mac app and sending a Telegram message, you can set this up.

---

## Will It Change or Delete My Photos?

**No. Uhella never touches your photos.**

- It reads your Photos library the same way a slideshow screensaver would — as a viewer, not an editor
- It cannot move, rename, delete, or modify any photo
- It stores its own notes (descriptions, search index) in a separate database file that has nothing to do with your Photos app
- If you uninstall Uhella, your Photos library is exactly as you left it

Your photos are safe. The only thing Uhella writes to disk is its own index — a set of notes about your photos that it uses to answer your questions.

---

## Why Telegram?

It might seem like an unusual choice. Here's why it works so well:

**You already have it.** Most people have Telegram on their phone, tablet, and computer. No new app to learn.

**It works everywhere.** Your bot is reachable whether you're on your home WiFi or traveling in another country. As long as Telegram works, Uhella works.

**It's a real conversation.** Unlike a photo search app where you type in a box and scroll results, Telegram feels like asking someone. You can follow up, correct, refine, and the bot remembers the context of what you were just talking about.

**Replies are natural.** Replying to a photo message to label it ("That's Grandma Helen") is exactly how you'd annotate something in a real conversation — no separate "edit metadata" screen.

**It's already secure.** Telegram's encryption handles the connection between your phone and your Mac. You're not opening any new security holes; you're routing through a protocol millions of people already trust.

**It's private by design.** The bot only responds to one person — you. Anyone else who finds your bot gets no response.

**No app updates to manage.** Telegram updates itself. Uhella's intelligence lives on your Mac and improves when you teach it, not when you download something.

---

## Getting Started

Once Uhella is set up on your Mac, open Telegram and find your Uhella bot. Everything happens in that private chat — just type naturally.

---

## Searching Your Photos

Type what you want to find. No special commands needed.

| What you type | What happens |
|---|---|
| `Tanzania` | Shows photos from Tanzania |
| `birthday cake 2023` | Shows birthday photos from 2023 |
| `sunset beach` | Shows beach sunsets |
| `mom and dad` | Shows photos with your parents |
| `old car` | Shows vintage or classic cars |

The bot understands context — dates, places, people, objects, moods. It reads the visual description of each photo, so it finds things even if they're not in a filename or album.

Up to **5 photos** are shown at a time. If you want more, ask: *"show me more"* or *"any others?"*

---

## Proactive Memories

After **5 minutes of silence** in the chat, the bot will occasionally surface something interesting from your library on its own — a forgotten trip, a funny moment, something from years ago.

This happens only once per quiet period. As soon as you reply, the timer resets and the bot waits again.

---

## Teaching the Bot About Your Photos

The bot learns from what you tell it. When you reply to a photo the bot sent, your words get attached to those photos — making future searches smarter.

### Adding context

Reply directly to a bot photo message and describe what's in it:

> *"This is from our Kenya safari in 2019"*

> *"That's Grandma Helen at Christmas"*

> *"Lake Tahoe — the summer we rented the cabin"*

The next time you search for "Kenya", "Grandma Helen", or "Lake Tahoe", those photos will show up.

### Correcting a wrong description

If the bot thinks something is in a photo but it isn't, just say so:

> *"No girl here"*

> *"Not a beach"*

> *"There is no dog"*

The bot removes that detail from the photo's description. Future searches won't pull up that photo for that term.

### Labeling the most recent search results

You don't have to quote a specific message. If you just type a label or correction after a search, it applies to the photos from that search:

1. You search: *"sunset"* → bot shows 5 photos
2. You type: *"These are from Santorini 2018"*
3. All 5 photos are now tagged with that context

---

## Tips

**Be specific when you can.** "Red dress birthday party" finds photos faster than "party".

**Names work well.** If you've told the bot "That's Grandma Helen", searching "Helen" will find her photos.

**Corrections stick.** Once you say "No dog" on a photo, it won't appear in dog searches again.

**You can chat naturally.** The bot handles full sentences, not just keywords. *"Do you have any photos from my trip to Japan?"* works just as well as *"Japan"*.

---

## What the Bot Cannot Do

- It only sees **your** library on the Mac it's installed on
- It cannot edit, delete, or move your actual photos — it only reads them
- It works best with photos that have been described (indexed). Newly added photos may take a few minutes to appear in search results
- The chat is **private** — only the account configured as the owner can use it

---

## Example Conversation

```
You:   Portugal trip
Bot:   [shows 5 photos from Lisbon 2022]

You:   [reply to one photo] This was the day we got lost in Alfama
Bot:   Got it, I've noted that for these photos.

You:   any photos with the yellow tram?
Bot:   [shows photos with the Lisbon tram]

You:   [5 minutes pass with no messages]
Bot:   Browsing your library and found something worth sharing 📸
       [shows 3 photos from a hike in 2019]

You:   oh wow I forgot about that hike! which mountain was this?
Bot:   Based on the location data and the scenery, this looks like it could be
       in the Pacific Northwest — possibly near Mount Rainier...
```

---

*Uhella keeps everything local on your Mac. Your photos and conversations never leave your machine.*
