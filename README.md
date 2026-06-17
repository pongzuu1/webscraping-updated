# Starlink_Scrape

A small Flask app that scrapes Starlink account usage using a Firefox profile and exports usage data as JSON/CSV.

## Features
- Scrapes Starlink telemetry using a Firefox profile
- Returns cleaned JSON from the `/scrape` endpoint
- Exports CSV to `output/starlink_usage.csv` and provides a `/download` endpoint
- Falls back to `output/data_usage.json` when live scraping/auth fails (demo mode)

## Prerequisites
- Python 3.8+
- Firefox (the scraper is written for Firefox only)
- A Firefox profile logged in to Starlink

## Install
Install dependencies from `requirements.txt`:

```bash
pip install -r requirements.txt
```

## Environment
Create a `.env` file next to `app.py` (or set environment variables). Example:

```env
STARLINK_PROFILE_PATH=C:\Users\<you>\AppData\Roaming\Mozilla\Firefox\Profiles\<profile>.StarlinkScraper
STARLINK_BROWSER=firefox
```

- `STARLINK_PROFILE_PATH`: path to a Firefox profile folder that contains an active Starlink login session.
- `STARLINK_BROWSER`: must be `firefox` (project uses Firefox only).

## Run (development)
```bash
python app.py
```
The app runs on `http://127.0.0.1:5000` by default (Flask debug mode enabled in `app.py`).

## Endpoints
- `GET /` — serves the frontend (`templates/index.html`).
- `GET /scrape` — triggers the scraper. Calls `fetch_raw_data(profile, dashboard, api, browser)` from `scraper/engine.py` and returns processed JSON (or falls back to `output/data_usage.json`).
- `GET /download` — downloads `output/starlink_usage.csv` if present.

## Notes & Troubleshooting
- The scraper expects arguments in this order: `fetch_raw_data(profile_path, dashboard_url, api_url, browser_type)`.
- If you see `Auth failed and no backup data found.`, ensure `STARLINK_PROFILE_PATH` points to a profile with an active Starlink session.
- If the profile is locked by a running Firefox instance, close Firefox or create a dedicated profile for scraping.
- Check the terminal running `python app.py` for exceptions and logs.

## How to capture a Firefox profile (short)
1. Open `about:profiles` in Firefox.
2. Create or use a profile and sign in to Starlink in that profile.
3. Note the profile folder path and set `STARLINK_PROFILE_PATH` to it.

## Security & Production
- Do not commit `.env` or profile folders.
- For production: disable Flask `debug`, run under a WSGI server, and secure access to endpoints and profile data.

## Files of interest
- `app.py` — Flask entrypoint
- `requirements.txt` — dependencies
- `scraper/engine.py` — browser automation & fetch logic
- `scraper/exporter.py` — JSON → cleaned data / CSV exporter
- `output/data_usage.json` — optional fallback JSON
- `output/starlink_usage.csv` — generated CSV
