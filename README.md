🎯 EventRadar — Full-Stack Event Scraping & Analytics System

A production-ready pipeline that automatically scrapes real-world events (concerts, workshops, conferences, meetups — NOT movies) from multiple event platforms, deduplicates the data intelligently, syncs it to Google Sheets, and displays live analytics on a publicly hosted dashboard.


📌 Objective
Build a production-ready, full-stack system that:

Scrapes only EVENTS (not movies or entertainment listings) from event platforms
Stores and deduplicates the data using a 3-layer deduplication engine
Syncs automatically to Google Sheets for easy access
Displays live analytics on a public dashboard URL

This project demonstrates real-world full-stack engineering skills across web scraping, automation, data handling, cloud deployment, and analytics.

🌐 Live Demo
ResourceURL📊 Live Dashboardhttps://your-app.railway.app🔗 Google SheetEventRadar Data Sheet❤️ Health Checkhttps://your-app.railway.app/api/health

✨ Features

Multi-platform scraping — Eventbrite, Meetup, and Allevents.in
Smart deduplication — 3-layer engine (Exact ID → Content Hash → Fuzzy Title Match)
Google Sheets sync — All events auto-synced in real time
Live analytics dashboard — Charts, filters, and event stats on a public URL
Scheduled scraping — Runs automatically every 2 hours
REST API — Trigger scrapes, fetch filtered events, view analytics via endpoints
No database needed — Google Sheets acts as the data store


🧠 How Deduplication Works
EventRadar uses a 3-layer deduplication engine to ensure no event is stored twice, even across different platforms:
Layer 1 — Exact Source ID
Each platform assigns a unique ID to every event. If eventbrite:event_123456 has been seen before, it gets skipped immediately.
Layer 2 — Content Hash
A SHA-256 hash is computed from: normalized_title | YYYY-MM-DD | normalized_city. This catches the same real-world event listed on multiple platforms under slightly different names.
Layer 3 — Fuzzy Title Match
Uses fuzzy string matching (fuzz.token_sort_ratio). If two events are ≥ 85% similar in title AND share the same date AND city, the newer one is flagged as a duplicate.

🗂️ Project Structure
event-scraper/
├── main.py              ← FastAPI app + scheduler
├── pipeline.py          ← Orchestrates scrape → dedup → store
├── deduplication.py     ← 3-layer dedup engine
├── sheets.py            ← Google Sheets read/write
├── analytics.py         ← Compute stats from events
├── scrapers/
│   ├── base.py          ← Base scraper class
│   ├── eventbrite.py    ← Eventbrite scraper
│   ├── meetup.py        ← Meetup GraphQL scraper
│   └── allevents.py     ← Allevents.in scraper
├── static/
│   └── index.html       ← Dashboard UI
├── credentials.json     ← ⚠️ Google service account (DO NOT COMMIT)
├── .env                 ← ⚠️ Your config (DO NOT COMMIT)
├── .env.example         ← Template for environment variables
├── requirements.txt     ← Python dependencies
└── Dockerfile           ← For deployment

📊 Google Sheets Structure
SheetContentseventsAll scraped events (master data)statsAggregated metrics, updated every scrapelogEvery scrape run with timestamps and counts

🔌 API Endpoints
MethodEndpointDescriptionGET/Live analytics dashboardGET/api/healthHealth check — returns {"status": "healthy"}GET/api/configShows current scraper configGET/api/dashboardRaw analytics JSONGET/api/events?city=Mumbai&limit=10Filtered events by cityPOST/api/scrape/triggerManually trigger a scrape

⚙️ Setup & Installation
Prerequisites

Python 3.11+
A Google Cloud account
Git

Step 1 — Clone & Install
bashgit clone https://github.com/YOUR_USERNAME/eventradar.git
cd eventradar

pip install -r requirements.txt
playwright install chromium
Step 2 — Google Sheets Setup

Go to Google Cloud Console → Create a new project named EventRadar
Enable the Google Sheets API under APIs & Services → Library
Create a Service Account → Download the JSON key → Save as credentials.json in the project root
Create a new Google Spreadsheet, name it EventRadar Data
Share it with the service account's client_email (from credentials.json) with Editor access
Copy the Spreadsheet ID from the URL

Step 3 — Configure Environment
bashcp .env.example .env
Edit .env:
envSPREADSHEET_ID=your_spreadsheet_id_here
GOOGLE_CREDENTIALS_PATH=credentials.json
SCRAPER_CITIES=Mumbai,Delhi,Bangalore,Pune
SCRAPER_CATEGORIES=technology,music,business
SCRAPE_INTERVAL_HOURS=2
PORT=8000
Step 4 — Run Locally
bashpython main.py
Visit http://localhost:8000 — your dashboard is live!
To manually trigger a scrape:
bashcurl -X POST http://localhost:8000/api/scrape/trigger

🚀 Deployment (Railway — Free Public URL)

Push your code to GitHub:

bashgit init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/YOUR_USERNAME/eventradar.git
git push -u origin main

Go to railway.app → New Project → Deploy from GitHub → Select your repo
Add these environment variables in the Railway dashboard:

SPREADSHEET_ID         = your_sheet_id
GOOGLE_CREDENTIALS_JSON = <paste entire contents of credentials.json>
SCRAPER_CITIES         = Mumbai,Delhi,Bangalore,Pune
SCRAPER_CATEGORIES     = technology,music,business
PORT                   = 8000
Railway will give you a public URL like: https://eventradar-production.railway.app 🎉

Alternative: Render.com — same process, gives you https://eventradar.onrender.com


🔧 Customization
What to changeWhereAdd more citiesSCRAPER_CITIES in .envAdd more categoriesSCRAPER_CATEGORIES in .envChange scrape frequencySCRAPE_INTERVAL_HOURS in .envAdd a new platformCreate scrapers/yourplatform.py extending BaseScraper, implement fetch_events(), register in scrapers/__init__.py

🔒 Security

Never commit credentials.json or .env to git
Both are listed in .gitignore
On Railway, credentials are passed as an environment variable (GOOGLE_CREDENTIALS_JSON), never as a file
If you accidentally expose your credentials, immediately revoke them from Google Cloud Console → IAM → Service Accounts → Keys


📦 Tech Stack
LayerTechnologyBackendPython, FastAPIScrapingPlaywright, BeautifulSoup, httpxDeduplicationRapidFuzz, hashlibStorage / SyncGoogle Sheets API, gspreadSchedulingAPSchedulerFrontendHTML, CSS, JavaScript (Chart.js)DeploymentRailway / Render (Docker)

📄 License
MIT License — feel free to use, modify, and distribute.
