# MoneyPrinterV2 — Modified Fork

> Original project by [@FujiwaraChoki](https://github.com/FujiwaraChoki/MoneyPrinterV2). This is a modified fork with the following changes:
> - YouTube Shorts auto-uploads 5x/day on a staggered schedule (no manual confirmation)
> - Twitter posts 6x/day — 4 plain tweets + 2 affiliate tweets, fully staggered from YouTube
> - Cold outreach now targets businesses with **no website** and contacts them via **Twilio SMS** instead of email
> - Contacted businesses are cached so the same number is never texted twice
> - Affiliate links are configured in `config.json` and cycled automatically

---

## Requirements

- Python 3.12
- Firefox browser
- [Ollama](https://ollama.com/) with a pulled model (e.g. `ollama pull llama3.2:3b`)
- [ImageMagick](https://imagemagick.org/)
- [Go](https://golang.org/) — required for outreach scraper only
- A [Twilio](https://twilio.com/) account with A2P 10DLC registration — required for SMS outreach
- A [Google Gemini API key](https://aistudio.google.com/) — free tier is sufficient

---

## Installation

```bash
git clone <your-fork-url>
cd MoneyPrinterV2
cp config.example.json config.json
python -m venv venv

# Windows
.\venv\Scripts\activate

# Mac/Linux
source venv/bin/activate

pip install -r requirements.txt
```

---

## Configuration

Open `config.json` and fill in:

| Field | Description |
|---|---|
| `firefox_profile` | Path to your Firefox profile folder (must be logged into YouTube/Twitter) |
| `nanobanana2_api_key` | Your Google Gemini API key |
| `ollama_model` | Ollama model name e.g. `llama3.2:3b` (or leave blank to pick on startup) |
| `imagemagick_path` | Full path to ImageMagick binary |
| `twilio_account_sid` | From your Twilio console |
| `twilio_auth_token` | From your Twilio console |
| `twilio_from_number` | Your Twilio phone number in E.164 format e.g. `+13125550100` |
| `sms_message_template` | Your intro SMS — use `{{COMPANY_NAME}}` as a placeholder |
| `affiliate_links` | Array of Amazon affiliate URLs e.g. `["https://amzn.to/abc", "https://amzn.to/xyz"]` |
| `google_maps_scraper_niche` | Search query for Google Maps e.g. `"plumbers in Chicago"` |

---

## Schedule (automatic once CRON is set up)

| Time | Action |
|---|---|
| 12:00am | YouTube — generate + upload Short |
| 1:00am | Twitter — plain tweet |
| 4:00am | YouTube — generate + upload Short |
| 5:00am | Twitter — plain tweet |
| 8:00am | YouTube — generate + upload Short |
| 9:00am | Twitter — affiliate tweet |
| 12:00pm | Twitter — plain tweet |
| 4:00pm | YouTube — generate + upload Short |
| 5:00pm | Twitter — affiliate tweet |
| 8:00pm | YouTube — generate + upload Short |
| 9:00pm | Twitter — plain tweet |

---

## Usage

### Terminal 1 — YouTube
```bash
python src/main.py
# Select 1 (YouTube) → select your account → select 3 (Setup CRON) → leave running
```

### Terminal 2 — Twitter
```bash
python src/main.py
# Select 2 (Twitter) → select your account → select 3 (Setup CRON) → leave running
```

### Terminal 3 — Outreach (run manually 2–3x/week)
```bash
bash scripts/run_outreach.sh
# Or: python src/main.py → select 4 (Outreach)
```

---

## Twilio Setup (required for outreach)

1. Create a Twilio account at [twilio.com](https://twilio.com)
2. Buy a phone number (~$1.15/month)
3. Complete **A2P 10DLC registration** — required for bulk SMS in the US
   - Brand registration: ~$4 one-time
   - Campaign registration: ~$20 one-time
   - Approval takes 1–2 weeks — plan ahead
4. Fill in `twilio_account_sid`, `twilio_auth_token`, `twilio_from_number` in `config.json`

---

## Notes

- The YouTube generator takes 20–30 min per video. The schedule is staggered so YouTube and Twitter never run at the same time.
- Affiliate tweets cycle through your `affiliate_links` array automatically — add as many links as you want.
- The outreach cache (`mp/outreach_contacted.json`) tracks every number ever texted. Delete this file to reset and re-contact businesses.
- Keep both Terminal 1 and Terminal 2 running at all times. They use Python's `schedule` library, not OS-level cron, so closing the terminal stops the schedule.
