# Tivlen Automation — Credentials & Variables Reference

## n8n Variables to Set
Go to n8n → Settings → Variables and create each one:

| Variable | Where to get it | Example |
|---|---|---|
| `GOOGLE_DRIVE_FOLDER_ID` | Drive URL: `/folders/THIS_PART` | `1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs` |
| `GOOGLE_SHEETS_ID` | Sheets URL: `/spreadsheets/d/THIS_PART/` | `1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs` |
| `ANTHROPIC_API_KEY` | console.anthropic.com → API Keys | `sk-ant-api03-...` |
| `TWILIO_ACCOUNT_SID` | console.twilio.com → Account Info | `ACxxxxxxxxxxxxxxxx` |
| `TWILIO_AUTH_TOKEN` | console.twilio.com → Account Info | `your_auth_token` |
| `TWILIO_WHATSAPP_FROM` | Twilio WhatsApp Sandbox number | `whatsapp:+14155238886` |
| `ANISA_WHATSAPP_NUMBER` | Your personal WhatsApp number | `whatsapp:+34612345678` |
| `CLOUDCONVERT_API_KEY` | cloudconvert.com → API Keys | `eyJ0eXAiOiJKV1Q...` |
| `REPORT_EMAIL` | Your email for monthly reports | `anisa@tivlenshop.com` |
| `N8N_WEBHOOK_BASE_URL` | Your n8n public URL | `https://your-n8n.app.n8n.cloud/webhook` |
| `IG_USER_ID` | Instagram Graph API → your account ID | `17841400008460056` |
| `IG_ACCESS_TOKEN` | Instagram Graph API → long-lived token | `EAAxxxxxx...` |
| `LI_ACCESS_TOKEN` | LinkedIn OAuth2 → access token | `AQV...` |

---

## n8n Credentials to Create

### 1. Google Drive (OAuth2)
- Type: Google Drive OAuth2
- Scopes: `https://www.googleapis.com/auth/drive`

### 2. Google Sheets (OAuth2)
- Type: Google Sheets OAuth2
- Scopes: `https://www.googleapis.com/auth/spreadsheets`

### 3. Gmail (OAuth2)
- Type: Gmail OAuth2
- Used for: monthly report email

### 4. Twilio
- Type: Twilio
- Account SID: from Twilio console
- Auth Token: from Twilio console

### 5. LinkedIn (OAuth2)
- Type: LinkedIn OAuth2
- Scopes: `r_liteprofile`, `r_emailaddress`, `w_member_social`

---

## Twilio WhatsApp Sandbox Setup

1. Go to console.twilio.com → Messaging → Try it out → Send a WhatsApp message
2. Follow the sandbox setup — send the join code from YOUR WhatsApp to the sandbox number
3. Under Sandbox Settings, set:
   - **"When a message comes in"** → `https://YOUR-N8N-URL/webhook/tivlen-whatsapp-reply`
   - Method: HTTP POST
4. That's it. Every WhatsApp message you send to the sandbox number triggers Workflow 3.

---

## Instagram Graph API Setup

1. Go to developers.facebook.com → Create App → Business
2. Add Instagram Graph API product
3. Connect your Instagram Business account
4. Generate a long-lived User Access Token (valid 60 days — set a calendar reminder to refresh)
5. Get your Instagram User ID:
   ```
   GET https://graph.facebook.com/v18.0/me?fields=id,name&access_token=YOUR_TOKEN
   ```

---

## CloudConvert Setup

1. Sign up at cloudconvert.com
2. Go to API → API Keys → Create API Key
3. Enable the `tasks.write` and `tasks.read` scopes
4. Free plan: 25 conversions/day (more than enough for 1 post/day)
5. Paid: $8/month for 500 conversions

---

## Activation Order (important!)

Activate workflows in this order to avoid issues:

1. ✅ Workflow 3: WhatsApp Webhook (must be active to receive replies)
2. ✅ Workflow 4: Story Poster
3. ✅ Workflow 5: Analytics Collector
4. ✅ Workflow 6: Monthly Report
5. ✅ Workflow 1: Sunday Batch (activates the whole thing)
6. ✅ Workflow 2: Daily Approval

Test by running Workflow 1 manually first → check Sheets has rows → then trigger Workflow 2 manually → reply to WhatsApp → watch it post.
