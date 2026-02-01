# MuffinStore Quickstart

Get MuffinStore running on your device or build it from source in a few steps.

---

## Option A: Install a Prebuilt IPA

### 1. Download the IPA

Go to the [GitHub Actions page](https://github.com/mineek/MuffinStore/actions), open the latest successful workflow run, and download the **MuffinStore IPA** artifact.

### 2. Transfer to your device

Send the `.ipa` file to your iOS device using AirDrop, a file manager, or any method you prefer.

### 3. Install with TrollStore

Open the IPA in [TrollStore](https://github.com/opa334/TrollStore). TrollStore will handle code signing and installation.

### 4. Launch

Open MuffinStore from your home screen. You're ready to go.

---

## Option B: Build from Source

### 1. Install Theos

Follow the official [Theos installation guide](https://theos.dev/docs/installation) for macOS. Make sure the `THEOS` environment variable is set:

```bash
echo $THEOS
# Should print something like /opt/theos
```

### 2. Verify Xcode

MuffinStore targets iOS 16.5 SDK. Confirm you have a compatible Xcode version installed:

```bash
xcodebuild -version
# Xcode 16.2.0 or similar
```

If you have multiple Xcode versions, select the right one:

```bash
sudo xcode-select -s /Applications/Xcode_16.2.0.app/Contents/Developer
```

### 3. Clone and build

```bash
git clone https://github.com/mineek/MuffinStore.git
cd MuffinStore
FINALPACKAGE=1 gmake clean package
```

The output IPA will be in the `packages/` directory.

### 4. Install

Transfer `packages/MuffinStore.ipa` to your device and install it with TrollStore (same as Option A, steps 2-4).

---

## Using MuffinStore

### Download an older version of any app

1. Open MuffinStore.
2. Tap **Download**.
3. Paste an App Store link (e.g. `https://apps.apple.com/app/id123456789`).
4. Choose how to specify the version:
   - **Server** -- fetches a list of available versions from an API. Pick the version you want from the list.
   - **Manual** -- enter a version identifier directly. You can obtain these from tools like [ipatool-py](https://github.com/ipatool/ipatool).
5. The download begins through the App Store. Check your home screen for progress.

### Downgrade an installed app

1. Open MuffinStore.
2. Scroll down to the **Installed Apps** section.
3. Tap any app in the list.
4. Choose **Server** or **Manual** to select a version.
5. The older version downloads and replaces the current installation.

---

## Prerequisites Checklist

| Requirement | Details |
|---|---|
| iOS version | 15.0 or later |
| Device | iPhone or iPad (ARM64) |
| TrollStore | Installed and functional |
| Apple ID | Signed into the App Store; the target app must be in your purchase history |

---

## Troubleshooting

| Problem | Solution |
|---|---|
| **"No results" error** | The App Store link may be invalid, or the app was removed from the store. Double-check the URL contains a valid `id` parameter. |
| **"No version IDs" from server** | The external version history API may not have data for this app. Use the **Manual** method instead. |
| **Download doesn't start** | Make sure you are signed into the App Store with the Apple ID that has the app in its purchase history. |
| **Build fails with "SDK not found"** | Verify Xcode is installed and `xcode-select` points to the correct Xcode version. Confirm Theos is properly configured. |
| **`gmake` not found** | Install GNU Make via Homebrew: `brew install make`. The command is `gmake` on macOS. |
