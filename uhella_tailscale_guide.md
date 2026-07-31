# Uhella — Full Access From Anywhere, With Tailscale

Chatting with your photos over Telegram is great, but sometimes you want the *real* app — the full gallery, the timeline, the search UI — not just a chat window. Tailscale is how you get that, from anywhere in the world.

---

## What Tailscale Adds

Telegram gives you a conversation with your photos. Tailscale gives you the whole app.

| | Telegram | **Tailscale** |
|---|---|---|
| Full Uhella app (gallery, timeline, search UI) | ❌ No — chat only | ✅ Yes |
| Works away from home | ✅ Yes | ✅ Yes |
| One-time setup | Create a bot, paste a token | Install + sign in (once each, on Mac and iPhone) |
| What it feels like | Texting a friend about your photos | Using Uhella exactly like you would at home |

Of the three remote-access options in Uhella, Tailscale is the richest — it's a private, encrypted network between your own devices, so your iPhone can reach your Mac's full backend as if it were sitting on your home Wi-Fi, no matter where you actually are.

It's not a zero-click process — there's a one-time install-and-sign-in on the Mac, plus a one-time App Store install and sign-in on the iPhone. After that, it just works, from anywhere.

---

## Mac-Side Setup

In the Uhella menu bar app, open **Remote Access → Set Up Tailscale…**. A single window opens (titled "Set up Tailscale", headed **"Tailscale Remote Access"**) that shows your current status and adapts to it — there's no forced multi-step wizard, and you can reopen it anytime to check where things stand.

### If Tailscale isn't installed yet

The status line reads **"Status: Not installed"**. Click **Download & Install Tailscale →** — Uhella downloads the official Tailscale.app installer directly from Tailscale and runs it for you. This may ask for your Mac password once — that's normal. The status line updates automatically once it finishes.

### If it's installed but you're not signed in

The status line reads **"Status: Installed — not signed in yet"**. Click **Sign In →** — your browser opens Tailscale's own sign-in page. Sign in with any account you like — Google, Microsoft, GitHub, or email — and approve this Mac when prompted.

This part is a one-time, hands-on step by Tailscale's own design — Uhella can open the page for you, but can't finish the sign-in on your behalf, and this window's own status can occasionally lag behind reality. After signing in, **check the Tailscale icon in your Mac's menu bar (top of the screen)** to confirm you're actually connected — that's the reliable place to verify, rather than watching this window.

### Once you're signed in

The status line turns green: **"Status: ✓ Signed in — `<your-hostname>`"**. Below it you'll see your Mac's MagicDNS hostname in a box, something like `my-mac-mini.tailnet-name.ts.net` — this is the address your iPhone will use to reach it.

Click **Copy** next to it to grab the full address, including the port Uhella's backend runs on (`8765`), e.g. `http://my-mac-mini.tailnet-name.ts.net:8765` — ready to paste on your iPhone.

> Never use the numeric 100.x address — only this hostname is guaranteed to work from your iPhone. (More on why below.)

---

## iPhone-Side Setup

1. **Install Tailscale from the App Store** on your iPhone — it's free.
2. **Sign in with the exact same Tailscale account** you used on the Mac in the sign-in step above. Both devices need to be on the same tailnet for this to work.
3. **Open Uhella**, tap the server icon, and paste the address you copied from your Mac's "All Set" step into the manual entry field.

That's it — Uhella now talks to your Mac over Tailscale, wherever you are.

> **Important:** Always paste the hostname (the one ending in `.ts.net`), never the numeric `100.x.x.x` address, even if you see both somewhere in Tailscale's own apps. iOS's built-in network security settings only make an exception for the hostname form — the numeric IP will silently fail to connect from the iPhone even though it works fine on other platforms.

---

## Is My Mac Exposed to the Internet?

**No.** This is the question most people quietly worry about, so let's be direct about it.

Tailscale doesn't open your Mac up to "the internet" in the way a public web server would. It builds a private mesh network — think of it as an invitation-only Wi-Fi network that happens to work over any internet connection, anywhere in the world, instead of just one room.

- Only devices signed into *your own* Tailscale account (your tailnet) can ever reach your Mac this way — a stranger who somehow learns your MagicDNS hostname still can't connect without being signed into your tailnet.
- The trust model is the same shape as being on your home Wi-Fi — it's just no longer limited to your home's physical range.
- Uhella's backend itself doesn't change at all when you turn on Tailscale — it's the exact same server you already trust on your LAN. Tailscale just widens who can reach it, from "anyone on my Wi-Fi" to "anyone on my tailnet" — and your tailnet only ever has the devices you personally signed in.
- Your photos, notes, and search index all still live only on your Mac. Tailscale carries the connection; it doesn't see or store your data.

If you're comfortable trusting your own home Wi-Fi with your photos, you can be equally comfortable trusting your own tailnet — it's the same circle of trust, just not tied to one room anymore.

This trust model depends on keeping your tailnet exactly as private as your home Wi-Fi is: don't turn on Tailscale Funnel (which deliberately exposes a service to the public internet) and don't share this Mac with another person's Tailscale account — either one would let someone outside your own circle of devices reach Uhella's backend, which has no login or access control of its own beyond "being on the tailnet."

---

## Troubleshooting

**Can't sign in / sign-in seems stuck.** Check the Tailscale icon in your Mac's menu bar to confirm it shows you as logged in. If not, reopen **Remote Access → Set Up Tailscale…** and click **Sign In →** again — it's safe to repeat.

**MagicDNS hostname isn't resolving on the iPhone.** Confirm both devices are signed into Tailscale and connected (open the Tailscale app on each and check its status screen). A device that's been asleep or just restarted can take a few seconds to reconnect.

**Not working over cellular data.** This is rare, and usually a NAT/carrier network edge case rather than anything wrong with your setup. Open the Tailscale app on your iPhone and check its own connection status/diagnostics — Tailscale will usually tell you directly whether it's connected and how (direct connection vs. relayed).

---

*Uhella keeps everything local on your Mac. Tailscale only carries the connection between your own devices — your photos and conversations never pass through anyone else's servers.*
