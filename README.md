# CampusDiary

CampusDiary is a backend service for a campus lost-and-found and community board. Students can report lost or found items, get matched with the right person through in-app chat, and post general campus updates, while an admin layer keeps the platform moderated.

## Features

- Email OTP verification before account registration, using a time-limited one-time code sent over SMTP
- JWT-based authentication for all protected actions
- Lost item reporting with title, description, location, and image
- Found item reporting with the same structure, linked to the reporting user
- In-app chat between a reporter and a finder tied to a specific item, with unread message counts and read receipts
- Community posts board for general updates, with support for pinned posts
- Image upload endpoint with file type validation and randomized filenames to avoid collisions
- Admin routes to list users, view all lost and found items, inspect messages, and delete items, gated behind an isAdmin flag on the user account

## Tech Stack

- Backend: Python, Flask
- Database: MongoDB Atlas, accessed through PyMongo
- Auth: Flask-JWT-Extended for token issuance and route protection, Werkzeug for password hashing
- Email: SMTP (Gmail) for OTP delivery
- Other: Flask-CORS for cross-origin requests, python-dotenv for environment configuration, Pillow for image handling, Gunicorn for production serving

## Architecture

The application follows a blueprint-based structure, with each domain area registered as its own Flask blueprint:

- `auth_routes` — OTP generation and verification, registration, login
- `lost_routes` — create and list lost items
- `found_routes` — create and list found items
- `chat_routes` — send messages, fetch conversation by item, track unread counts, mark messages read
- `admin_routes` — user and item management restricted to admin accounts
- `upload_routes` — image upload and storage

All data lives in a single MongoDB database (`CampusDiaryDB`) split across collections for users, lost items, found items, posts, messages, and OTPs.

## Getting Started

### Prerequisites

- Python 3.11+
- A MongoDB Atlas connection string
- An SMTP-capable email account for sending OTPs

### Installation

```bash
git clone https://github.com/abhiiimanyu-x7/CampusDiary.git
cd CampusDiary
python -m venv venv
venv\Scripts\activate      # Windows
pip install -r requirements.txt
```

### Environment Variables

Create a `.env` file in the project root with the following keys:

```
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=your_jwt_secret
ADMIN_EMAIL=admin_account_email

EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=465
EMAIL_USER=your_email
EMAIL_PASS=your_email_app_password
EMAIL_USE_SSL=true
OTP_EXPIRY_SECONDS=300
```

### Running the server

```bash
python app.py
```

The server starts on the default Flask port and prints a MongoDB connection check on startup. A running instance responds at `/` with a simple status message confirming the backend is up.

## API Overview

| Area | Base path |
|---|---|
| Auth | `/auth` |
| Lost items | `/lost` |
| Found items | `/found` |
| Chat | `/chat` |
| Admin | `/admin` |
| Uploads | `/upload` |

Most write operations require a valid JWT obtained from `/auth/login`, passed as a Bearer token.
