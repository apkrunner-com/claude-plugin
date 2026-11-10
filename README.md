# ApkRunner plugin for Claude Code

Give Claude a real Android device in the cloud. Claude builds your app, installs the APK, taps
through it like a tester, reads the screen as a list of views, and reads crashes and logcat when
something breaks. Nothing runs on your machine: no emulator, no Android SDK image, no KVM. That is
why it also works in Claude Code on the web and other cloud sandboxes where an emulator can't start.

The device is the same as in ApkRunner's web player (apkrunner.com): Android 12 to 15 on ARM
servers, phone, foldable and tablet screens, with microG in place of Google Play services.

## Setup

1. Install the plugin:

   ```
   claude plugin marketplace add apkrunner-com/claude-plugin
   claude plugin install apkrunner@apkrunner
   ```

   or, inside a session, `/plugin marketplace add apkrunner-com/claude-plugin` and then
   `/plugin install apkrunner@apkrunner`. Without git, add the same marketplace from
   `https://apkrunner.com/claude/marketplace.json` instead.
2. Sign in once: run `/mcp`, pick `plugin:apkrunner:apkrunner` and choose **Authenticate**. Your
   browser opens apkrunner.com, where you sign in with Google or by email (new accounts start with
   free minutes) and allow Claude Code. You can disconnect it at any time on
   [apkrunner.com/account](https://apkrunner.com/account).

Where the agent can't open a browser (CI, a cloud sandbox), add the server with an API key from
[apkrunner.com/account](https://apkrunner.com/account) instead of the plugin's sign-in:

```
claude mcp add --transport http apkrunner https://api.apkrunner.com/mcp \
  --header "Authorization: Bearer ark_…"
```

## Use

Ask in plain words, for example:

- "Build the debug APK, install it on Android 15 and check that sign-up works."
- "Reproduce the crash from issue #42 on Android 12 and find its cause."
- `/apkrunner:test-android-app login and checkout on Android 12 and 15`

## Tools

| Tool | What it does |
| - | - |
| `start_device`, `device_status`, `stop_device` | Start a device (Android 12-15, a screen preset, optionally an APK by URL or a web page), check on it, stop it |
| `install_apk` | Install an APK, XAPK or APKS by URL, or from your machine through a `curl` command it gives |
| `list_apps`, `launch_app`, `stop_app`, `clear_app_data`, `uninstall_app` | Manage apps by package name |
| `open_url` | A web page in the device's browser, or a deep link in your app |
| `list_elements` | The views on screen with text, id, state and tap coordinates |
| `screenshot` | A PNG of the screen |
| `tap`, `double_tap`, `long_press` | By coordinates or by a view's text |
| `swipe` | By direction or between two points |
| `type_text`, `press_key` | Text into the focused field; back, home, enter and other keys |
| `wait_for_text` | Wait for a view to appear |
| `logcat`, `crashes` | The device log, filtered by app and level; crashes and ANRs with their exception |

## What it costs

Device time comes from your ApkRunner account, as in the web player: free minutes to try it,
then a paid plan. A device stops after 3 minutes without a tool call, when your minutes run out,
or when Claude calls `stop_device`. One device per account at a time.

## Data

- **Where it connects.** The plugin adds one remote MCP server, `https://api.apkrunner.com/mcp`,
  which you sign in to through apkrunner.com (OAuth); Claude Code keeps the token. It runs no code
  on your machine.
- **Your APK.** `install_apk` with a path gives Claude a `curl` command that uploads the file
  straight to the server running your device (a host under `w.apkrunner.com`, in Europe or the US),
  which installs it and deletes the file. With a URL, ApkRunner's server downloads the file and
  keeps it for up to an hour to install it.
- **What goes to the device.** Taps, swipes, keys, the text Claude types and the links it opens go
  to your cloud device: a fresh Android, wiped with everything on it when the session ends.
- **What comes back.** Screenshots, the text and positions of the views on screen, the app list,
  logcat lines and crash reports come back into your Claude session, and so to the model provider
  your Claude Code uses.
- **What ApkRunner keeps.** Your account, hashes of the sign-in tokens and API keys (never the
  tokens or keys), each session's
  record (times, Android version, apps used, crashes), and which tools were called, whether they
  worked and how long they took. Not the screenshots, the views, the logs or the text Claude types.

See the [privacy policy](https://apkrunner.com/privacy) ("AI Agents: API Keys and the MCP Server").

## License

MIT
