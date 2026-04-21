# DocScan to Sheets — PWA Setup Guide

## What this app does
- 📷 Open camera → photograph any document
- 🔍 OCR runs **100% offline** (Tesseract.js, no data sent anywhere)
- ✏️ Review & edit extracted fields before uploading
- 📊 Appends a row to your Google Sheet automatically
- ☁️ Offline queue — saves locally and uploads when back online
- 📱 Installable on Android as a home screen app

---

## Step 1 — Get a Google OAuth Client ID (5 minutes)

1. Go to https://console.cloud.google.com
2. **Create a new project** (or select existing)
3. Go to **APIs & Services → Library**
4. Search and **enable "Google Sheets API"**
5. Go to **APIs & Services → OAuth consent screen**
   - Choose **External**, fill in app name, support email
   - Add scope: `https://www.googleapis.com/auth/spreadsheets`
   - Add your email as a test user
6. Go to **APIs & Services → Credentials → Create Credentials → OAuth 2.0 Client ID**
   - Application type: **Web application**
   - Name: DocScan PWA
   - Authorized JavaScript origins: add your hosting URL (e.g. `https://yourdomain.com`)
   - Authorized redirect URIs: same URL
7. Copy the **Client ID** (looks like `123456789-abc.apps.googleusercontent.com`)

---

## Step 2 — Add your Client ID to the app

Open `index.html` and find line ~370:

```js
const GOOGLE_CLIENT_ID = 'YOUR_GOOGLE_CLIENT_ID.apps.googleusercontent.com';
```

Replace with your actual Client ID.

---

## Step 3 — Host the app (choose one)

### Option A: GitHub Pages (free, easiest)
```bash
# Create a repo, push these files, enable GitHub Pages
git init && git add . && git commit -m "DocScan PWA"
git remote add origin https://github.com/YOURNAME/docscan.git
git push -u origin main
# Enable Pages in repo Settings → Pages → main branch
```

### Option B: Netlify (drag & drop)
- Go to https://app.netlify.com
- Drag the `docscan-pwa` folder onto the page
- Done — you get an instant HTTPS URL

### Option C: Local (for testing)
```bash
# Requires HTTPS for camera API — use ngrok:
npx serve .          # or: python3 -m http.server 8080
ngrok http 8080      # gives you an https:// tunnel URL
```

---

## Step 4 — Install on Android

1. Open the hosted URL in **Chrome on Android**
2. Tap the **3-dot menu → Add to Home screen**
3. Or: Chrome will show an **"Install app"** banner automatically

The app will appear on your home screen like a native app.

---

## Step 5 — Configure your Google Sheet

1. Open the app → **Sign in with Google**
2. Tap **Configure** → paste your **Spreadsheet ID**
   - Find it in the Sheet URL: `docs.google.com/spreadsheets/d/SPREADSHEET_ID/edit`
3. Tap **Verify** to confirm access
4. Set the **sheet tab name** (default: Sheet1)
5. Configure **column mappings**:
   - **Field label** = name shown in the review screen
   - **Column** = Google Sheets column letter (A, B, C…)
   - **OCR keyword** = word near the value in the document (e.g. "invoice", "total", "date")
6. Tap **Save**

---

## Using the app

1. Tap **Scan Document** → camera opens
2. Point camera at document → tap shutter button
3. Wait a few seconds for OCR to run (offline)
4. Review extracted fields — edit anything wrong
5. Tap **Upload to Sheet**
6. Done! Check your Google Sheet for the new row.

**Offline:** If you have no internet, tap Upload anyway — it saves to a local queue. When you come back online, tap **Sync** or it syncs automatically.

---

## Files

```
docscan-pwa/
├── index.html      ← entire app (HTML + CSS + JS)
├── manifest.json   ← PWA manifest
├── sw.js           ← service worker (offline caching)
└── icons/
    ├── icon-192.png
    └── icon-512.png
```

## Privacy
- OCR runs entirely on-device using Tesseract.js
- Images are never uploaded anywhere
- Only the extracted text values are sent to Google Sheets
- Google sign-in token is stored in localStorage on your device only
