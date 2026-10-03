---
title: FAQ
description: Frequently asked questions about Rikka Apps products.
tags:
  - faq
---

# Frequently Asked Questions

## General

### Is root required?

It depends on the app:

| App | Root required? | Alternative |
|-----|----------------|-------------|
| Shizuku | No | Can use adb to start |
| App Ops | No | Can use Shizuku or adb |
| Storage Isolation | **Yes** | No alternative available |

Shizuku and App Ops are designed to work without root by using adb. Storage Isolation fundamentally requires root because it hooks into the storage layer at a level that only root can access.

### Which Android versions are supported?

- **Shizuku**: Android 6.0 (API 23) and above.
- **App Ops**: Android 5.0 (API 21) and above.
- **Storage Isolation**: Android 6.0 (API 23) and above.

Older Android versions are not supported because the system APIs these apps rely on were introduced in those versions.

### How can I report a bug?

1. Check if the issue is already known by searching the relevant GitHub repository:
   - Shizuku: [github.com/RikkaApps/Shizuku/issues](https://github.com/RikkaApps/Shizuku/issues)
   - App Ops: [github.com/RikkaApps/AppOps/issues](https://github.com/RikkaApps/AppOps/issues)
   - Storage Isolation: [github.com/RikkaApps/StorageRedirect/issues](https://github.com/RikkaApps/StorageRedirect/issues)
2. If not already reported, create a new issue with:
   - Your device model and Android version.
   - Whether your device is rooted.
   - Steps to reproduce the problem.
   - Any error messages or logs.
3. For purchase-related issues, see [Purchase (restore) issues on Google Play](./google_play_purchase.md) first — most purchase problems are on Google's side.

## Shizuku

### Why does Shizuku stop after reboot?

Shizuku runs as a process started via adb. The adb process is killed on reboot because it runs in user space. If your device is rooted, Shizuku can start automatically. Without root, you need to re-run the adb command after each reboot.

### Can Shizuku be started automatically without root?

Not natively. Some users use automation apps (like Tasker) to run the start command on boot, but this still requires adb to be active. Wireless adb can help if your device supports it.

### Which apps support Shizuku?

Apps that integrate with Shizuku will list it as a working mode. Some popular apps include App Ops, and any app using the Shizuku API. Check each app's documentation for Shizuku support.

## App Ops

### What's the difference between App Ops and Android's built-in permission manager?

Android's built-in permission manager only exposes a subset of permissions. App Ops gives access to the full set of "appops" — the internal permission system Android uses — including permissions like precise location, background location, and more that aren't available in the standard settings.

### Will denying permissions with App Ops break apps?

It can. Some apps expect certain permissions to always be granted and may crash or malfunction if they are denied. If an app starts behaving unexpectedly after you changed a permission, re-enable it in App Ops.

### Does App Ops work without Shizuku or root?

Yes, App Ops can work in ADB mode. You connect your device to a computer via adb and follow the in-app instructions. However, this mode is less convenient than Shizuku or root mode.

## Storage Isolation

### Why does Storage Isolation require root?

Storage Isolation works by redirecting an app's storage access at the system level. This requires modifying how the app interacts with the file system, which can only be done with root privileges.

### Will isolated apps still be able to save files?

Yes. Isolated apps can still read and write files — they just do so within their own isolated storage directory instead of polluting the shared storage with random folders. The app behaves normally; only the storage location changes.

### Can I isolate system apps?

It is not recommended to isolate system apps, as they may rely on shared storage for critical functionality. Only isolate user-installed apps that create unwanted folders in your shared storage.
