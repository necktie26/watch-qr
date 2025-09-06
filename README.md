# Watch QR

A single-file, zero-dependency QR code scanner that works from **screen share** (tab/window/entire screen) or **camera**. It detects QR codes via the native **BarcodeDetector** API, optionally auto-opens detected URLs, and includes copy-to-clipboard, keyboard shortcuts, a gentle beep on detection, and persistent settings.

> **Status:** **Early testing (pre-release).** Functionality and UI may change. **Public release coming soon.**
> **File layout:** everything lives in one file: `watch-qr.html`.
> **Like it?** Please **⭐️ star the repo** — it really helps!

---

## Features

* **Screen / Window / Tab** capture or **Camera** mode (rear camera preferred on mobile).
* **Native decoding** via `BarcodeDetector` with `qr_code` format.
* **Auto-open URLs** with a pre-prepared helper window to avoid popup blockers.
* **Open in this tab** option (uses `location.replace` to avoid history clutter).
* **Copy to clipboard** for any scanned value.
* **Audible beep** on detection (light, short tone).
* **Status badges & accessibility** (ARIA roles, live regions).
* **Keyboard shortcuts:** `S` = Start, `X` / `Esc` = Stop.
* **Persistent settings** in `localStorage` (source, auto-open, open-here).

---

## Quick start

1. Clone or download this repository.
2. Open `watch-qr.html` in a modern browser (Chrome/Edge/Brave/Arc recommended).
   *For **Camera** mode, some browsers require a secure context. If camera access fails when opening the file directly, serve it locally:*

   ```bash
   # any simple static server will do; pick one you already have
   python -m http.server 5173
   # then open http://localhost:5173/watch-qr.html
   ```
3. **Run the scanner in its own browser window (recommended).** Click **Start** and choose the **tab/window/screen** you want to scan (or switch **Source** to **Camera**).
4. After you click **Start**, a helper **tab/window** titled **“Waiting for a QR URL…”** will open. **Return to the scanner window/tab** to continue.
5. You may now **minimize or hide the scanner window** and keep working in the captured tab/window. The scanner keeps running in the background.
6. When a QR code becomes visible in the captured area, the scanner will show it in the results panel and—if **Auto open** is enabled—the helper tab/window will **navigate from “Waiting for a QR URL…” to the detected URL**.

> Tip: Keep **Auto open** on and **Open in this tab** off if you want URLs to open in the helper tab/window automatically.

---

## Permissions

* **Screen capture** uses `navigator.mediaDevices.getDisplayMedia(...)`.
* **Camera capture** uses `navigator.mediaDevices.getUserMedia(...)`.
* Audio is **not** captured. The app creates an `AudioContext` only to play a very short beep after a user gesture.

---

## Browser support

* Designed for **modern Chromium-based browsers**.
* Requires `BarcodeDetector` with `qr_code` support. If unavailable, the UI will show a warning and still allow screen/camera preview, but no decoding can occur.
* Uses `HTMLVideoElement.requestVideoFrameCallback` when available; falls back to a timer loop otherwise.

> No polyfills or external libraries are included.

---

## How it works

1. On **Start**, the app initializes `BarcodeDetector({ formats: ['qr_code'] })`.
2. Depending on **Source**, it requests a video stream from `getDisplayMedia` (screen) or `getUserMedia` (camera) and attaches it to `<video>`.
3. Each frame (or \~200ms fallback), it runs `detector.detect(video)`.
4. The **first QR code** from the result set is read; a short **cooldown** prevents repeated triggers for the same value.
5. * If the value **looks like a URL**, the app:
   * Shows a safe anchor in the results panel.
   * **Auto-opens** it if enabled:

     * **In this tab** via `location.replace(...)`, or
     * **In a prepared window** named `qr_target`, or
     * As a new tab via `window.open(...)` if no helper is available.
   * If the value is **text**, it’s shown as monospaced content.
6. The app plays a short **beep** and updates the **status** and **results** panel.

---

## UI reference

* **Start / Stop:** begin or end capture and scanning.
* **Auto open:** automatically open detected **URLs**.
* **Open in this tab:** replace the current page with the detected URL.
* **Source:** choose **Screen / Window / Tab** or **Camera**.
* **Last result:** shows type (**URL** or **Text**) and the detected value with **Copy** / **Open** actions.
* **Status line:** live feedback (`Ready`, `Scanning…`, `QR detected!`, `Stopped`, warnings/errors).

---

## Accessibility

* Status updates use `role="status"` and `aria-live="polite"`.
* Controls have accessible names, labels, and keyboard shortcuts.
* Focus styles are visible for keyboard users.

---

## Privacy & security

* **No network calls** or analytics. Everything runs entirely in the browser.
* QR values are **not persisted**; only user preferences (auto-open, open-here, source) are saved to `localStorage`.
* Auto-open uses a **prepared window** (opened by a user gesture) to reduce popup-blocker issues.
* Opening in the current tab uses `location.replace` to avoid polluting history.
* Screen/camera streams are stopped when you click **Stop** or when the track ends.

---

## Troubleshooting

* **“BarcodeDetector is not available”**
  Update your browser or use a Chromium-based browser that supports `BarcodeDetector` with `qr_code`.

* **Camera mode fails on `file://`**
  Serve the file over `http(s)` (see **Quick start**) due to secure-context requirements.

* **No beep sound**
  Ensure you’ve interacted with the page (user gesture) and your device isn’t muted.

* **Auto-open didn’t open**
  Popup blockers can still intervene. Keep **Auto open** on; don’t check **Open in this tab**; allow popups for the site if prompted.

* **Multiple QR codes in view**
  The app reads the **first** detected code in each frame. Move or zoom to isolate the target code.

---

## Contributing

I’m open to **issues** and **pull requests** — bug reports, feature proposals, and improvements are welcome.
If this project helps you, **please star the repo ⭐️** — it’s the easiest way to support development.

* Keep the **single-file, zero-dependency** approach.
* Use **English** for UI text and code.
* Prefer **safe DOM updates** (avoid `innerHTML` for untrusted data).
* Test on recent Chromium browsers; include notes for edge cases.

---

## Roadmap (ideas)

* Domain **allowlist (whitelist)** for auto-open: only URLs from approved domains are opened; all other links are ignored.
* Option to **pause/resume** detection without stopping the stream.
* **Multi-code** listing per frame.
* Visual **scan region overlay** and zoom controls.
* Optional **vibration** on mobile.
* Fallback decoder (e.g., WebAssembly) for non-supporting browsers (would introduce a dependency).

---

## License

Add your preferred license (e.g., MIT) in a `LICENSE` file.

---

## Acknowledgements

* Uses the browser’s **Media Capture** and **BarcodeDetector** APIs.
