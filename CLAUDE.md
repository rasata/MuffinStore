# CLAUDE.md

## Project Overview

MuffinStore is an iOS jailbreak utility (Objective-C) that lets users download and install older versions of App Store apps via private Apple APIs and TrollStore integration.

## Build System

- **Framework:** [Theos](https://theos.dev) (jailbreak toolchain)
- **Target:** iPhone, iOS 15.0+, ARM64, clang 16.5
- **Output:** IPA package

### Build Commands

```bash
# Full build
gmake clean package

# Release build
FINALPACKAGE=1 gmake clean package
```

Theos must be installed and configured before building. Requires Xcode 16.2.0.

## Project Structure

```
MuffinStore/
├── main.m                      # App entry point
├── MFSAppDelegate.h/m          # App delegate, window/nav setup
├── MFSRootViewController.h/m   # Main UI and all download logic
├── CoreServices.h              # Private framework headers (LSApplicationProxy, etc.)
├── Makefile                    # Theos build config
├── control                     # Debian package metadata
├── entitlements.plist          # Code signing entitlements
├── Resources/
│   ├── Info.plist
│   └── AppIcon*.png
└── .github/workflows/build.yml # CI: builds IPA on push to main
```

## Architecture

- **Pattern:** MVC with Preferences framework UI (PSListController)
- **Key class:** `MFSRootViewController` — contains all app logic (download flow, API calls, installed app listing)
- **Private frameworks used:** Preferences, StoreKitUI, CoreServices (private headers in `CoreServices.h`)
- **Networking:** NSURLSession for iTunes Lookup API and version history API (`apis.bilin.eu.org`)

## Code Conventions

- Objective-C with ARC (`-fobjc-arc`)
- Class prefix: `MFS`
- UI built programmatically via PSSpecifier (Preferences framework)
- Alert-driven user flow (UIAlertController)
- No tests
