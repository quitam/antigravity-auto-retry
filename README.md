# Simple AGQ — Antigravity Quota Monitor & Auto Retry

> A lightweight VS Code extension for [Antigravity IDE](https://antigravity.dev) that monitors AI model quotas in real-time and **automatically retries** when agents fail.

**Tested and optimized for macOS Apple Silicon (M1/M2/M3/M4).**  
Also supports macOS Intel, Linux (Ubuntu/Debian), and Windows.

---

## ✨ Features

### 📊 Real-time Quota Monitoring

- View remaining quota for all AI models (Gemini, Claude, GPT, etc.)
- Color-coded status: 🟢 OK, 🟡 Low (<20%), 🔴 Exhausted
- Countdown timer showing when quota resets
- Auto-refresh every 2 minutes (configurable)
- Status bar indicator for quick glance

### 🔄 Auto Retry (New in v1.2, major rewrite in v1.2.3)

- Automatically clicks **"Retry"** when the AI agent encounters errors
- No more babysitting — zero-click automation
- **Fire-and-forget injection** — connects via CDP, injects script, disconnects immediately (no persistent WebSocket)
- **Instant detection** — uses `MutationObserver` to react immediately when retry button appears (+ slow fallback interval)
- **Focus-safe** — uses `document.hasFocus()` guard to prevent clicking in background tabs
- **Smart target polling** — waits for IDE to be ready instead of failing immediately
- **New tab auto-detection** — monitors for new agent tabs every 30s and injects them automatically
- Toggle on/off from the sidebar
- Uses Chrome DevTools Protocol (CDP) for reliable DOM injection
- Exact-match button labels only ("Retry", "Try Again", "Resume") — no false positives

### 🚀 One-Click Debug Mode

- Restart IDE with debug mode enabled directly from the extension
- Visual indicator showing debug mode status (Active/Inactive)
- Auto Retry toggle is disabled when debug mode is off

---

## 📦 Installation

1. Download the latest `.vsix` file from [Releases](../../releases)
2. Open Antigravity IDE
3. `Cmd+Shift+P` → **"Extensions: Install from VSIX..."**
4. Select the downloaded `.vsix` file
5. Reload the IDE

---

## 🔧 Usage

### Quota Monitoring

The extension activates automatically. Click the **🚀 AGQ** icon in the sidebar to view quota details.

### Auto Retry Setup

1. Open the AGQ sidebar
2. Click **"⟳ Restart with Debug Mode"** (required once)
3. After IDE restarts, toggle **Auto Retry** ON
4. Done! The extension will auto-click Retry whenever the agent fails

> **Note:** You can also start the IDE manually with debug mode:
>
> ```bash
> antigravity --remote-debugging-port=9222
> ```

---

## ⚙️ Settings

| Setting                         | Default | Description                                  |
| ------------------------------- | ------- | -------------------------------------------- |
| `simpleAgq.autoRefreshInterval` | `120`   | Quota refresh interval in seconds (min: 30)  |
| `simpleAgq.showInStatusBar`     | `true`  | Show quota indicator in status bar           |
| `simpleAgq.autoRetryEnabled`    | `false` | Enable auto retry (persists across restarts) |

---

## 🖥️ Platform Support

| Platform                        | Quota Monitor | Auto Retry |     Restart Button      |
| ------------------------------- | :-----------: | :--------: | :---------------------: |
| **macOS Apple Silicon (M1–M4)** | ✅ Optimized  |     ✅     |           ✅            |
| macOS Intel                     |      ✅       |     ✅     |           ✅            |
| Linux (Ubuntu/Debian)           |      ✅       |     ✅     | ⚠️ May need PATH config |
| Windows                         |      ✅       |     ✅     | ⚠️ May need PATH config |

---

## 🔒 Privacy & Security

- **100% local** — no data sent to any server
- Connects only to `127.0.0.1` (your local Antigravity process)
- No telemetry, no analytics, no tracking
- Content-Security-Policy headers on all webview templates
- Input sanitization on all shell commands (prevents injection)
- CDP target filtering — only injects into Antigravity agent tabs
- Open source — inspect the code yourself

---

## 📋 How It Works

```
┌──────────────────────────────────────────────┐
│ Antigravity IDE (Electron)                   │
│                                              │
│  ┌─ Extension Host ──┐  ┌──── UI/DOM ────┐  │
│  │  AGQ Extension     │  │  [Retry] 🔘    │  │
│  │  (quota + retry)   │  │  (Chromium)     │  │
│  └────────┬───────────┘  └───────▲────────┘  │
│           │  1. Connect CDP      │           │
│           │  2. Inject script    │           │
│           │  3. Disconnect ←──── │           │
│           │     (fire & forget)  │           │
│           └──── CDP:9222 ────────┘           │
└──────────────────────────────────────────────┘
```

1. **Quota**: Finds the Antigravity language server process → calls its gRPC API → parses quota data
2. **Auto Retry**: Connects to IDE via CDP → injects a self-running `setInterval` script → **disconnects immediately** → script auto-clicks Retry buttons on its own (no persistent connection needed)

> **💡 What does "inject script" mean?**
>
> The extension injects a tiny, harmless JavaScript snippet into the IDE's own UI (not into any external website or your code). All it does is:
>
> 1. Watch the DOM with `MutationObserver` for instant detection
> 2. When a DOM change occurs, look for buttons labeled exactly **"Retry"**, **"Try Again"**, or **"Resume"**
> 3. If found **and** the tab is currently focused → click it
> 4. A slow `setInterval` (10s) runs as a safety fallback
>
> That's it — no data collection, no network requests, no file access. The script only interacts with Antigravity's own DOM to automate the button click you would do manually.

---

## 📝 Changelog

### v1.2.3
- **Fixed:** Opening new agent tabs no longer causes old tabs to steal focus
- **Architecture:** Fire-and-forget CDP injection — no more persistent WebSocket connections
- **Faster retry:** Hybrid `MutationObserver` + fallback interval — reacts instantly when retry button appears
- **Smarter targeting:** Only injects into Antigravity agent tabs, auto-detects new tabs
- **Security hardening:** CSP headers, input sanitization, CDP error handling, XSS prevention

### v1.2.2
- **Fixed:** "Inject failed. 0 targets []" error when enabling Auto Retry before IDE targets are ready
- Auto Retry now polls for targets (every 5s, up to 5 minutes) instead of failing immediately
- Auto-reconnects when all connections are lost
- Removed dead code in reconnect monitor

### v1.2.1
- Initial Auto Retry with Debug Mode support
- One-click restart with debug mode

### v1.0.0
- Real-time quota monitoring
- Status bar indicator
- Auto-refresh

---

## 📄 License

MIT

---

**Keywords:** antigravity, antigravity-ide, quota, auto-retry, ai-agent, vscode-extension, apple-silicon, m1, m2, m3, m4, macos, retry, automation, ai-coding, windsurf-alternative
