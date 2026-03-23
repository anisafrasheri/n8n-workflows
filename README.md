The full pipeline as 6 separate n8n workflows in one file, all connected through Google Sheets as the shared database:

Workflow 1 — Sunday Batch: reads your Drive folder, filters unused images, pulls your top posts for voice memory, generates 3 variants × 7 days in one Claude call per image, writes everything to Sheets, sends you a WhatsApp confirmation
Workflow 2 — Daily 9am Approval: reads today's row from Sheets, sends you the 3 variant choices + all 3 caption previews as separate WhatsApp messages, auto-posts Variant A at 10am if you don't reply
Workflow 3 — WhatsApp Webhook: always-on, catches your reply (1/2/3/0), renders both feed and story PNGs via CloudConvert, posts to Instagram feed + LinkedIn, schedules the Story, updates Sheets
Workflow 4 — Story at 11am: picks up the pending story from Sheets and posts it
Workflow 5 — Analytics at 48h: fetches real engagement numbers from Instagram + LinkedIn APIs and writes them back to the same Sheets row
Workflow 6 — Monthly Report: Claude reads the full month of data, writes a branded HTML email with stats, top posts, pillar breakdown, and personalised recommendations for Anisa — sent via Gmail on the 1st
