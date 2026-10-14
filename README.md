# SMS to Telegram Forwarder

Forwards incoming SMS from an Android phone straight to a Telegram chat,
so you can read your texts from any device you're signed into — no SMS
charges, since forwarding happens over the internet via a Telegram bot.

Supports managing multiple phones from a single Telegram chat, with
per-device on/off control sent right from Telegram itself.

## Features

- 📩 Forwards every incoming SMS (sender + full message) to a Telegram chat
- 🔗 Works across devices — read forwarded texts on your phone, desktop, or the Telegram web app
- 🔄 Rejoins multi-part long SMS into a single message
- 🎛 Toggle forwarding on/off from inside the app, **or** remotely from Telegram:
  - `/start` / `/stop` — on/off for a single-device setup
  - `/start <device name>` / `/stop <device name>` — control one specific phone
  - `/start all` / `/stop all` — control every phone on the bot at once
- 📱 Multi-device support — run it on several phones, each with its own
  device name, all reporting to the same bot
- ⚡ Long-press the Save button (3s) to quick-fill saved default credentials
- 🧱 Auto-builds a debug APK via GitHub Actions — no local Android Studio
  setup required; builds are published automatically under **Releases**
- 🔒 Your bot token and chat ID are stored only on-device (`SharedPreferences`)
  — never hardcoded or committed, unless you've explicitly set up the
  long-press shortcut yourself

## How it works

1. Android broadcasts an `SMS_RECEIVED` signal the instant a text arrives
2. A manifest-registered receiver in the app catches that signal
3. Before forwarding, it checks Telegram for any new `/start` or `/stop`
   command (optionally targeted at this device's name) and updates its
   on/off state accordingly
4. If enabled, it sends the message via Telegram's Bot API
   (`api.telegram.org/bot<token>/sendMessage`) — no SMS used

Commands are only checked right before a forward attempt, since nothing
runs continuously in the background. This keeps the app lightweight, with
no persistent notification and minimal battery impact — but it also means
a `/stop` takes effect starting with the next incoming text, not instantly.

## Setup

### 1. Create a Telegram bot
- Message [@BotFather](https://t.me/BotFather) → `/newbot` → follow the
  prompts → copy the bot token it gives you
- Send your new bot any message (required before it can message you back)
- Message [@userinfobot](https://t.me/userinfobot) to get your numeric chat ID

### 2. Build the APK
This repo builds itself via GitHub Actions — push to `main` and check the
**Actions** tab. Once the build succeeds, download `app-debug.apk` from
**Releases**.

### 3. Install & configure
- Sideload the APK (Google Play restricts SMS-reading apps, so this isn't
  Play Store–distributed)
- Open the app, enter your bot token, chat ID, and a device name
- Tap **Save**, grant SMS permission
- Optionally send `/start` to your bot in Telegram to confirm it's listening

### 4. For multiple phones
Install the same APK on each phone, use the same bot token and chat ID,
but give each phone a **different device name**. Control them individually
with `/start <name>` / `/stop <name>`, or all at once with `/start all` /
`/stop all`.

## Notes

- Some phones (especially Xiaomi/MIUI, Samsung) kill background apps
  aggressively — exempt this app from battery optimization in Settings if
  forwarding stops after a while
- Only use this on your own devices — it forwards every incoming SMS,
  including 2FA/verification codes
- If this repo is public and ever had a real bot token committed to it
  (e.g. via a hardcoded shortcut), treat that token as compromised and
  generate a new one via @BotFather

---

Created by [The AryanOnion](https://github.com/AryanKumar1234)
