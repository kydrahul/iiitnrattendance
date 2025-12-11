# Render Deployment Guide - IIIT NR Attendance System Backend

## 📋 Overview

Your backend is deployed on **Render.com** as a Web Service. This guide explains how to manage the deployment and keep it running 24/7.

---

## 🚀 How Render Works

### Free Tier Limitations
- **Automatic Spin Down**: Free tier services automatically sleep after **15 minutes of inactivity**
- **Cold Start**: When a request comes in, it takes **30-60 seconds** to wake up
- **Monthly Limit**: 750 hours/month of runtime (enough for 24/7 if you upgrade to paid)

### Paid Tier Benefits ($7/month)
- **Always On**: No automatic sleep
- **Faster Performance**: Better CPU and memory
- **Custom Domains**: Use your own domain
- **No Cold Starts**: Instant response times

---

## 🔧 Keeping Your Backend Running (Free Tier)

### Option 1: External Ping Service (Recommended for Free Tier)

Use a free service to ping your backend every 5-10 minutes to prevent it from sleeping:

#### **UptimeRobot** (Free, Recommended)
1. Go to [uptimerobot.com](https://uptimerobot.com)
2. Sign up for a free account
3. Click "Add New Monitor"
4. Configure:
   - **Monitor Type**: HTTP(s)
   - **Friendly Name**: IIIT NR Backend
   - **URL**: `https://your-backend-url.onrender.com/health`
   - **Monitoring Interval**: 5 minutes
5. Click "Create Monitor"

#### **Cron-Job.org** (Alternative)
1. Go to [cron-job.org](https://cron-job.org)
2. Sign up and create a new cron job
3. Set URL: `https://your-backend-url.onrender.com/health`
4. Schedule: Every 5 minutes
5. Save

#### **Render Cron Jobs** (Built-in)
Render offers cron jobs on paid plans. You can create a simple cron job that pings your service.

### Option 2: Upgrade to Paid Plan ($7/month)

The simplest solution - your backend will **always be running**:

1. Go to your Render Dashboard
2. Select your backend service
3. Click "Upgrade" in the top right
4. Choose "Starter" plan ($7/month)
5. Confirm payment

**Benefits**:
- No cold starts
- Always responsive
- Better performance
- Worth it for production use

---

## 👥 Sharing Access with Your Collaborator

### Method 1: Add as Team Member (Recommended)

1. **Go to Render Dashboard**: [dashboard.render.com](https://dashboard.render.com)
2. **Click on your service** (the backend)
3. **Settings** → **Team**
4. **Invite Member**:
   - Enter their email
   - Set role: "Admin" (full access) or "Developer" (deploy only)
5. They'll receive an invitation email

### Method 2: Share Environment Variables (Limited Access)

If you just want them to **restart** or **view logs**:

1. Share your Render login credentials (not recommended for security)
2. Or give them the **Deploy Hook URL** (see below)

---

## 🔄 How Your Collaborator Can Start/Restart the Backend

### Via Render Dashboard (If they have access)

1. **Login to Render**: [dashboard.render.com](https://dashboard.render.com)
2. **Navigate to Services** → Select your backend service
3. **Manual Deploy**:
   - Click "Manual Deploy" → "Deploy latest commit"
   - Or click "Clear build cache & deploy" if there are issues

### Via Deploy Hook (Webhook)

Create a deploy hook so anyone can trigger a deployment:

1. **In Render Dashboard**:
   - Go to your service → **Settings**
   - Scroll to **Deploy Hook**
   - Click "Create Deploy Hook"
   - Copy the URL (looks like: `https://api.render.com/deploy/srv-xxxxx?key=yyyyy`)

2. **Share this URL** with your collaborator

3. **To Deploy**: They can trigger deployment by:
   ```bash
   curl -X POST https://api.render.com/deploy/srv-xxxxx?key=yyyyy
   ```
   
   Or simply **open the URL in a browser**

### Via Git Push (If connected to GitHub)

If your Render service is connected to GitHub:

1. Your collaborator pushes code to the main branch
2. Render **automatically detects** the push
3. Automatically builds and deploys the new version

**To enable auto-deploy**:
- Render Dashboard → Your Service → Settings
- Enable "Auto-Deploy" for your branch (usually `main`)

---

## 📊 Monitoring Your Backend

### Check if Backend is Running

Visit your health endpoint:
```
https://your-backend-url.onrender.com/health
```

**Expected Response**:
```json
{
  "status": "ok",
  "timestamp": "2025-12-11T17:27:19.000Z"
}
```

### View Logs

1. **Render Dashboard** → Your Service
2. Click **Logs** tab
3. View real-time logs
4. Look for:
   - ✅ `Server running on port 4000`
   - ✅ `Geofence configured`
   - ✅ `Firebase service account loaded`
   - ❌ Any error messages

### Check Service Status

In Render Dashboard:
- **Green dot** = Running
- **Yellow dot** = Deploying
- **Red dot** = Failed/Stopped

---

## 🔐 Environment Variables (Important!)

Your backend requires these environment variables to be set in Render:

### Required Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `FIREBASE_SERVICE_ACCOUNT_JSON` | Firebase Admin SDK credentials | `{"type":"service_account",...}` |
| `PORT` | Server port (auto-set by Render) | `4000` |
| `QR_SECRET` | Secret for QR code signing | `your-random-secret-key` |

### Optional Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `COLLEGE_LATITUDE` | Campus latitude | `21.128471766438903` |
| `COLLEGE_LONGITUDE` | Campus longitude | `81.76613230185365` |
| `COLLEGE_GEOFENCE_RADIUS` | Geofence radius (meters) | `1200` |

### How to Set Environment Variables

1. **Render Dashboard** → Your Service → **Environment**
2. Click **Add Environment Variable**
3. Enter **Key** and **Value**
4. Click **Save Changes**
5. Service will **automatically redeploy**

---

## 🛠️ Troubleshooting

### Backend is Not Responding

**Symptoms**: App shows "Network Error" or times out

**Solutions**:
1. **Check if service is sleeping** (free tier)
   - Visit `/health` endpoint
   - Wait 30-60 seconds for cold start
   
2. **Check logs** for errors
   - Render Dashboard → Logs
   - Look for crash messages

3. **Manually restart**
   - Render Dashboard → Manual Deploy

4. **Verify environment variables**
   - Ensure `FIREBASE_SERVICE_ACCOUNT_JSON` is set correctly

### Service Keeps Crashing

**Check logs for**:
- ❌ Firebase credentials error
- ❌ Port binding error
- ❌ Missing dependencies

**Solutions**:
1. **Rebuild with cache cleared**
   - Settings → "Clear build cache & deploy"

2. **Check package.json**
   - Ensure all dependencies are listed
   - Run `npm install` locally to verify

3. **Verify start command**
   - Should be: `node server.js`
   - Check in Settings → Build & Deploy

### Slow Response Times

**Causes**:
- Cold start on free tier (30-60s)
- Heavy database queries

**Solutions**:
1. **Upgrade to paid plan** (eliminates cold starts)
2. **Use ping service** (keeps it warm on free tier)
3. **Optimize database queries** (already done with caching)

---

## 📝 Quick Reference Commands

### Local Development
```bash
# Install dependencies
cd backend
npm install

# Run locally
npm start

# Run with auto-reload
npm run dev
```

### Check Backend Status
```bash
# Health check
curl https://your-backend-url.onrender.com/health

# Check cache stats (requires auth token)
curl -H "Authorization: Bearer YOUR_TOKEN" \
  https://your-backend-url.onrender.com/api/cache/stats
```

### Deploy via Git
```bash
# Push to trigger auto-deploy
git add .
git commit -m "Update backend"
git push origin main
```

---

## 🎯 Best Practices

### For Production Use

1. ✅ **Upgrade to paid plan** ($7/month) for reliability
2. ✅ **Enable auto-deploy** from GitHub
3. ✅ **Set up monitoring** (UptimeRobot)
4. ✅ **Use environment variables** for all secrets
5. ✅ **Monitor logs** regularly
6. ✅ **Set up alerts** for downtime

### For Development/Testing

1. ✅ **Use ping service** to keep free tier alive
2. ✅ **Check logs** after each deploy
3. ✅ **Test health endpoint** before testing app
4. ✅ **Keep local copy** running during development

---

## 📞 Support

### Render Support
- Documentation: [render.com/docs](https://render.com/docs)
- Community: [community.render.com](https://community.render.com)
- Status: [status.render.com](https://status.render.com)

### Your Backend Info
- **Service Type**: Web Service
- **Runtime**: Node.js
- **Start Command**: `node server.js`
- **Port**: 4000 (auto-assigned by Render)
- **Health Check**: `/health`

---

## 🔗 Important URLs

Replace `your-backend-url` with your actual Render URL:

- **Health Check**: `https://your-backend-url.onrender.com/health`
- **API Base**: `https://your-backend-url.onrender.com/api`
- **Render Dashboard**: `https://dashboard.render.com`

---

## ✅ Checklist for Your Collaborator

- [ ] Get access to Render dashboard (via team invite)
- [ ] Bookmark the service URL
- [ ] Test health endpoint
- [ ] Know how to view logs
- [ ] Know how to manually deploy
- [ ] Set up UptimeRobot (if using free tier)
- [ ] Save deploy hook URL (if provided)
- [ ] Understand environment variables
- [ ] Test deployment process once

---

**Last Updated**: December 11, 2025  
**Backend Version**: 0.1.0  
**Deployment Platform**: Render.com
