---
title: How to use
description: General guide on how to use Rikka Apps products effectively.
tags:
  - guide
  - beginner
---

# How to use

This article provides a general guide on how to get started with Rikka Apps products: **Shizuku**, **App Ops**, and **Storage Isolation**.

## Getting started

### Shizuku

Shizuku is a service that helps other apps use system APIs conveniently with adb or root privilege. It acts as a bridge between apps and the Android system.

**To start Shizuku:**

1. Download and install Shizuku from [Google Play](https://play.google.com/store/apps/details?id=moe.shizuku.privileged.api) or [GitHub](https://github.com/RikkaApps/Shizuku/releases).
2. If your device is rooted, simply tap **Start** in the Shizuku app and grant root access.
3. If your device is not rooted, you need to run a command via adb:
   ```shell
   adb shell sh /sdcard/Android/data/moe.shizuku.privileged.api/start.sh
   ```
   You will need to re-run this command every time you reboot your device.
4. Once started, other apps that support Shizuku will be able to use it automatically.

::: tip
You only need Shizuku running — apps that depend on it will detect it automatically.
:::

### App Ops

App Ops lets you control the hidden Android "appops" permissions that the system doesn't expose in the settings UI.

**To use App Ops:**

1. Download and install App Ops from [Google Play](https://play.google.com/store/apps/details?id=rikka.appops) or [GitHub](https://github.com/RikkaApps/AppOps/releases).
2. Launch the app and select your working mode:
   - **Root mode** — if your device is rooted.
   - **Shizuku mode** — if Shizuku is running (requires Shizuku to be started first).
   - **ADB mode** — if you don't have root, connect via adb and follow the in-app instructions.
3. Select the app you want to manage.
4. Toggle individual permissions on or off as needed.

::: warning
Changing some permissions may cause apps to malfunction. Use with caution.
:::

### Storage Isolation

Storage Isolation gives apps isolated storage so they can't create messy folders in your shared storage.

**To use Storage Isolation:**

1. Download and install Storage Isolation from [Google Play](https://play.google.com/store/apps/details?id=moe.shizuku.redirectstorage) or [GitHub](https://github.com/RikkaApps/StorageRedirect/releases).
2. Root is required for this app.
3. Launch the app and enable the Storage Isolation service.
4. Select the apps you want to isolate from the list.
5. Isolated apps will only have access to their own private storage directory.

::: warning
Storage Isolation requires root. Do not use on devices without root access.
:::

## Common features

### Permission management

Both App Ops and Shizuku revolve around managing what apps are allowed to do. App Ops gives you granular control over per-app permissions (camera, microphone, location, etc.), while Shizuku enables other apps to perform privileged operations without needing root themselves.

### Working modes

Most Rikka Apps support multiple working modes:
- **Root** — the simplest and most reliable, but requires a rooted device.
- **Shizuku** — no root needed, but Shizuku must be running.
- **ADB** — for users without root who can't or don't want to use Shizuku permanently.

### Cross-app integration

Shizuku is designed to be used by other apps. Apps like App Ops can use Shizuku as their backend instead of requiring root directly. This means you only need to set up Shizuku once, and multiple apps can benefit from it.

## Tips and tricks

### Keep Shizuku running after reboot

If you're not rooted, Shizuku stops after every reboot. To automate restarting it, you can:
- Use a task automation app like Tasker to run the start command on boot.
- Or simply re-run the adb command after each reboot.

### Use App Ops to restrict background location

Android's built-in settings don't always let you deny location access per-app. With App Ops, you can toggle the `OP_FINE_LOCATION` and `OP_COARSE_LOCATION` ops to fully deny location access to specific apps.

### Freeze unused apps' permissions

If you have apps installed that you rarely use but don't want to uninstall, use App Ops to disable their network and location permissions to save battery and protect privacy.

### Backup your App Ops configuration

App Ops allows you to export and import your permission configurations. This is useful when switching devices or reinstalling, so you don't have to reconfigure everything manually.
