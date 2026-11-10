---
name: test-android-app
description: Test an Android app on a real cloud Android device (ApkRunner) - build the APK, install it, walk through the flows the user names like a manual tester, collect crashes and logcat, and report what works and what breaks. Use when asked to run, try, smoke-test or QA an Android app or APK, check that a change works on Android, or reproduce an Android bug.
argument-hint: "[what to test, e.g. 'login and checkout on Android 12 and 15']"
---

# Test an Android app on a cloud device

The `apkrunner` MCP tools drive a real Android device in the cloud (Android 12-15, ARM, Google Play
services replaced by microG). The user pays for device time by the minute, so work steadily and stop
the device at the end.

What to test: $ARGUMENTS (if empty, test the app's main flow: launch, the first screens, sign-in or
onboarding if there is one, and the primary action).

If the apkrunner tools are missing or say that sign-in is needed, ask the user to run `/mcp`, pick
the apkrunner server and choose Authenticate: it opens apkrunner.com to sign in, once.

## 1. Get the APK

- In an Android project, build a debug APK: `./gradlew assembleDebug` (or the module's
  `:app:assembleDebug`). It is at `app/build/outputs/apk/debug/app-debug.apk` (or a flavor's
  directory next to it). Prefer an APK with an `arm64-v8a` or universal build of native libraries.
- If the user gave an APK file or a link, use that.

## 2. Start the device and install

1. `start_device`, with `android` if the user named a version (default 14). If it says the device
   is still starting or queued, call `device_status` until it is ready.
2. `install_apk` with `path` for a local file: it returns a `curl` command. Run it with Bash; it
   prints `{"package": "…"}` when the app is installed and open. For a file on the web use
   `install_apk` with `url`, or pass `apk_url` to `start_device`.
3. If the install is refused, report the reason (wrong ABI, minSdk too high, a broken file).

## 3. Walk through the flows

- Read the screen with `list_elements` before acting: it gives each view's text, id and the
  coordinates to tap. Take a `screenshot` when layout or visuals matter, when list_elements shows
  nothing useful (games, video, custom drawing), and as evidence of each bug.
- Act with `tap` (by `text` when the view has one, else by coordinates), `type_text` (tap the field
  first; `submit: true` presses Enter), `swipe` (`direction: "up"` scrolls down a list),
  `press_key` (`back`, `enter`…), `wait_for_text` for screens that load.
- After each step, check that the screen is the one expected. When something looks wrong, read
  `crashes` and `logcat` (with the app's `package` and `level: "E"` first) before guessing.
- Test data: invent plausible values; never type the user's real credentials unless they gave
  them for this purpose.
- For several Android versions, finish one, `stop_device`, then `start_device` with the next.

## 4. Report

End with `stop_device`, then a short report:

- What was tested, on which Android version and screen.
- Each flow: passed or failed, with the step where it failed.
- Each bug: steps to reproduce, expected vs actual, the exception line and the top stack frames
  from `crashes`/`logcat`, and the screenshot.
- If you have the source, point to the likely cause in the code.
