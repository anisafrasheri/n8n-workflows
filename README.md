# Tivlen Social Media Automation

Automated daily content pipeline for [Tivlen Shop](https://tivlenshop.com) — a purpose-driven brand by Anisa Frasheri that empowers young people to build self-confidence.

Every day, this system picks a quote image from Google Drive, sends it to Claude AI, generates on-brand visuals + captions, asks for approval via WhatsApp, and posts to Instagram and LinkedIn automatically.

---

## What It Does

```
Every Sunday 8am
  └── Picks 7 quote images from Google Drive
  └── Calls Claude API → generates 3 visual variants + captions per image
  └── Stores everything in Google Sheets (Mon–Sun schedule)
  └── Sends WhatsApp confirmation: "Your week is ready 💛"

Every day 9am
  └── Sends WhatsApp with 3 variant previews + captions
  └── You reply: 1 / 2 / 3 / 0 (skip)
  └── No reply by 10am → auto-posts Variant A

On your reply
  └── Renders approved SVG → PNG (feed 1080×1080 + story 1080×1920)
  └── Posts to Instagram feed at 9am
  └── Posts to LinkedIn at 9am
  └── Posts Instagram Story at 11am

48 hours after posting
  └── Fetches real engagement data (likes, saves, reach, comments)
  └── Writes metrics back to Google Sheets

1st of every month
  └── Claude reads all month's data
  └── Generates branded HTML performance report
  └── Sends to your email
```

---

## Repo Structure

```
tivlen-automation/
│
├── README.md                          ← you are here
│
├── workflows/
│   └── tivlen_complete_workflow.json  ← all 6 n8n workflows in one file
│
├── docs/
│   ├── tivlen_credentials_guide.md    ← all API keys, where to get them, activation order
│   ├── tivlen_sheets_setup.md         ← Google Sheets column schema
│   ├── tivlen_setup_guide.md          ← step-by-step first-time setup
│   └── tivlen_claude_system_prompt.md ← the Claude system prompt used in the API call
│
└── archive/
    └── tivlen_n8n_workflow.json       ← v1 workflow (basic, no approval or analytics)
```

---

## Workflows Overview

| # | Name | Schedule | Purpose |
|---|---|---|---|
| 1 | Sunday Batch Generator | Every Sunday 8am | Generates a full week of content |
| 2 | Daily Morning Approval | Every day 9am | Sends WhatsApp with variants to approve |
| 3 | WhatsApp Reply Webhook | Always on | Receives reply, renders PNG, posts |
| 4 | Instagram Story Poster | Every day 11am | Posts the Story version |
| 5 | Analytics Collector | Every day 11am | Fetches engagement 48h after posting |
| 6 | Monthly Report Email | 1st of month 9am | Sends performance report via Gmail |

---

## Tech Stack

| Service | Role |
|---|---|
| **n8n** | Workflow automation engine |
| **Claude API** (`claude-sonnet-4-20250514`) | Content intelligence — reads quote images, generates SVG visuals + captions |
| **Google Drive** | Source folder for quote images |
| **Google Sheets** | Central database — queue, post tracking, analytics |
| **Twilio** | WhatsApp messages (approval flow) |
| **CloudConvert** | SVG → PNG rendering (1080×1080 feed, 1080×1920 story) |
| **Instagram Graph API** | Feed + Story publishing, engagement data |
| **LinkedIn API** | Post publishing |
| **Gmail** | Monthly report delivery |

---

## Prerequisites

- n8n instance (cloud or self-hosted)
- Anthropic API account → [console.anthropic.com](https://console.anthropic.com)
- Twilio account with WhatsApp Sandbox activated → [console.twilio.com](https://console.twilio.com)
- CloudConvert account → [cloudconvert.com](https://cloudconvert.com)
- Google Cloud project with Drive + Sheets + Gmail APIs enabled
- Instagram Business account connected to Facebook App
- LinkedIn developer app with `w_member_social` scope

---

## Setup in 6 Steps

### 1. Google Drive
Create a folder called `tivlen-quotes/` and drop your quote images in it (PNG or JPG). Copy the folder ID from the URL.

### 2. Google Sheets
Create a new spreadsheet. Add a sheet called `posts` and set up the columns exactly as described in [`docs/tivlen_sheets_setup.md`](docs/tivlen_sheets_setup.md). Copy the spreadsheet ID from the URL.

### 3. Twilio WhatsApp Sandbox
1. Go to Twilio Console → Messaging → Try it out → Send a WhatsApp message
2. Follow the sandbox activation (you send a join code from your WhatsApp)
3. Under Sandbox Settings → **"When a message comes in"**, set the URL to:
   ```
   https://YOUR-N8N-URL/webhook/tivlen-whatsapp-reply
   ```

### 4. n8n Variables
In n8n → Settings → Variables, create every variable listed in [`docs/tivlen_credentials_guide.md`](docs/tivlen_credentials_guide.md).

### 5. n8n Credentials
Create these credential entries in n8n:
- Google Drive OAuth2
- Google Sheets OAuth2
- Gmail OAuth2
- Twilio
- LinkedIn OAuth2

### 6. Import & Activate Workflows
1. In n8n → Add workflow → Import from file → upload `workflows/tivlen_complete_workflow.json`
2. Activate in this exact order:
   ```
   Workflow 3 (Webhook) → Workflow 4 → Workflow 5 → Workflow 6 → Workflow 1 → Workflow 2
   ```
3. Test: run Workflow 1 manually → check Sheets has rows → trigger Workflow 2 manually → reply `1` on WhatsApp → watch it post.

---

## How the Approval Flow Works

Every morning at 9am you receive a WhatsApp from the bot:

```
🌟 Tivlen — Monday 24 March

📌 Quote: "Don't strive to be well-known. Strive to be worth knowing."
🏷 Pillar: Empowerment

3 variants are ready. Reply with:
1 → Post Variant A: Quote Graphic
2 → Post Variant B: Motivational Typography
3 → Post Variant C: Affirmation Card
0 → Skip today

⏰ No reply by 10am = auto-posts Variant A
```

You also receive three follow-up messages with the full caption previews for each variant. Reply with a single number. That's it.

---

## How Claude Generates Content

Claude receives the quote image and:

1. Reads the quote text from the image
2. Detects the content pillar (Empowerment / Growth / Community / Awareness / Lifestyle / Thought Leadership)
3. Reads your top-performing past captions from Sheets to match your voice
4. Checks today's date for seasonal/cultural relevance
5. Generates 3 visually distinct SVG designs using Tivlen's brand system
6. Writes an Instagram caption and a LinkedIn caption for each variant

All three variants use the Tivlen brand: warm cream background `#FFF8E1`, golden yellow `#F5C518`, bold sans-serif + cursive font mix, and a cursive "tivlen shop" watermark at the bottom of every post.

The system prompt used for generation is in [`docs/tivlen_claude_system_prompt.md`](docs/tivlen_claude_system_prompt.md).

---

## Google Sheets Schema

The `posts` sheet is the central database for the entire pipeline. Every post has one row that gets updated across all 6 workflows — from generation on Sunday through to engagement metrics 48 hours after posting.

Full column reference: [`docs/tivlen_sheets_setup.md`](docs/tivlen_sheets_setup.md)

Key columns:
- `status`: `pending_approval` → `posted` / `skipped`
- `approved_variant`: `a` / `b` / `c`
- `ig_eng_rate`: calculated engagement rate, used by Claude for voice memory

---

## Analytics & Voice Memory

48 hours after every post, the pipeline fetches:
- Instagram: likes, comments, saves, reach, impressions, engagement rate
- LinkedIn: reactions, comments, impressions

Every Sunday before generating new content, Claude reads your top 5 highest-engagement captions and your 10 most recent posts. It uses this to build a voice profile that shapes the tone, phrasing, and caption length for the new week's content. The longer you run the system, the more it sounds like Anisa.

---

## Monthly Report

On the 1st of every month, Claude reads the full previous month of data and generates a branded HTML email with:

- Summary stats (total posts, avg engagement rate, total reach, total saves)
- Top 3 posts of the month
- Best-performing content pillar
- Best-performing post format
- Personalised recommendations for next month

---

## Brand Guidelines

Tivlen's visual identity is fully encoded in the Claude system prompt. Key rules:

- **Background**: always warm cream `#FFF8E1` — never cold white
- **Primary accent**: golden yellow `#F5C518` — must be visually dominant
- **Fonts**: bold sans-serif (Arial Black) + cursive (Dancing Script) — always mixed, never one alone
- **Watermark**: cursive "tivlen shop" at the bottom center of every post
- **Feel**: warm, hand-crafted, human — never corporate

---

## Cost Estimate

Running this daily for one month:

| Service | Cost |
|---|---|
| Claude API (~30 calls/month × ~5k tokens) | ~$2–4 |
| CloudConvert (~60 conversions/month) | Free tier or ~$1 |
| Twilio WhatsApp (~60 messages/month) | ~$3 |
| n8n Cloud | $20/month (Starter) or free if self-hosted |
| **Total** | **~$25–30/month** |

---

## Contributing

This is a private automation for Tivlen Shop. If you're adapting this for your own brand, update the system prompt in `docs/tivlen_claude_system_prompt.md` with your own brand colors, fonts, and voice guidelines.

---

## License

Private repository — Tivlen Shop. Not for redistribution.

---

*Built with Claude AI + n8n · Made with 💛 for Tivlen*
