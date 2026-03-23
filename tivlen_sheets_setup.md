# Tivlen Google Sheets Setup

## Create one spreadsheet with these two sheets:

---

## Sheet 1: `posts`

Create these columns in row 1 exactly as written:

| Column | Type | Description |
|---|---|---|
| row_id | text | Auto-generated (post_timestamp) |
| quote_text | text | Quote extracted by Claude |
| pillar | text | Empowerment / Growth / Community / Awareness / Lifestyle |
| format | text | Quote Graphic / Affirmation Card / etc |
| scheduled_date | date | YYYY-MM-DD |
| status | text | pending_approval / posted / skipped |
| drive_file_id | text | Google Drive file ID of source image |
| variant_a_svg_feed | text (long) | SVG code for feed post |
| variant_a_svg_story | text (long) | SVG code for story post |
| variant_a_ig_caption | text (long) | Instagram caption |
| variant_a_li_caption | text (long) | LinkedIn caption |
| variant_a_format | text | Format name for variant A |
| variant_b_svg_feed | text (long) | |
| variant_b_svg_story | text (long) | |
| variant_b_ig_caption | text (long) | |
| variant_b_li_caption | text (long) | |
| variant_b_format | text | |
| variant_c_svg_feed | text (long) | |
| variant_c_svg_story | text (long) | |
| variant_c_ig_caption | text (long) | |
| variant_c_li_caption | text (long) | |
| variant_c_format | text | |
| approved_variant | text | a / b / c |
| ig_post_id | text | Returned by Instagram API |
| li_post_id | text | Returned by LinkedIn API |
| ig_likes | number | |
| ig_comments | number | |
| ig_saves | number | |
| ig_reach | number | |
| ig_impressions | number | |
| ig_eng_rate | number | Calculated: (likes+saves+comments)/reach*100 |
| li_reactions | number | |
| li_comments | number | |
| li_impressions | number | |
| story_status | text | pending / posted |
| story_post_at | datetime | When to post the story |
| story_ig_id | text | Story post ID |
| posted_at | datetime | When the feed post went live |

---

## Sheet 2: `voice_memory`

Optional but recommended. Claude writes here after each week.

| Column | Type |
|---|---|
| created_at | datetime |
| voice_profile | text (long) |
| top_format | text |
| top_pillar | text |
| avg_eng_rate | number |
| notes | text |

---

## Important settings:

1. Make sure the spreadsheet is shared with your Google service account (the one connected to n8n)
2. Copy the spreadsheet ID from the URL: `docs.google.com/spreadsheets/d/THIS_IS_THE_ID/edit`
3. Paste it as `GOOGLE_SHEETS_ID` in n8n variables
4. The SVG columns will hold very long strings — Google Sheets handles this fine (50,000 char limit per cell)
