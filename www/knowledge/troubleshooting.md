---
title: Troubleshooting
description: Solutions to common problems users may encounter.
tags:
  - troubleshooting
  - support
---

# Troubleshooting

This article covers solutions to common problems you may encounter while using Rikka Apps products.

## App crashes on start

If an app (Storage Isolation, App Ops, or NoPopping) exits immediately on launch, this is likely the anti-tampering mechanism being triggered. See [Exit on start](./exit_on_start.md) for detailed information.

Common causes include:
- The app was re-signed or modified.
- The app is running in a virtual environment (e.g., parallel space apps).
- An Xposed framework is enabled for the application.

**How to fix:**
1. Make sure the app is downloaded from official channels: Google Play, GitHub releases, or Coolapk (for users in mainland China).
2. Exclude the app from your Xposed framework if you use one.
3. If the issue persists after upgrading from a different channel, send the installed APK to [support@rikka.app](mailto:support@rikka.app) and reinstall from an official source.

## Shizuku not starting

### Via root
- Make sure your device is properly rooted and the root manager (e.g., Magisk) is granting permission to Shizuku.
- Try tapping **Start** again. If it still fails, check if a Magisk module or system modification is interfering.

### Via adb
- Make sure USB debugging is enabled in Developer Options.
- Verify the connection with `adb devices`.
- Make sure you are running the correct start command for your Shizuku version:
  ```shell
  adb shell sh /sdcard/Android/data/moe.shizuku.privileged.api/start.sh
  ```
- If you get a permission denied error, try re-running the command.

## App Ops not detecting Shizuku

- Make sure Shizuku is actually running (check the Shizuku app — it should show "Running").
- Make sure App Ops is updated to the latest version.
- Try restarting App Ops after confirming Shizuku is running.
- If you previously used root mode, switch the working mode to Shizuku in App Ops settings.

## Compatibility issues

### Android version support

| App | Minimum Android Version |
|-----|------------------------|
| Shizuku | Android 6.0 (API 23) |
| App Ops | Android 5.0 (API 21) |
| Storage Isolation | Android 6.0 (API 23) |

### Custom ROM issues

Some custom ROMs modify the Android framework in ways that break the APIs Rikka Apps rely on. If you are using a custom ROM:

- **MIUI**: Some permissions like "Display interface in the background" are disabled by default and need to be manually granted to the Play Store for purchase-related features to work.
- **HyperOS**: Similar to MIUI — check battery and background restrictions for Shizuku.
- **LineageOS / AOSP-based**: Usually work fine, but check if any system-level restrictions are interfering.

### Known issues with Xposed

Xposed-based frameworks (LSPosed, EdXposed, etc.) can interfere with Rikka Apps. If you experience crashes or unexpected behavior:

1. Exclude the Rikka Apps from your Xposed module scope.
2. If the issue persists, disable the Xposed module entirely to confirm it's the cause.
3. Report the issue with details about which Xposed modules you have installed.
