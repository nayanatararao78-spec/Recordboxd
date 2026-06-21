"""
Recordboxd backend
-------------------
This is the "brain" of the app. It runs separately from what you see
on screen, and its job is to:
  1. Store albums you log in a real database (not memory that disappears)
  2. Give the frontend a way to ask for that data (an "API")

FastAPI is the tool we're using to build that API. Think of it as a
waiter: the frontend "orders" something (e.g. "give me all albums" or
"save this new album"), and FastAPI figures out how to handle that
order and respond.
"""

from fastapi import FastAPI, HTTPException
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel
from typing import Optional
import sqlite3

# This creates the actual app object. Everything below "attaches" to it.
app = FastAPI(title="Recordboxd API")

# Browsers block websites from talking to APIs on a different address
# unless the API explicitly allows it. This unblocks that, so our
# frontend (running in the browser) can actually reach this backend.
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)

DB_PATH = "recordboxd.db"


def get_connection():
    """Open a connection to our database file."""
    conn = sqlite3.connect(DB_PATH)
    conn.row_factory = sqlite3.Row  # lets us access columns by name
    return conn


def init_db():
    """
    Create the albums table if it doesn't already exist.
    This runs once when the server starts up.
    """
    conn = get_connection()
    conn.execute("""
        CREATE TABLE IF NOT EXISTS albums (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            title TEXT NOT NULL,
            artist TEXT NOT NULL,
            rating REAL NOT NULL,
            review TEXT
        )
    """)
    conn.commit()
    conn.close()


# This describes the "shape" of data we expect when someone logs an
# album. FastAPI uses this to automatically check that requests are
# valid (e.g. rejects a request missing a title).
class AlbumIn(BaseModel):
    title: str
    artist: str
    rating: float
    review: Optional[str] = None


@app.on_event("startup")
def on_startup():
    init_db()


@app.get("/albums")
def list_albums():
    """Return every album that's been logged, most recent first."""
    conn = get_connection()
    rows = conn.execute("SELECT * FROM albums ORDER BY id DESC").fetchall()
    conn.close()
    return [dict(row) for row in rows]


@app.post("/albums")
def create_album(album: AlbumIn):
    """Save a new album log to the database."""
    conn = get_connection()
    cursor = conn.execute(
        "INSERT INTO albums (title, artist, rating, review) VALUES (?, ?, ?, ?)",
        (album.title, album.artist, album.rating, album.review),
    )
    conn.commit()
    new_id = cursor.lastrowid
    conn.close()
    return {"id": new_id, **album.dict()}


@app.delete("/albums/{album_id}")
def delete_album(album_id: int):
    """Remove a logged album by its id."""
    conn = get_connection()
    cursor = conn.execute("DELETE FROM albums WHERE id = ?", (album_id,))
    conn.commit()
    conn.close()
    if cursor.rowcount == 0:
        raise HTTPException(status_code=404, detail="Album not found")
    return {"deleted": album_id}
