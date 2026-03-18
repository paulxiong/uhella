# Debugging iPhone → Backend → Ollama with mitmdump (Uhella)

This guide explains how to use the included helper script and mitmdump addon to record a chronological timeline of traffic flowing from an iPhone (Flutter app) to the backend and then to Ollama. It also summarizes the three reviewed scripts:

- scripts/debug_ollama_traffic.sh — orchestrator that starts Ollama, the backend (uvicorn) and two mitmdump reverse proxies.
- scripts/mitmproxy_filter/timeline_logger.py — mitmdump addon that pretty-prints a chronological event timeline with truncated body context.
- scripts/resign_tflite_dylibs.sh — utility to re-sign TFLite dylibs and the app bundle (not required for mitmdump; included for completeness).

---

## Quick overview (what debug_ollama_traffic.sh sets up)

Flow created by the script:

- Flutter app (on iPhone) → mitmdump A listening on :8765 → uvicorn backend on :8766
- backend (uvicorn) → mitmdump B listening on :11435 → Ollama on :11434

The script:
- Restarts Ollama with `OLLAMA_MAX_LOADED_MODELS=1` (written to `/tmp/ollama_debug.log`).
- Starts the backend (uvicorn) at :8766 and sets `OLLAMA_HOST`/`OLLAMA_BASE_URL` to point to the Ollama proxy (:11435).
- Runs two mitmdump instances with the same addon (timeline_logger.py). mitmdump A runs in the background and mitmdump B runs in the foreground.
- Logs uvicorn to `~/.uhella_backend/server.log`.

Paths and ports used by the script (defaults):
- BACKEND_PROXY_PORT = 8765 (listen for Flutter → backend)
- BACKEND_REAL_PORT = 8766 (actual uvicorn backend)
- OLLAMA_PROXY_PORT = 11435 (listen for backend → Ollama)
- OLLAMA_PORT = 11434 (actual Ollama)

Run the orchestrator:

```bash
# Prereq: mitmproxy installed
brew install mitmproxy

bash scripts/debug_ollama_traffic.sh
```

Press Ctrl+C to stop. The foreground mitmdump process keeps the script alive.

---

## Prepare the iPhone/device

1. Make sure the iPhone and Mac are on the same network.
2. Install mitmproxy's CA certificate on the iPhone: open `http://mitm.it` from the device (or browse to the Mac IP:8765 if reachable) and follow the install instructions.
3. After installing the profile, enable full trust in Settings -> General -> About -> Certificate Trust Settings.
4. Set the Wi‑Fi HTTP proxy (Manual) on the iPhone to use your Mac's IP and port `8765` (BACKEND_PROXY_PORT). This routes the Flutter app's backend requests through mitmdump A.

Notes:
- If the app uses certificate pinning, mitmproxy cannot decrypt TLS traffic.
- If your environment uses different ports, update the script or the device proxy accordingly.

---

## How timeline_logger.py formats output

The addon prints an interleaved chronological timeline of requests and responses. Example lines:

```
12:34:01.123  →  Flutter→backend  POST /agent
                   {"prompt": "hello", "top_k": 25, ...}
12:34:01.145  →  backend→Ollama   POST /api/chat
                   {"model": "qwen2.5:3b", "messages": [...], ...}
12:34:08.321  ←  backend→Ollama   200  7.3kb  7176ms
                   {"message": {"role": "assistant", "content": "..."}}
12:34:08.334  ←  Flutter→backend  200  1.1kb  7211ms
                   {"response": "..."}
```

Legend:
- Timestamp: local time with millisecond precision.
- Arrow → (request) and ← (response).
- Label: `Flutter→backend` (cyan) or `backend→Ollama` (yellow). Labels are determined by port numbers in `timeline_logger._label()`; if ports differ the label will be `host:port`.
- The request/response body is printed below, indented, with truncation:
  - Strings truncated to ~120 chars (_MAX_STR).
  - Only the last _MAX_LINES lines of formatted JSON are shown.
  - NDJSON (Ollama streaming) is parsed and printed as an array of JSON objects.
- The addon ignores paths listed in `_FILTERED_PATHS` (by default `/status`).

If you need more detail, edit `timeline_logger.py` to increase `_MAX_STR` or `_MAX_LINES` or remove truncation.

---

## Common troubleshooting steps

- mitmdump not found: `brew install mitmproxy`.
- Certificate errors on device: confirm mitmproxy CA installed and trusted (Settings -> Certificate Trust Settings).
- Ports in use: stop conflicting apps or change the script's ports (search at top of `debug_ollama_traffic.sh`).
- Uvicorn fails to start: check `~/.uhella_backend/server.log` (the script prints last lines on startup failure).
- Ollama fails to start: check `/tmp/ollama_debug.log`.
- If you see placeholder asset IDs (e.g., `<tool asset id>1`) in timeline output: inspect the backend→Ollama POST /api/chat payload and the LLM assistant responses in the Ollama response; these placeholders generally originate from prompt examples or LLM-generated text and should be remedied by backend validation (see agent logs/tests).

---

## Useful manual mitmdump commands (equivalent to what's in the script)

Flutter → backend proxy (background):

```bash
mitmdump \
  --mode "reverse:http://localhost:8766@8765" \
  --set "view_filter=!~u /status" \
  -s scripts/mitmproxy_filter/timeline_logger.py -q &
```

Backend → Ollama proxy (foreground):

```bash
mitmdump \
  --mode "reverse:http://localhost:11434@11435" \
  -s scripts/mitmproxy_filter/timeline_logger.py -q
```

To save flows for later inspection (write to file):

```bash
mitmdump --mode "reverse:http://localhost:8766@8765" -s scripts/mitmproxy_filter/timeline_logger.py -w /tmp/uhella-flows.mitm
# later view with mitmweb -r /tmp/uhella-flows.mitm
mitmweb -r /tmp/uhella-flows.mitm
```

---

## Quick notes on resign_tflite_dylibs.sh

This is a robust re-signing utility used when bundling a macOS app that includes TFLite dynamic libraries. Key points:

- Usage: `resign_tflite_dylibs.sh <AppBundlePath> [CodeSignIdentity]`
- It finds libtensorflowlite dylibs and re-signs them, signs all Mach-O binaries inside the app, and finally re-signs the outer bundle.
- The script auto-selects a signing identity if none is provided and can auto-detect an entitlements file.
- Not required for mitmdump debugging, but useful when packaging a build that needs valid signatures.

---

## Summary / Best practices

- Use `bash scripts/debug_ollama_traffic.sh` to get a turnkey timeline view.
- Install and trust mitmproxy certificate on your iPhone before routing traffic.
- If you need full request/response bodies, tweak `_MAX_STR` and `_MAX_LINES` in `scripts/mitmproxy_filter/timeline_logger.py`.
- Save mitmdump flows with `-w` to review them later in mitmweb.

---

If you'd like, I can:
- Add quick shell snippets to automate installing the mitmproxy certificate on the iPhone (notes only — certificate install must be done on-device).
- Add a short troubleshooting checklist document for common issues (port conflicts, certificate pinning, app using HTTPS pinning).

File created: `scripts/mitmdump_debug_agent.md`