# Xbox Cloud Gaming Fix for Brave Browser

A lightweight Manifest V3 browser extension that resolves the persistent **blank white screen** bug when loading Xbox Cloud Gaming (`play.xbox.com`) in **Brave Browser**.

---

## 🔍 The Problem & Root Cause

When navigating to Microsoft's new cloud gaming experience (`play.xbox.com`), the server returns an initial 15-byte placeholder body (`<!doctype html>`) accompanied by a `Critical-CH` (Critical Client Hints) response header:

```http
critical-ch: Sec-CH-Forced-Colors, Sec-CH-Prefers-Contrast, Sec-CH-Prefers-Reduced-Transparency, Sec-CH-Prefers-Color-Scheme, Sec-CH-Viewport-Width, Sec-CH-Viewport-Height
```

* Standard Chromium and Edge browsers immediately restart navigation with these client hints populated.
* **Brave's privacy engine deliberately disables automatic navigation retries on `Critical-CH`** to prevent fingerprinted tracking on initial top-level page loads.
* As a result, Brave displays that empty 15-byte HTML file (`bodyLength: 0`, empty title, zero scripts), rendering a permanent blank white page. Toggling Brave Shields off does not fix this because it occurs at the network navigation level before Shields take effect.

### The Fix

When the initial request **does not advertise `sec-ch-ua` (Client Hints support)**, Microsoft's server falls back to standard HTTP negotiation and immediately serves the complete, fully-rendered web application (240+ KB) instead of the 15-byte placeholder.

This extension uses Chrome's high-performance `declarativeNetRequest` API to strip `sec-ch-ua`, `sec-ch-ua-mobile`, and `sec-ch-ua-platform` **only** for `play.xbox.com`, letting Brave load the dashboard seamlessly.

---

## 🚀 Installation Guide

### Option 1: Load Unpacked (Instant)

1. Clone or download this repository:
   ```bash
   git clone https://github.com/ijlaal1610/xbox-cloud-brave-fix.git
   ```
2. Open Brave and navigate to:
   ```text
   brave://extensions
   ```
3. Toggle **Developer mode** on (top-right switch).
4. Click **Load unpacked** (top-left button).
5. Select the downloaded `xbox-cloud-brave-fix` directory.
6. Navigate to `https://play.xbox.com/` and enjoy!

---

## 🔒 Permissions & Privacy

This extension does not run background tracking scripts, requires no external network connections, and reads no personal data:
* **`declarativeNetRequest`**: Strips `sec-ch-ua` headers locally at the browser network layer.
* **Host Permissions**: Restricted strictly to `*://play.xbox.com/*`.

---

## 📄 License

MIT License. See [LICENSE](LICENSE) for details.
