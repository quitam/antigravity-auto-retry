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

### 🔄 Auto Retry (New in v1.2, improved in v1.2.2)

- Automatically clicks **"Retry"** when the AI agent encounters errors
- No more babysitting — zero-click automation
- **Smart target polling** — waits for IDE to be ready instead of failing immediately
- Toggle on/off from the sidebar
- Uses Chrome DevTools Protocol (CDP) for reliable DOM injection
- Works with `MutationObserver` for near-instant detection (~500ms)
- Auto-reconnects if connection is lost

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
- Open source — inspect the code yourself

---

## 📋 How It Works

```
┌──────────────────────────────────────────┐
│ Antigravity IDE (Electron)               │
│                                          │
│  ┌─ Extension Host ──┐  ┌── UI/DOM ──┐  │
│  │  AGQ Extension     │──│  [Retry] 🔘│  │
│  │  (quota + retry)   │  │ (Chromium)  │  │
│  └────────────────────┘  └────────────┘  │
│         │                      ▲         │
│         └──── CDP:9222 ────────┘         │
└──────────────────────────────────────────┘
```

1. **Quota**: Finds the Antigravity language server process → calls its gRPC API → parses quota data
2. **Auto Retry**: Connects to IDE via Chrome DevTools Protocol → injects a DOM observer script → auto-clicks Retry buttons

---

## 📝 Changelog

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
