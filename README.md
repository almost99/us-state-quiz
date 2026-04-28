# Olivia's State Quest — Deployment

A US states learning PWA. Open in Chrome on Android and install to home screen
to play fullscreen, offline-capable.

## Files in this folder

- `index.html` — The app
- `manifest.webmanifest` — PWA manifest (tells Chrome it's installable)
- `sw.js` — Service worker (offline support, instant loads)
- `icon-192.png`, `icon-512.png` — Standard icons
- `icon-192-maskable.png`, `icon-512-maskable.png` — Adaptive icons for Android
- `apple-touch-icon.png` — Icon for iOS bookmarks
- `favicon.png` — Browser tab icon

## GitHub Pages setup (≈5 minutes)

### 1. Create a repo

- Go to https://github.com/new
- Name it whatever you like (e.g., `state-quest`)
- Make it Public (Pages requires Public on free accounts) or use GitHub Pro
- Don't add a README, .gitignore, or license

### 2. Push these files

From a terminal in the folder containing `index.html`:

```
git init
git add .
git commit -m "Initial deploy"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/state-quest.git
git push -u origin main
```

### 3. Enable Pages

- In the repo on GitHub, go to **Settings → Pages**
- Under **Source**, select **Deploy from a branch**
- Set branch to `main`, folder to `/ (root)`, and click **Save**
- Wait ~30 seconds. The Pages URL appears at the top of the same page:
  `https://YOUR-USERNAME.github.io/state-quest/`

### 4. Install on Olivia's phone

- Open that URL in Chrome on her Android phone
- Wait for the page to fully load
- Tap the three-dot menu → look for **Install app** or **Add to Home Screen**
  - Chrome may also pop up an install banner on its own after a few seconds
- Tap Install. The State Quest icon lands on her home screen
- Launch from the icon — fullscreen, no address bar, offline-capable

### 5. Updating later

When you want to change the app:
- Edit files locally
- `git add . && git commit -m "Update" && git push`
- GitHub Pages auto-redeploys in ~30 seconds
- The service worker version is set to `state-quest-v1` in `sw.js` —
  to force already-installed phones to pick up changes, bump it to `v2`, etc.

## Troubleshooting

**No "Install app" option in Chrome**: Make sure the page loaded fully, then
refresh once. Chrome needs to fetch the manifest and verify the icons before
showing the install prompt. Some Chrome versions require visiting the page
twice across separate sessions before offering install.

**Icon looks blurry on Android**: Some launchers cache old icons aggressively.
Long-press the home screen icon → remove → reinstall to refresh.

**Want a custom domain**: Add a `CNAME` file with your domain name, then
configure DNS at your registrar to point to `YOUR-USERNAME.github.io`.
