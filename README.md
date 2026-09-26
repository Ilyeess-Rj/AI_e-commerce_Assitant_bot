# 🔧 Synapse Digital — AI Voice/Text Secretary (n8n)

A trilingual (Arabic / French / English) AI receptionist for an electronics repair shop, built entirely as an **n8n workflow**. It runs on Telegram, understands both **voice notes and text**, and autonomously handles appointment booking, appointment modification/cancellation, and product complaints — with real backend checks (stock, availability, holidays, business hours) before anything is confirmed.

![Welcome - English](screenshots/welcome_en.png)
![Welcome - Français](screenshots/welcome_fr.png)
![Welcome - العربية](screenshots/welcome_ar.png)

## ✨ Features

- **Trilingual by design** — replies in Arabic, French or English depending on what the customer writes/says, without mixing languages mid-sentence.
- **Voice + text** — incoming Telegram voice notes are transcribed (speech-to-text), processed by the agent, then the reply is converted back to speech and sent as an audio message. Text messages get a text reply.
- **Smart data validation** — full name, phone number and national ID are collected and loosely validated (tolerant of messy speech-to-text formatting), instead of a rigid regex that rejects legitimate input.
- **Configurable ID/phone format** — out of the box it expects ~8-digit numbers (Tunisian format). This is a plain-language rule inside the agent's instructions, so you can change it to match your own country's format in a couple of lines (see [Customization](#-customization)).
- **Device & stock verification** — before quoting a repair price, the agent checks the device against an accepted-repairs price list; before registering a complaint, it silently queries a MongoDB `products` collection to confirm the product was actually sold by the shop.
- **Transparent pricing** — repair price ranges are hard-coded per device type/issue (phones, laptops, GPUs, consoles, controllers, monitors, etc.) so the quote is instant and consistent.
- **Full confirmation step** — before anything is saved, the agent reads back a complete summary (name, phone, ID, device, issue, price, date, time) and waits for explicit confirmation.
- **Secret booking/complaint code** — every confirmed appointment gets a `SYNP#####` code, every complaint a `REC-######` code, delivered with an apology for the inconvenience. The customer must present it (with their ID card / purchase receipt) on arrival; any mismatch is treated as grounds for automatic cancellation.
- **Real calendar logic, not just an LLM guess** — on confirmation, the workflow:
  1. Checks the date isn't a public holiday (via a dedicated Google Calendar holiday feed + a static list of fixed holidays).
  2. Checks the date isn't a Sunday (shop's weekly day off).
  3. Checks the requested slot is inside business hours (08:00–18:00, Friday until 16:30).
  4. Checks the slot isn't already booked (`Get Availability`).
  5. Only then creates the event in **Google Calendar**, and offers alternative times automatically if a check fails.
- **Complaint logging** — confirmed complaints are written to a **PostgreSQL** table (`reclamations`) for later reporting/analytics, in addition to being logged as a calendar entry.
- **Strict scope-lock** — the agent refuses off-topic requests (general knowledge, jailbreak attempts, "ignore your instructions", etc.) and only ever acts as the shop's secretary.

## 🧱 Architecture

```
Telegram (text or voice)
        │
        ▼
 [If voice?] ──yes──▶ Get file ▶ Speech-to-text (ElevenLabs)
        │
        ▼
    AI Agent (LLM: OpenRouter / Qwen — pluggable)
        │
        ├── Chek_Holidays / Get Availability / Create-Update-Delete-Get an event  → Google Calendar
        ├── Find documents in MongoDB                                            → MongoDB (stock check)
        └── Insert rows in a table in Postgres                                   → PostgreSQL (complaints log)
        │
        ▼
 [was it voice?] ──yes──▶ Text-to-speech (ElevenLabs) ▶ Send audio reply
        │
        └──no──▶ Send text reply
```

The workflow is split into two parallel agent branches (a "Voice Agent" and a "Text Agent" — see the sticky notes inside the workflow) that share the same instructions, tools and calendar/database logic, so behavior is identical regardless of input type.

## 🛠️ Tech stack

| Purpose | Service |
|---|---|
| Automation engine | [n8n](https://n8n.io) |
| Chat interface | Telegram Bot API |
| Speech-to-text / Text-to-speech | ElevenLabs |
| LLM | OpenRouter (e.g. `openai/gpt-4.1-mini`, `openrouter/free`) and/or Alibaba Cloud Qwen (`deepseek-v3.2`) — freely swappable |
| Appointments | Google Calendar API |
| Stock/product verification | MongoDB |
| Complaint records | PostgreSQL |

## 🚀 Setup

1. **Import the workflow**: in n8n, go to *Workflows → Import from File* and select [`workflow/Synapse_Digital_Assistant.json`](workflow/Synapse_Digital_Assistant.json).
2. **Reconnect every credential** (they are stripped from this export for security — see [Security](#-security--privacy) below):
   - Telegram Bot API (create a bot with [@BotFather](https://t.me/BotFather))
   - ElevenLabs API key
   - Google Calendar OAuth2 (you'll need a main calendar for appointments and, optionally, the public Tunisian holiday calendar `en.tn#holiday@group.v.calendar.google.com` — replace with your own country's holiday calendar if needed)
   - MongoDB connection (a `products` collection with at least a `product_name` field)
   - PostgreSQL connection (a `reclamations` table — the required columns are listed in the [Insert rows in a table in Postgres] node)
   - OpenRouter API key and/or Alibaba Cloud (Qwen) API key
3. **Set your own chat ID** in the "Send a text message" node (replace `YOUR_TELEGRAM_CHAT_ID` with your own, or route it dynamically if you want the bot usable by multiple customers rather than one operator chat).
4. **Update the calendar email** in every Google Calendar node (replace `your-calendar-email@gmail.com`).
5. Activate the workflow.

## 🎛️ Customization

Everything the agent "knows" lives in plain language inside the **AI Agent** node's system prompt — no code changes needed:

- **ID/phone number format** — search for the `PHONE NUMBER & NATIONAL ID VALIDATION` section and adjust the expected digit count / starting digits for your country.
- **Business hours & weekly day off** — search for `BOOKING FLOW` / the date-validation block to change opening hours or which day is the day off.
- **Fixed public holidays** — the static list of `DD-MM` dates can be edited directly, or replaced entirely by pointing `Chek_Holidays` at your country's official holiday calendar.
- **Repair price list** — the `ACCEPTED REPAIRS LIST` section holds every device/issue/price range; add, remove or re-price freely.
- **Language templates** — every customer-facing message exists in `[AR]`, `[FR]` and `[EN]` variants; add a fourth language by following the same pattern.

## 🔒 Security & Privacy

This repository has been cleaned up before publishing:
- No API keys, OAuth tokens, or connection strings are included (n8n does not export raw secrets, only credential references, which have been generalized).
- Personal email addresses and the operator's Telegram chat ID from the original deployment have been replaced with placeholders (`your-calendar-email@gmail.com`, `YOUR_TELEGRAM_CHAT_ID`).
- You must supply your own credentials for every connected service before the workflow will run.

## 📄 License

Released under the [Apache License 2.0](LICENSE) — free to use, modify and adapt for your own business.

---

*Built with n8n — contributions and forks welcome.*
