# GitPulse — GitHub Audience Tracker


## About The Project

GitPulse is a full-stack GitHub audience tracker built with FastAPI and MongoDB. On every sync it fetches your followers and following from the GitHub REST API, diffs them against the stored snapshot in MongoDB, and logs every change as a timestamped event. The event log is append-only so the full history is always preserved.

The dashboard has three views — current followers newest first, current following in GitHub order, and lost followers built from a MongoDB aggregation that deduplicates by user and excludes anyone who has re-followed. A daily cron job via GitHub Actions keeps everything up to date automatically.

## Library Requirements

- FastAPI
- Uvicorn
- PyMongo
- Requests
- Python-dotenv
- APScheduler

## Getting Started

This will help you understand how to set up GitPulse to track your own GitHub followers. To get a local copy up and running follow these simple steps.

## Installation Steps

### Option 1: Installation from GitHub

1. **Clone the Repository**

   ```bash
   git clone https://github.com/mukarma1/GitPulse.git
   cd GitPulse
   ```

2. **Create a Virtual Environment**

   ```bash
   python -m venv backend/venv
   ```

3. **Activate the Virtual Environment**

   Windows:
   ```bash
   backend\venv\Scripts\activate
   ```

   macOS / Linux:
   ```bash
   source backend/venv/bin/activate
   ```

4. **Install Dependencies**

   ```bash
   pip install -r backend/requirements.txt
   ```

5. **Configure Environment Variables**

   ```bash
   cp backend/.env.example backend/.env
   ```

   Open `backend/.env` and fill in your values — see the [API Key Setup](#api-key-setup) section below.

6. **Run the Backend**

   ```bash
   cd backend
   uvicorn app.main:app --reload
   ```

7. **Open the Frontend**

   Open `frontend/index.html` directly in your browser. No build step or dev server needed.

   Click **Sync Now** to pull in your GitHub followers for the first time.

## API Key Setup

GitPulse needs two credentials to run — a GitHub token and a MongoDB connection string.

### 1. GitHub Personal Access Token (`GITHUB_TOKEN`)

This is required. Without it, GitHub blocks the following list endpoint entirely (authentication required) and applies strict rate limits on all other endpoints.

**Steps to create one:**

1. Go to [github.com/settings/tokens](https://github.com/settings/tokens)
2. Click **Generate new token (classic)**
3. Set a name like `gitpulse` and choose an expiration
4. Select the following scope:
   - `read:user` — to read your profile and your own following list
5. Click **Generate token** and copy it immediately — you cannot see it again

Add it to `backend/.env`:
```dotenv
GITHUB_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

**Note:** Keep your token private. Never commit it to a public repository.

### 2. MongoDB Connection URI (`MONGODB_URI`)

GitPulse stores all follower snapshots and event history in MongoDB.

**For local development**, a local MongoDB instance works:
```dotenv
MONGODB_URI=mongodb://localhost:27017
```

**For production** (required for GitHub Actions nightly sync), use MongoDB Atlas:

1. Create a free account at [cloud.mongodb.com](https://cloud.mongodb.com)
2. Create a free **M0 cluster**
3. Go to **Database Access** → create a user with read/write access
4. Go to **Network Access** → Add IP `0.0.0.0/0` (allow from anywhere — needed for GitHub Actions dynamic IPs)
5. Go to your cluster → **Connect** → **Drivers** → copy the URI

```dotenv
MONGODB_URI=mongodb+srv://db-user:password@cluster0.xxxxx.mongodb.net/?appName=Cluster0
```

### Complete `.env` file

```dotenv
GITHUB_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
GITHUB_USERNAME=your-github-username
MONGODB_URI=mongodb+srv://db-user:password@cluster0.xxxxx.mongodb.net/?appName=Cluster0
DB_NAME=github_analytics
SYNC_INTERVAL_MINUTES=60
```

 Auther: Mukarma Rehman 
 github:https://github.com/mukarma1
