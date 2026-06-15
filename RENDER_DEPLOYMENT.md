# Render Deployment Guide

## Prerequisites
- Render account (free tier available at https://render.com)
- GitHub repository pushed with deployment files

## Free Tier Limitations
- **Backend (Web Service)**: Spins down after 15 minutes of inactivity
- **Frontend (Static Site)**: Always available
- **Database**: PostgreSQL free tier available
- **Memory**: 0.5 GB RAM
- **Storage**: Limited disk space

## Deployment Steps

### 1. Backend Deployment (FastAPI)

1. Go to [Render Dashboard](https://dashboard.render.com)
2. Click **New +** → **Web Service**
3. Connect your GitHub repository (`timetable-system`)
4. Configure:
   - **Name**: `timetable-backend`
   - **Root Directory**: `backend`
   - **Environment**: `Python 3`
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `uvicorn main:app --host 0.0.0.0 --port $PORT`
   - **Plan**: Free tier

5. Add Environment Variables:
   ```
   DATABASE_URL=sqlite:///./test.db  (or PostgreSQL connection string)
   REDIS_URL=redis://localhost:6379  (external Redis recommended)
   SECRET_KEY=your-secret-key-here
   PYTHON_VERSION=3.11
   ```

6. Click **Create Web Service**

### 2. Frontend Deployment (React/Vite)

1. Go to [Render Dashboard](https://dashboard.render.com)
2. Click **New +** → **Static Site**
3. Connect your GitHub repository
4. Configure:
   - **Name**: `timetable-frontend`
   - **Root Directory**: `frontend`
   - **Build Command**: `npm install && npm run build`
   - **Publish Directory**: `dist`
   - **Plan**: Free tier

5. Add Environment Variables:
   ```
   VITE_API_URL=https://timetable-backend.onrender.com  (your backend URL)
   NODE_ENV=production
   ```

6. Click **Create Static Site**

### 3. Database Setup (Optional - for production)

**For PostgreSQL on Render:**
1. Click **New +** → **PostgreSQL**
2. Choose free tier
3. Copy connection string and add to backend environment variables

**Or use external services:**
- Neon PostgreSQL (free tier available)
- Supabase
- Firebase

### 4. Redis Setup (Optional - for caching)

Use free Redis service:
- **Upstash** (free tier with 10GB)
- **Redis Cloud** (free tier)

Add `REDIS_URL` to backend environment variables

## Important Notes

### For Free Tier:
- Services spin down after 15 min inactivity (causes ~30s delay on first request)
- Database may have size limits
- Review Render's free tier terms

### Configuration Adjustments:
```python
# In backend/main.py - disable Redis if not available
try:
    redis = redis.from_url(os.getenv("REDIS_URL", "redis://localhost:6379"))
except:
    redis = None  # Fallback if Redis unavailable
```

### CORS Configuration:
```python
# Add to backend/main.py
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://timetable-frontend.onrender.com"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

## Deployment Workflow

1. Push changes to GitHub
2. Render auto-deploys on push (if enabled)
3. Monitor deployment logs in Render Dashboard
4. Check service health and logs

## Troubleshooting

- **Backend not starting**: Check logs in Render dashboard
- **Frontend can't connect to backend**: Verify `VITE_API_URL` in environment
- **Slow first requests**: Normal on free tier (service wake-up)
- **Database connection issues**: Verify connection string in environment variables

## Upgrade Path

When ready to upgrade:
- Render paid tiers (no spin-down, more resources)
- Railway, Heroku alternatives
- AWS, Google Cloud, Azure

---

For more help, visit: https://render.com/docs
