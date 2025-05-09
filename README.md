# apk-transfer-kit: APK Extract & Install Toolkit for ADB

> _Originally built for Quest 3, now a general-purpose Android APK extraction and transfer tool._

Built to make it easy to extract APKs installed directly from the Play Store and sideload split apps onto your Quest 3, or any Android device, securely and efficiently.

This friendly CLI toolkit helps you pull apps from one device or an emulator (like BlueStacks) and install them onto another (like your Quest 3), without relying on third-party APK sources.

Install a single app or an entire list with one command. Just provide the names, sit back, and let the script:

Extract, Prepare, and Install. All in one go.

## 📑 Table of Contents

- [🌟 Why I Made This](#-why-i-made-this)
- [🛠 Requirements](#-requirements)
- [🚀 How to Use It](#-how-to-use-it)
  - [1. Extract and Install APKs](#1-extract-and-install-apks)
  - [2. Check If an App Is Installed](#2-check-if-an-app-is-installed)
  - [3. Compare Apps Across Devices](#3-compare-apps-across-devices)
- [🚧 How It Works (For the Curious)](#-how-it-works-for-the-curious)
- [Quest 3 Specific](#quest-3-specific)
  - [🧰 Enabling Developer Mode on Your Devices](#-enabling-developer-mode-on-your-devices)
    - [🔹 BlueStacks (Source Device)](#-bluestacks-source-device)
    - [🔹 Meta Quest 3 (Target Device)](#-meta-quest-3-target-device)
  - [🚫 Known Apps That Do Not Work on Quest 3 Without Google Play Services](#-known-apps-that-do-not-work-on-quest-3-without-google-play-services)
    - [❌ Apps that fail to launch or require Play Services:](#-apps-that-fail-to-launch-or-require-play-services)
    - [📌 Reason](#-reason)
    - [✅ Workarounds](#-workarounds)
- [Contribution](#contribution)
---

## 🌟 Why I Made This

I made this for myself because I want the peace of mind and security of using apps pulled directly from the official Google Play Store, without relying on any third-party APK sites or middlemen. But you're welcome to use it or extend it for your own workflows.

Extracting and sideloading APKs — especially split APKs — can be a real pain, especially when using emulators like BlueStacks. This tool streamlines that process:

- Manually downloading Android apps with Google Play Store using BlueStacks
- Extracting all split APKs and sideload them onto my Quest 3.
- Bonus: I wanted a reliable CLI interface with good UX — helpful messages, automatic folder creation, and clean adb integration.

---

## 🛠 Requirements

- **Python 3.7 or higher**  
  This toolkit uses modern Python features like f-strings and Pathlib.  
  Make sure Python 3 is installed.

- **ADB (Android Debug Bridge)**  
  `adb` must be installed and available in your system PATH.  
  (On Windows, ensure `adb.exe` can be called as just `adb` from the command line. Let me know if that does not work.)

- **Connect Devices**  
  Your source and target devices must appear in `adb devices` before running the script.

---

## 🚀 How to Use It

> 💡 Tip: Run `adb devices` to list available connected devices and get their IDs for use in the commands below.

### 1. Extract and Install APKs

Use `apk-extract-and-install-with-adb.py` to extract APKs from one device and optionally install to another:

```bash
python apk-extract-and-install-with-adb.py \
  --source-device-id <source-device-id> \
  --target-device-id <target-device-id> \
  --install \
  --verbose \
  --partial-app-names "<app-name-1 app-name-2 app-name-3...>"
```

Note: `--partial-app-names` takes in
- one or more app names, i.e. `"com.calm.android com.pinterest com.reddit.frontpage"`
- or even part of apps' names, i.e. `"calm pinterest reddit"`
- or the output from the `compare-apps-across-devices.py`, i.e.

```
  - com.calm.android
  - com.pinterest
  - com.reddit.frontpage
```
  
This will:

- Go through the list of apps
- Search for a package containing `app-name`, e.g. `calm/reddit/pinterest`, on your source device
- Pull all split APKs
- Save them to `~/Documents/APKs/`
- Install them to your target device

You’ll be prompted to select a package if multiple matches are found.

### 2. Check If an App Is Installed

Use `check-apk-installed-on-device.py` to check if a device already has an app installed:

```bash
python check-apk-installed-on-device.py \
  --target-device-id <target-device-id> \
  --verbose \
  --print-all \
  --partial-app-name <app-name>
```

### 3. Compare Apps Across Devices

Use `compare-apps-across-devices.py` to compare user-installed apps between two devices and find out what’s missing or extra.

```bash
python compare-apps-across-devices.py \
  --source-device-id <source-device-id> \
  --target-device-id <target-device-id> \
  --verbose
```

This will:

- Help you identify which apps to extract and install to keep both devices in sync
- List apps installed only on the source (e.g., BlueStacks)
- Optionally list apps installed only on the target (e.g., Quest 3) if you specify `--show-extra-apps-on-target`

---

## 🚧 How It Works (For the Curious)

- Uses `adb shell pm list packages` and `adb shell pm path` to find and pull APKs.
- Handles split APKs automatically and installs with `adb install-multiple`.
- Prompts you for package selection if fuzzy matching returns more than one result.
- Logs are clean and color-coded (dim adb commands, colored UX prompts).
- All APKs are saved in `~/Documents/APKs/{package.name}/` by default.

## Quest 3 Specific

### 🧰 Enabling Developer Mode on Your Devices

Before using the scripts, make sure both source and target devices have developer mode enabled. I am using BlueStacks as my source device here.

#### 🔹 BlueStacks (Source Device)

1. Open BlueStacks and go to **Settings**.
2. Navigate to the **Advanced** tab.
3. Toggle **Android Debug Bridge (ADB)** to **On**.
4. Click **Save changes**.
5. Connect using:
   ```bash
   adb connect localhost:5555
   ```

👉 [BlueStacks ADB Setup Guide](https://support.bluestacks.com/hc/en-us/articles/23925869130381-How-to-enable-Android-Debug-Bridge-on-BlueStacks-5?utm_source=chatgpt.com)

#### 🔹 Meta Quest 3 (Target Device)

1. Visit the [Meta Developer Portal](https://developer.oculus.com/manage/organizations/create/) and create an organization.
2. Verify your Meta account (credit card or 2FA).
3. Open the **Meta Quest app** on your phone:
   - Tap **Menu** → **Devices** → Select your headset
   - Tap **Headset Settings** → **Developer Mode**
   - Toggle **Developer Mode** to **On**, then restart your headset

👉 [Quest 3 Developer Mode Guide](https://knowledge.matts-digital.com/en/virtual-reality/meta/meta-quest-3/how-to-enable-developer-mode-on-the-meta-quest-3/?utm_source=chatgpt.com)

---

### 🚫 Known Apps That Do Not Work on Quest 3 Without Google Play Services

These apps have been tested and are known **not to function correctly** when sideloaded onto a Quest 3 or other Android devices without Google Play Services and Play Store installed:

#### ❌ Apps that fail to launch or require Play Services:

- **Apple TV** — shows a "requires Play Store" error message
- **Binge** — shows a sign-in screen but shows a "requires Play Store" error message after logging in...
- **ChatGPT** — shows a "requires Play Store" error message
- **Kayo Sports** — shows a "requires Play Store" error message
- **YouTube** — shows a "requires Play Store" error message

#### 📌 Reason

These apps rely heavily on:

- Google Play Services APIs
- DRM or secure playback infrastructure
- Google account sign-in and licensing mechanisms

Because the Quest 3 does not ship with Google Mobile Services (GMS), these apps either crash or refuse to operate.

#### ✅ Workarounds

- Use web versions through the Oculus Browser (e.g. youtube.com, tv.apple.com)

> ℹ️ Installing Play Store or Google Play Services manually on the Quest 3 is **not recommended** and is unlikely to work without root, signature spoofing, or custom ROMs.


---
## Contribution

PRs and suggestions welcome!

---

MIT No Attribution License · Created with ❤️ by someone who loves their Quest 3 &
ChatGPT!

