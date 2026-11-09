# ApkRunner plugin for Claude Code

Give Claude a real Android device in the cloud. Claude builds your app, installs the APK, taps
through it like a tester, reads the screen as a list of views, and reads crashes and logcat when
something breaks. Nothing runs on your machine: no emulator, no Android SDK image, no KVM. That is
why it also works in Claude Code on the web and other cloud sandboxes where an emulator can't start.

The device is the same as in ApkRunner's web player (apkrunner.com): Android 12 to 15 on ARM
servers, phone, foldable and tablet screens, with microG in place of Google Play services.

## Setup

1. Sign in at [apkrunner.com/account](https://apkrunner.com/account) and select **Create a key**
   under API keys. The key starts with `ark_`.
2. Install the plugin:

   ```
   claude plugin marketplace add apkrunner-com/claude-plugin
   claude plugin install apkrunner@apkrunner
   ```

   or, inside a session, `/plugin marketplace add apkrunner-com/claude-plugin` and then
   `/plugin install apkrunner@apkrunner`. Without git, add the same marketplace from
   `https://apkrunner.com/claude/marketplace.json` instead.
3. Claude Code asks for the API key when the plugin is enabled. To change it later, run
   `/plugin configure apkrunner`.

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

The plugin connects Claude Code to one remote MCP server, `https://api.apkrunner.com/mcp`, with
your API key. What Claude sends there (taps, text it types, URLs, APKs you install) goes to a
fresh device that is wiped when the session ends; screenshots, the view list and logs come back
into your Claude session. The plugin runs no code on your machine; the only command it suggests
is the `curl` upload of your APK. See the [privacy policy](https://apkrunner.com/privacy).

## License

MIT
