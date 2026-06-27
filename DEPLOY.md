# Xiaozhi AI Assistant — Firmware Flasher
## Complete GitHub Pages Deployment Guide

---

## Folder Structure

Your repository must look exactly like this:

```
your-repo/
├── index.html          ← main flasher page
├── manifest.json       ← ESP Web Tools manifest
└── firmware/
    ├── bootloader.bin
    ├── partition-table.bin
    ├── xiaozhi.bin
    └── generated_assets.bin
```

---

## Step 1 — Create a GitHub Repository

1. Go to https://github.com and sign in.
2. Click **New** (top-left green button).
3. Name your repo, e.g. `xiaozhi-flasher`.
4. Set visibility to **Public** (GitHub Pages requires public repos on free plans).
5. Click **Create repository**.

---

## Step 2 — Upload Your Files

### Option A — GitHub Web Upload (easiest)

1. Open your new repo on GitHub.
2. Click **Add file → Upload files**.
3. Upload `index.html` and `manifest.json`.
4. Click **Commit changes**.
5. Now create the `firmware/` folder:
   - Click **Add file → Create new file**.
   - Type `firmware/.gitkeep` in the filename box.
   - Commit it. This creates the folder.
6. Open the `firmware/` folder, click **Add file → Upload files**.
7. Upload all four `.bin` files.
8. Commit changes.

### Option B — Git CLI

```bash
git clone https://github.com/your-username/xiaozhi-flasher.git
cd xiaozhi-flasher

# Copy your files in
cp /path/to/index.html .
cp /path/to/manifest.json .
mkdir firmware
cp /path/to/*.bin firmware/

git add .
git commit -m "Add flasher page and firmware"
git push
```

---

## Step 3 — Enable GitHub Pages

1. In your repo, go to **Settings** (top tab).
2. Scroll down to **Pages** in the left sidebar.
3. Under **Source**, select **Deploy from a branch**.
4. Set branch to `main` (or `master`), folder to `/ (root)`.
5. Click **Save**.
6. Wait 1–2 minutes.
7. GitHub will show your URL:  
   `https://your-username.github.io/xiaozhi-flasher/`

---

## Step 4 — Verify Everything Works

### Check manifest.json

Open this URL in your browser (replace with your actual URL):

```
https://your-username.github.io/xiaozhi-flasher/manifest.json
```

You should see the raw JSON. If you get a 404, GitHub Pages is not yet enabled or the file path is wrong.

### Check firmware files

```
https://your-username.github.io/xiaozhi-flasher/firmware/bootloader.bin
```

This should download the file. If 404, the firmware folder path is wrong.

### Test flashing

1. Open `https://your-username.github.io/xiaozhi-flasher/` in **Chrome or Edge**.
2. Connect your ESP32-S3 via USB.
3. Enter bootloader mode: hold BOOT, tap RESET, release BOOT.
4. Click **Flash Firmware**.
5. Select the COM port in the browser dialog.
6. Wait for completion (~30–90 seconds).
7. Press RESET on the board.

---

## Common Mistakes to Avoid

| Mistake | Fix |
|---------|-----|
| Offsets in manifest.json without quotes | Must be `"0x0"` not `0x0` |
| Wrong firmware folder path | Must be `firmware/filename.bin` — case-sensitive |
| Repo is private | Set repo to Public, or upgrade to GitHub Pro |
| Using Firefox or Safari | Must use Chrome or Edge (Web Serial API) |
| Charge-only USB cable | Use a data cable — many phone cables are charge-only |
| Skipping bootloader mode | Hold BOOT + tap RESET before flashing |
| GitHub Pages not enabled | Go to Settings → Pages and enable it |

---

## Your Final URL

```
https://your-username.github.io/xiaozhi-flasher/
```

Share this URL with anyone who needs to flash the firmware. No installation required on their end — just Chrome/Edge and a USB cable.

---

## Manual Bootloader Mode (ESP32-S3)

If the auto-reset doesn't work:

1. Hold the **BOOT** button (GPIO 0) on your board.
2. While holding BOOT, press and release the **RESET** (EN) button.
3. Release BOOT.
4. The board is now in download mode.
5. Flash the firmware.
6. Press RESET again to boot normally.

---

## USB Driver Installation

**Windows:**
- Try flashing first — Windows 10/11 usually detects ESP32-S3 automatically.
- If COM port doesn't appear: install [CP210x Driver](https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers) or [CH340 Driver](https://www.wch-ic.com/downloads/CH341SER_EXE.html) depending on your board's USB chip.
- Check Device Manager → Ports (COM & LPT) to see if the port appears.

**macOS:**
- macOS 12+ usually works without drivers.
- For older macOS or CH340-based boards: install [CH340 macOS driver](https://github.com/adrianmihalko/ch340g-ch34g-ch34x-mac-os-x-driver).

**Linux:**
- ESP32-S3 usually works out of the box.
- If permission denied: run `sudo usermod -aG dialout $USER` then log out and back in.

---

*Guide by kamilhassan · Xiaozhi AI Assistant Project*
