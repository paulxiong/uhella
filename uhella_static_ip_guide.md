# Uhella — Local Network (Static IP)

The simplest of Uhella's three remote-access options: no install, no sign-in, nothing to configure. If your iPhone is on the same Wi-Fi as your Mac, you're already set up.

---

## What This Is (and Isn't)

Uhella's backend always listens on your home network — it doesn't need Tailscale or Telegram to be reachable from other devices in your house. This option is just a way to find the address to type in.

| | **Local Network (Static IP)** | Tailscale | Telegram |
|---|---|---|---|
| Setup required | None | Mac wizard + iPhone install/sign-in | Create a bot, paste a token |
| Works away from home | ❌ No | ✅ Yes | ✅ Yes |
| Full Uhella app | ✅ Yes | ✅ Yes | ❌ Chat only |

The one real limitation: this **only works when your iPhone is on the same Wi-Fi/local network as your Mac.** Take your phone out of the house and it won't connect this way — that's what Tailscale or Telegram are for.

---

## Finding the Address

In the Uhella menu bar app, open **Remote Access → Local Network (Static IP)…**. A small window opens (titled "Local Network (Static IP)", headed **"Connect via Local Network"**), showing two things your Mac already knows about itself:

- **Machine name (recommended):** something like `my-mac-mini.local`
- **IP address:** a numeric address like `192.168.1.42`

There's a **Copy Address** button that grabs the full address — including the port Uhella's backend runs on (`8765`), e.g. `http://my-mac-mini.local:8765` — ready to paste on your iPhone.

---

## Why the Machine Name Instead of the IP?

The numeric IP address is normally assigned automatically by your router (this is called DHCP), which means it can quietly change over time — after a router restart, a firmware update, or just occasionally on its own. If that happens, an address you'd saved on your iPhone would stop working with no obvious explanation.

The machine name (the `.local` hostname) doesn't have that problem — it stays the same no matter how your router reassigns numeric IPs. Uhella's iPhone app already looks for this kind of hostname automatically when it tries to find your Mac on the network.

**Recommendation:** always use the machine name first. Only fall back to the numeric IP if the hostname doesn't seem to work for some reason (a small note in the window itself points this out too — *"May change if your router reassigns it via DHCP"*).

---

## Entering It on the iPhone

1. Open Uhella on your iPhone.
2. Tap the server icon.
3. Paste the address you copied into the manual entry field.

This is the exact same picker screen you'd use for Tailscale — the only difference is that this address only works while your iPhone is connected to the same Wi-Fi network as your Mac.

---

*Uhella keeps everything local on your Mac. This option doesn't send your connection through any outside service at all — it's the same private home network you're already on.*
