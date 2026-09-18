# Pokédex APK — cloud build

This folder is a complete Capacitor project. GitHub's servers do the heavy
lifting — you just upload, click, and download the finished APK.

## What you need

- A free GitHub account (sign up at github.com — works from the library computer or your phone)
- This folder's contents (everything except `node_modules` — leave that out, the server installs it)

## Steps (library computer)

1. **Create a repo:** on github.com → New repository → name it `pokedex-apk` → Create.
2. **Upload:** click "uploading an existing file", then drag in EVERYTHING from
   this folder — `www/`, `android/`, `.github/`, `package.json`,
   `package-lock.json`, `capacitor.config.json`, `.gitignore`.
   (If the drag-and-drop skips the `.github` folder, see the fallback below.)
3. **Commit**, then open the **Actions** tab → click **Build Pokédex APK** →
   **Run workflow** → wait about 5–10 minutes.
4. **Download:** when it finishes, open the run → download the `pokedex-apk`
   artifact (it's a zip — unzip it to get `app-debug.apk`).
5. **To your phone:** USB cable, Google Drive, email it to yourself — whatever's
   easiest. Or skip the computer entirely and download the artifact straight
   from github.com in your phone's browser.
6. **Install:** open the APK on your phone → allow "install unknown apps" when
   asked → Install. Android will warn it's from an unknown developer — that's
   normal, it's your own build.

Done — the Pokédex is now a real installed app with your red handheld icon.

## If the `.github` folder didn't upload

GitHub's web uploader sometimes skips dot-folders. No problem — create the
workflow by hand:

1. In your repo: **Add file → Create new file**.
2. Name it exactly: `.github/workflows/build-apk.yml`
3. Paste in the entire contents of the `build-apk.yml` file from this folder
   (it's also below), then **Commit**.

Then go to Actions and run it as above.

### Workflow file contents (for manual paste)

```yaml
name: Build Pokédex APK

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Check out code
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Set up Java 17
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17

      - name: Install dependencies
        run: npm ci

      - name: Sync web code into the Android project
        run: npx cap sync android

      - name: Build debug APK
        run: cd android && chmod +x gradlew && ./gradlew assembleDebug

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: pokedex-apk
          path: android/app/build/outputs/apk/debug/app-debug.apk
```

## Notes

- This builds a **debug** APK — perfect for installing on your own phone. The
  Play Store would need a release build + developer account ($25); that's a
  later step if you ever want it.
- The app's data (214 Pokémon, checklist, photos) works fully offline. Camera
  identify and narration need internet (they call Google's APIs).
- Your Gemini API key is entered inside the app — it's never baked into these files.
- If you ever get your own computer: `npm install`, then `npx cap open android`
  builds the same APK locally in Android Studio.
