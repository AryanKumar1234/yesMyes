# SMS to Telegram Forwarder

Forwards incoming SMS from an Android phone straight to a Telegram chat,
so you can read your texts from any device you're signed into — no SMS
charges, since forwarding happens over the internet via a Telegram bot.

Supports managing multiple phones from a single Telegram chat, with
per-device on/off control sent right from Telegram itself.

## Features

- 📩 Forwards every incoming SMS (sender + full message) to a Telegram chat,
  with the sending device's name at the top of each message
- 🔗 Works across devices — read forwarded texts on your phone, desktop, or the Telegram web app
- 🔄 Rejoins multi-part long SMS into a single message
- 🎛 Toggle forwarding on/off from inside the app, **or** remotely from Telegram:
  - `/start` / `/stop` — on/off for a single-device setup
  - `/start <device name>` / `/stop <device name>` — control one specific phone
  - `/start all` / `/stop all` — control every phone on the bot at once
- 📱 Multi-device support — run it on several phones, each with its own
  device name (falls back to the phone's model name if left blank), all
  reporting to the same bot
- 👀 Each device announces itself in the chat whenever the app is opened or
  saved (`📱 <device> is online — Forwarding: ON/OFF`), so the chat builds up
  a visible log of which phones are set up
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
- Optional: register the command menu by sending BotFather `/setcommands`,
  then selecting your bot and sending:
