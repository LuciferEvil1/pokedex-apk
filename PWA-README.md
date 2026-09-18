# Pokédex PWA → APK roadmap

This folder is your Pokédex as an install-ready web package:

- `index.html` — the full app (214 Pokémon, matchups, checklist, camera identify, Ask the Dex)
- `manifest.json` — tells Android this is an installable app (name, icons, fullscreen)
- `sw.js` — service worker so the home-screen install works offline
- `icon-192.png` / `icon-512.png` / `icon-180.png` — your red handheld icon, sized for Android/iOS

## Right now: home-screen install (no computer needed)

1. Host this folder on any static host over HTTPS (GitHub Pages, Netlify, etc.).
2. Open the URL in Chrome on your phone → menu → **Add to Home screen**.
3. It installs with your icon and opens fullscreen, like a native app.

## Later: real APK with Capacitor (needs a computer)

Prereqs on the computer: Node.js (LTS) and Android Studio.

```bash
# 1. Set up a project and point Capacitor at this folder
mkdir pokedex-apk && cd pokedex-apk
cp -r /path/to/pokedex-pwa www
npm init -y
npm install @capacitor/core @capacitor/cli

# 2. Initialize Capacitor (app name, your own app id)
npx cap init Pokedex com.ahkeem.pokedex --web-dir=www

# 3. Add Android and sync the web code in
npx cap add android
npx cap sync

# 4. Open in Android Studio and build the APK
npx cap open android
# In Android Studio: Build → Generate Signed Bundle / APK → Android App Bundle or APK
# Create a keystore when asked and KEEP IT SAFE — you need the same one for every update.
```

Then transfer the APK to your phone and install it (allow "install unknown apps" for your file manager when asked).

Notes:
- The service worker (`sw.js`) isn't needed inside Capacitor — the files ship inside the APK, so offline works automatically.
- The camera-identify and narration features call Google's APIs, so they need internet even in the APK. The Pokédex data itself works fully offline.
- Publishing to the Play Store later needs a $25 Google Play developer account, the same keystore, and a privacy policy.
- Your Gemini API key stays yours: it's entered in the app and never baked into these files.
