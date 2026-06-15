# 🚀 Render Free Tier Deployment - Step-by-Step Guide

## ✅ What's Been Prepared

Your repository now includes:
- ✅ `render.yaml` - Automated deployment configuration
- ✅ `Procfile` - Process definition for Render
- ✅ `Dockerfile` - Backend container specification
- ✅ Updated `requirements.txt` with production dependencies
- ✅ Frontend build configuration in `package.json`

---

## 📋 Prerequisites

1. **Render Account** - Sign up FREE at [render.com](https://render.com)
2. **GitHub Connected** - Your repo is public and ready
3. **Environment Variables Ready** - See section below

---

## 🚀 Deploy in 3 Steps

### Step 1: Connect GitHub to Render

1. Go to https://dashboard.render.com
2. Click **Dashboard** → **New**
3. Select **Web Service** (for backend)
4. Choose "Connect a repository"
5. Select `Gayatri1251070245/timetable-system`

### Step 2: Configure Backend Service

**Fill in these fields:**

```
Name:                    timetable-backend
Runtime:                 Python
Root Directory:          backend
Build Command:           pip install -r requirements.txt
Start Command:           gunicorn main:app --worker-class uvicorn.workers.UvicornWorker --bind 0.0.0.0:$PORT
Plan:                    Free
Auto-deploy:             Yes (recommended)
```

**Add Environment Variables:**

| Key | Value | Required |
|-----|-------|----------|
| `DATABASE_URL` | `sqlite:///./timetable.db` | Yes |
| `SECRET_KEY` | Generate random key | Yes |
| `REDIS_URL` | `redis://localhost` | No (optional) |
| `ENVIRONMENT` | `production` | Yes |
| `CORS_ORIGINS` | `["https://timetable-frontend.onrender.com"]` | Yes |

> **🔐 To generate a SECRET_KEY:**
> ```bash
> python -c "import secrets; print(secrets.token_urlsafe(32))"
> ```

Click **Create Web Service** and wait for deployment ⏳

### Step 3: Deploy Frontend

Once backend is deployed:

1. Click **New** → **Static Site**
2. Connect same repository (`timetable-system`)
3. Fill in:

```
Name:                    timetable-frontend
Root Directory:          frontend
Build Command:           npm install && npm run build
Publish Directory:       dist
Plan:                    Free
```

**Environment Variables:**

| Key | Value |
|-----|-------|
| `VITE_API_URL` | `https://timetable-backend.onrender.com` |
| `VITE_WS_URL` | `wss://timetable-backend.onrender.com` |
| `NODE_ENV` | `production` |

Click **Create Static Site** ✅

---

## 📊 Your Deployment URLs

After deployment completes:

- **Backend API:** `https://timetable-backend.onrender.com`
- **Frontend:** `https://timetable-frontend.onrender.com`

---

## ⚠️ Free Tier Important Notes

| Feature | Free Tier Behavior |
|---------|-------------------|
| **Web Service (Backend)** | Spins down after 15 min of inactivity (causes ~30s delay on first request) |
| **Static Site (Frontend)** | Always running ✅ |
| **Storage** | 0.5 GB per service |
| **Memory** | 0.5 GB RAM |
| **Build Time** | ~5-10 minutes first time |
| **Bandwidth** | 100 GB/month |

---

## 🔄 Auto-Deployment

Once configured, every push to `master` branch triggers automatic deployment:

```bash
git add .
git commit -m "Update deployment config"
git push origin master
```

Check deployment status in Render Dashboard → Service Logs

---

## 🐛 Troubleshooting

### Backend fails to start
- Check logs: Dashboard → Service → Logs
- Verify `main.py` exists in `backend/` directory
- Check all environment variables are set

### Frontend can't connect to backend
- Verify `VITE_API_URL` matches backend service URL
- Check CORS settings in backend:
  ```python
  app.add_middleware(
      CORSMiddleware,
      allow_origins=["https://timetable-frontend.onrender.com"],
      allow_credentials=True,
      allow_methods=["*"],
      allow_headers=["*"],
  )
  ```

### Build fails
- Run `npm install` locally to check for errors
- Run `pip install -r requirements.txt` locally to verify dependencies

### Service spinning down
- Free tier limitation - upgrade to paid for always-on
- Or use Render's "Keep Alive" pings to prevent spin-down

---

## 💰 Upgrade Path

When ready for paid features:

1. **Render Paid Tier** - $7/month (no spin-down)
2. **Add PostgreSQL Database** - $7/month (free tier available)
3. **Add Redis Cache** - $10/month

Or use alternatives:
- Railway
- Replit
- Fly.io
- Heroku

---

## 📚 Useful Links

- [Render Docs](https://render.com/docs)
- [Python on Render](https://render.com/docs/deploy-python)
- [Node.js on Render](https://render.com/docs/deploy-node)
- [render.yaml Spec](https://render.com/docs/infrastructure-as-code)

---

## ✨ Next Steps

1. **Sign up at Render**: https://render.com
2. **Connect GitHub** to your Render account
3. **Create Web Service** (Backend) using steps above
4. **Create Static Site** (Frontend) using steps above
5. **Monitor deployment** in Render Dashboard

---

**Happy Deploying! 🎉**
