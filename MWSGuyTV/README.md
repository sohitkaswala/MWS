# MWS Guy TV

A simple full-screen website wrapper for **Android TV / Google TV** that opens
<https://www.mwsguy.sbs/p/home.html>.

- Package: `com.mwsguy.tv`
- Min Android: 5.0 (API 21), target API 34
- Shows on the Android TV / Google TV home screen (Leanback launcher + banner)
- D-pad navigation, Back button goes back in page history (exits at the first page)
- Full-screen immersive mode, screen stays awake, HTML5 fullscreen video supported
- Friendly "Can't reach site / Retry" page when offline

To point the app at a different site, edit `HOME_URL` in
`app/src/main/java/com/mwsguy/tv/MainActivity.java`.

## Project layout

```
MWSGuyTV/
├── build.gradle / settings.gradle / gradle.properties
├── .github/workflows/build-apk.yml      <- cloud build (Option A)
└── app/
    ├── build.gradle
    └── src/main/
        ├── AndroidManifest.xml
        ├── java/com/mwsguy/tv/MainActivity.java
        └── res/ (banner.png, launcher icons, strings, theme)
```

---

## Build the APK

### Option A - No installs: build on GitHub (easiest)

1. Create a free account at <https://github.com> and make a **new repository**.
2. Upload this whole folder to it (drag & drop in the web UI works; make sure the
   `.github` folder is included).
3. Open the **Actions** tab -> **Build APK** -> **Run workflow**
   (it also runs automatically on every push).
4. When the run finishes (about 3-5 min), open it and download the
   **MWS-Guy-TV-apk** artifact. Unzip it -> `app-debug.apk`.

### Option B - Android Studio

1. Install [Android Studio](https://developer.android.com/studio).
2. **File -> Open** and choose this folder; let Gradle sync finish
   (it downloads the SDK pieces it needs).
3. **Build -> Build Bundle(s) / APK(s) -> Build APK(s)**.
4. Click **locate** in the popup. The file is at
   `app/build/outputs/apk/debug/app-debug.apk`.

### Option C - Command line

Requires JDK 17, Android SDK (platform 34 + build-tools) and Gradle 8.7+.

```bash
export ANDROID_HOME=/path/to/android-sdk
gradle assembleDebug
# -> app/build/outputs/apk/debug/app-debug.apk
```

---

## Install on your TV

You must allow installs from unknown sources, then use either method below.

### Method 1 - Downloader app (no computer needed)

1. On the TV install **Downloader** (by AFTVnews) from the Play Store / app store.
2. **Settings -> Device Preferences -> Security & restrictions -> Unknown sources**
   (or Settings -> Apps -> Special app access -> Install unknown apps) -> enable it
   for **Downloader**.
3. Upload `app-debug.apk` to Google Drive/Dropbox/etc. and get a direct download
   link, enter it in Downloader, and install.

### Method 2 - USB drive

1. Copy `app-debug.apk` to a USB stick.
2. Plug it into the TV, open a file manager app (e.g. *File Commander*, *X-plore*)
   and open the APK -> **Install**.

### Method 3 - ADB over Wi-Fi (from a computer)

1. On the TV: **Settings -> Device Preferences -> About** -> click **Build** 7 times
   to enable Developer options. Then **Developer options -> USB/Network debugging** ON.
   Note the TV's IP address (Settings -> Network).
2. On your computer (with [platform-tools](https://developer.android.com/tools/releases/platform-tools) installed):
   ```bash
   adb connect <TV_IP>:5555
   adb install -r app-debug.apk
   ```
3. Accept the "Allow debugging" prompt on the TV the first time.

After installing, find **MWS Guy TV** in your TV's app row (it may be under
"Apps"). If it's missing from the home screen, open it from
**Settings -> Apps -> See all apps**.

## Releasing / Play Store (optional)

The release build is signed with the debug key so it installs by sideloading.
For Google Play, create your own keystore and change `signingConfig` in
`app/build.gradle`. Note that Play requires Android TV apps to meet TV quality
guidelines (and may reject thin website wrappers).

## Troubleshooting

- **"App not installed"** - uninstall any older copy first, or use `adb install -r`.
- **Can't click links with the remote** - use the D-pad to move focus; some pages
  designed for mouse/touch may need a cursor-style app like *Mouse Toggle* for
  Android TV.
- **Blank/black screen** - check the TV's internet connection; Back/Select on the
  retry page reloads the site.
- **Video won't play** - make sure *Android System WebView* is up to date in the
  Play Store.
