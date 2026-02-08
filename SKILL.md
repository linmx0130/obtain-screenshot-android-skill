---
name: obtain-screenshot-android
description: Capture screenshots from Android devices using ADB (Android Debug Bridge). Use when Kimi needs to obtain screenshots from connected Android devices, emulators, or physical devices for debugging, testing, documentation, or visual verification purposes.
---

# Obtain Screenshot from Android Devices

Capture screenshots from Android devices using ADB commands.

## Prerequisites

- ADB (Android Debug Bridge) must be installed and available in PATH. On Mac, you may try to find ADB tool in ` ~/Library/Android/sdk/platform-tools/`.
- Device must be connected via USB with USB debugging enabled, or connected via Wi-Fi debugging
- Device must be authorized (adb authorization dialog accepted on device)
- Emulator could be used as a device.

## Quick Start
First of all, querying whether there are connected devices.
```bash
# List connected devices
adb devices -l
```
- If there is no device connected, stop and asks the user to connect device or launch the emulator.
- If there is only one connected device, you don't need to add `<device_serial>` in the following commands. Otherwise, you will have to add the device serial to choose which device to use.

Capture a screenshot and pull it to local machine:

```bash
adb exec-out screencap -p > ./screenshot.png
```

The screenshot will be saved in PNG format as `./screenshot.png`. 

## Common Workflows

### Capture Screenshot from Specific Device

When multiple devices are connected, specify the device serial:

```bash
# Capture from specific device
adb -s <device_serial> exec-out screencap -p > ./screenshot.png
```

### Capture Screenshot with Timestamp

```bash
# Generate filename with timestamp
filename="screenshot_$(date +%Y%m%d_%H%M%S).png"
adb -s <device_serial> exec-out screencap -p > ./$filename
```

## Troubleshooting

### Device not found

- Ensure USB debugging is enabled on the device (Settings > Developer options > USB debugging)
- Check cable connection
- Run `adb devices` to verify device is detected
- Accept the RSA fingerprint dialog on the device if shown

### Permission denied

- Ensure the device has authorized the computer (check for authorization dialog on device)
- Try restarting ADB server: `adb kill-server && adb start-server`

### Screenshot is black or empty

- Ensure the device screen is on and unlocked
- Some secure screens (banking apps, etc.) cannot be captured

## Use Case Example: Preview Jetpack Compose Component
If you have an Jetpack Compose preview component, you can use `PreviewActivity` to launch the preview.
```bash
$ ./gradlew installDebug
$ adb shell am start -n <package>/androidx.compose.ui.tooling.PreviewActivity -a android.intent.action.MAIN -c android.intent.category.LAUNCHER --es composable <preview-component-package-name> --splashscreen-show-icon
$ adb exec-out screencap -p > ./compose_preview.png
```

If the package name is `com.example.app` and the preview component name is `com.example.app.MainActivityKt.MainScreenPreview`, the command to use will be
```bash
$ ./gradlew installDebug
$ adb shell am start -n com.example.app/androidx.compose.ui.tooling.PreviewActivity -a android.intent.action.MAIN -c android.intent.category.LAUNCHER --es composable com.example.app.MainActivityKt.MainScreenPreview --splashscreen-show-icon
$ adb exec-out screencap -p > ./compose_preview.png
```

The UI output of the preview will be stored in `./compose_preview`.

## Clean up
Finally, after the execution, remember to delete all screenshots unless the users explictly ask to keep them.
