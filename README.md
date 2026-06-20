# Recordboxd

A Letterboxd-style album logging app — log albums you've listened to,
rate them with half-star precision, and write short reviews.

Built as a learning project to practice full-stack development:
a Python (FastAPI) backend with a real SQLite database, and a
JavaScript frontend that talks to it.

## Stack

- **Backend:** Python, FastAPI, SQLite
- **Frontend:** HTML, CSS, JavaScript (no framework yet, kept simple
  while learning the fundamentals)

## Features

- Log an album: title, artist, half-star rating (out of 5), optional review
- View your full log, most recent first
- Remove a logged album

## Running it locally

**Backend:**
```bash
cd backend
pip install -r requirements.txt
uvicorn main:app --reload
```
This starts the API at `http://localhost:8000`.

**Frontend:**
Open `frontend/index.html` directly in a browser. It expects the
backend to be running at `http://localhost:8000`.

## Roadmap

- [ ] User accounts (so logs are personal, not shared)
- [ ] Spotify integration for automatic listening history
- [ ] Stats page (most-listened artists, albums per month, etc.)
- [ ] Deploy live (Vercel for frontend, Railway/Render for backend)
