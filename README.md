# Uhella Backend Server User Guide

The **backend server** is the small Mac app that keeps Uhella running in the background.

It does the heavy work for you:

- reads your local Photos library
- runs the AI on your Mac
- powers the Telegram bot
- keeps indexing in the background
- helps with archive and free-iCloud-space tasks

Your photos stay on **your Mac**.

---

## What you need

- macOS 13 or newer
- the **Uhella Backend** app
- permission to access your Photos library
- internet for first-time setup and Telegram
- a Mac that can stay on, or a **Mac mini** if you travel a lot or want Uhella available anytime

---

## First-time setup

The first launch is simple and easy.

Uhella Backend shows a **step-by-step setup window** and guides you through everything with a normal Mac UI — no terminal, no manual server setup.

It will guide you through downloading and preparing what it needs:

1. **Ollama**
2. **AI engine**
3. **AI model**

The first setup can take a while because it may need to download several GB of files. After that, daily use is much easier.

If macOS asks for **Photos access**, allow it.

---

## Setting up Telegram

If you want to use Uhella from your phone, set up Telegram inside the backend app.

Uhella guides you through it step by step:

1. Create a Telegram bot with **@BotFather**
2. Paste the bot token into Uhella
3. Find your Telegram ID with **@userinfobot**
4. Confirm that Telegram messages are reaching you

There is also an optional step for **large video downloads**.

---

## What the backend server does every day

Once it is running, Uhella Backend works by itself:

- keeps the AI server running
- keeps your photo index up to date
- powers photo search and chat replies
- sends Telegram messages through your bot
- watches for archive-ready photos and videos

When Uhella has photos or videos ready to move, it can send a Telegram notification with a **Move** button.

---

## What you will see in the menu bar

Uhella Backend lives in the **macOS menu bar**. That is your control center.

Common options include:

- **Open Uhella**
- **Start at Login**
- **Keep Awake**
- **Photo Library** — switch libraries, reset index, clean photo cache
- **Telegram Bot**
- **Configure Telegram**
- **Check for Updates**
- **Quit Uhella Backend**

---

## Useful everyday settings

### Start at Login

Turn this on if you want Uhella to start automatically when your Mac signs in.

### Keep Awake

Turn this on if your Mac tends to sleep and you want Uhella to stay available.

### Photo Library

If you have more than one Photos library, you can switch between them from the menu bar.

You can also:

- **Reset Index** if you want Uhella to rebuild its search data from scratch
- **Clean Photo Cache** if you want to free local preview-file space without touching the originals

---

## Status and warnings

The backend server checks itself while it runs.

It can show warnings for things like:

- Photos access missing
- low disk space
- iCloud Photos configuration problems
- setup not finished yet

If you see a warning, open the menu bar app first. In many cases, the fix is shown right there.

---

## Privacy

Uhella Backend is designed to run **locally on your Mac**.

- your Photos library stays on your machine
- the backend server reads your library locally
- Telegram is used as the chat channel

Uhella is meant to give you a private way to talk to your own photo library.

---

## If something is not working

Try these first:

1. Make sure the backend app is open in the menu bar
2. Make sure your Mac is awake and connected to the internet
3. Make sure Photos access was allowed
4. Open **Configure Telegram** again if the bot is not replying
5. Use **Reset Index** if photo search looks incomplete or outdated
6. Use **Check for Updates** to install the latest backend version

If your Mac often sleeps or you travel a lot, a **Mac mini** is usually the easiest long-term setup.

---

## Simple way to think about it

**Uhella Backend is the always-working part of Uhella. It stays on your Mac, runs the AI, keeps your library ready, and lets Telegram talk to your photos.**

