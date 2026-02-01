# MuffinStore

An experimental App Store app downgrader and installer for iOS, designed to work with [TrollStore](https://github.com/opa334/TrollStore).

MuffinStore allows you to download and install older versions of App Store applications on your iOS device. It leverages private Apple frameworks and StoreKit APIs to initiate downloads of specific app versions directly through the App Store infrastructure.

## Features

- **Download older app versions** by entering an App Store link and selecting the desired version
- **Browse installed apps** directly within MuffinStore and quickly initiate a downgrade for any of them
- **Two version lookup methods:**
  - **Manual entry** -- enter a version identifier obtained from tools like [ipatool-py](https://github.com/ipatool/ipatool) or similar sources
  - **Server-based lookup** -- fetch a list of available version identifiers from an external API and pick from a list
- **iPad support** with proper popover presentation for action sheets
- **TrollStore integration** for seamless app installation

## Privacy Note

When using the server-based version lookup, the app sends the target app's App Store ID to an external API (`apis.bilin.eu.org`) to retrieve version history. If you consider this a privacy concern, you can use the manual entry method instead, which only communicates with Apple's App Store servers.

## Requirements

- iOS 15.0 or later
- ARM64 device (iPhone or iPad)
- [TrollStore](https://github.com/opa334/TrollStore) installed on the device
- A valid Apple ID signed into the App Store (the app must have been previously "purchased"/downloaded on your account)

## Installation

Download the latest IPA from [GitHub Actions artifacts](https://github.com/mineek/MuffinStore/actions) or build it yourself (see below), then install via TrollStore.

## Building from Source

### Prerequisites

- macOS with [Theos](https://theos.dev/docs/installation) installed and configured
- Xcode 16.2.0 (or compatible version with iOS 16.5 SDK)

### Build Steps

```bash
# Clone the repository
git clone https://github.com/mineek/MuffinStore.git
cd MuffinStore

# Build a release IPA
FINALPACKAGE=1 gmake clean package
```

The built IPA will be placed in the `packages/` directory.

### CI/CD

The project includes a GitHub Actions workflow (`.github/workflows/build.yml`) that automatically builds the IPA on every push to `main`. Build artifacts are uploaded and available for download from the Actions tab. The workflow runs on macOS 15 with Theos and Xcode 16.2.0.

## Project Structure

```
MuffinStore/
├── main.m                        # Application entry point
├── MFSAppDelegate.h/m            # App delegate -- creates window and navigation controller
├── MFSRootViewController.h/m     # Main view controller -- all UI and download logic
├── CoreServices.h                # Private framework interface declarations
│                                   (LSApplicationProxy, LSApplicationWorkspace,
│                                    LSEnumerator, MCMContainer)
├── Makefile                      # Theos build configuration
├── control                       # Debian package metadata
├── entitlements.plist            # Code signing entitlements for system-level access
├── Resources/
│   ├── Info.plist                # App bundle configuration
│   └── AppIcon*.png              # App icons for all device sizes
└── .github/workflows/build.yml   # GitHub Actions CI workflow
```

## How It Works

1. **App selection** -- The user either pastes an App Store link or taps on an installed app from the list.
2. **App ID resolution** -- For App Store links, the app ID is parsed from the URL. For installed apps, the bundle ID is looked up via the iTunes Search API to resolve the numeric App Store ID.
3. **Version selection** -- The user chooses between entering a version identifier manually or fetching available versions from the server API. The server returns a list of historical versions with their external identifiers.
4. **Download initiation** -- MuffinStore constructs a purchase request using private StoreKit classes (`SKUIItemStateCenter`, `SKUIItem`, `SKUIItemOffer`) and submits it through the StoreKit purchase flow. The App Store handles the actual download and installation.

## Technical Details

- **Language:** Objective-C with ARC
- **UI framework:** Uses Apple's private `Preferences` framework (`PSListController`, `PSSpecifier`) for a system settings-style interface
- **Private frameworks:** `Preferences`, `StoreKitUI`, `CoreServices` (private headers)
- **Architecture:** MVC -- a single view controller (`MFSRootViewController`) manages the entire flow
- **Entitlements:** Requires extensive system entitlements including no-sandbox execution, filesystem access, app installation/uninstallation capabilities, and private iTunes Store API access. These are granted through TrollStore's code signing.

## License

This project is licensed under the [GPL-3.0 License](LICENSE).

## Support

Everything is free and open source. If you'd like to support the developer, consider donating on [Ko-fi](https://ko-fi.com/mineekdev).
