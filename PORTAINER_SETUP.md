# Portainer Deployment Guide

## 🎯 Quick Setup for roosterbots.designbuildautomate.io

### Step 1: Access Portainer

Go to your Portainer instance (usually `https://portainer.designbuildautomate.io:9000`)

---

### Step 2: Create New Stack

1. Navigate to **Stacks** → **Add Stack**
2. **Name**: `roosterbots-site`
3. **Build method**: Select **"Repository"**
4. **Repository URL**: `https://github.com/jtalborough/roosterbots-site`
5. **Repository reference**: `refs/heads/main`
6. **Compose path**: `docker-compose.yml`

**OR** use **Web editor** and paste the contents of `docker-compose.yml`

---

### Step 3: Add Environment Variables

Scroll down to **Environment variables** section and add these:

#### Required Variables:

```
STRIPE_SECRET_KEY=sk_live_your_actual_key
STRIPE_WEBHOOK_SECRET=whsec_your_actual_secret
BASE_URL=https://roosterbots.designbuildautomate.io
ALLOWED_ORIGINS=https://roosterbots.designbuildautomate.io
```

#### Optional (with defaults):

```
NODE_ENV=production
PORT=3000
```

**Format in Portainer:**
- Click **"+ add environment variable"** for each one
- Name: `STRIPE_SECRET_KEY`
- Value: `sk_live_xxxxx`

---

### Step 4: Deploy Stack

1. Click **"Deploy the stack"**
2. Wait for containers to build and start
3. Check logs if there are errors

---

### Step 5: Enable Webhook (for GitHub Actions)

1. Click on your deployed stack (`roosterbots-site`)
2. Scroll to **Webhooks** section
3. Toggle **"Enable webhook"**
4. Copy the webhook URL
   - Format: `https://portainer.xxx/api/webhooks/xxxxx-xxxx-xxxx`

---

### Step 6: Add Webhook to GitHub

1. Go to: https://github.com/jtalborough/roosterbots-site/settings/secrets/actions
2. Click **"New repository secret"**
3. **Name**: `PORTAINER_WEBHOOK_URL`
4. **Value**: (paste webhook URL from Portainer)
5. Click **"Add secret"**

---

## ✅ Verification

### Check Containers are Running

In Portainer:
- **Containers** → You should see:
  - `ftc-web` (running)
  - `ftc-api` (running)

### Check API Health

```bash
curl https://roosterbots.designbuildautomate.io/api/health
```

Expected response:
```json
{"status":"ok","timestamp":"2025-10-09T..."}
```

### Check Website

Visit: https://roosterbots.designbuildautomate.io

You should see your Hugo site!

---

## 🔧 Troubleshooting

### Container Won't Start

**Check logs in Portainer:**
1. Click on the container
2. View **Logs**
3. Look for error messages

**Common issues:**
- Missing environment variables
- Port conflicts (80, 3000 already in use)
- Build errors

### API Container Fails

**Check environment variables are set:**
```bash
# In Portainer, click on ftc-api container
# Go to "Inspect" → "Env"
# Verify all variables are present
```

**Missing Stripe keys will cause startup to succeed but payments to fail**

### Web Container Can't Connect to API

**Verify network:**
- Both containers should be on `ftc-network`
- API should be accessible at `http://api:3000` from web container

---

## 🔄 Updating the Site

### Automatic Updates (via GitHub Actions)

1. Make changes locally
2. Commit and push to `main` branch:
   ```bash
   git add .
   git commit -m "Update content"
   git push
   ```
3. GitHub Actions will automatically:
   - Build Docker images
   - Push to GitHub Container Registry
   - Trigger Portainer webhook
   - Redeploy containers

### Manual Update in Portainer

1. Go to **Stacks** → `roosterbots-site`
2. Click **"Pull and redeploy"**
3. Or click **"Update the stack"** to change configuration

---

## 🌐 SSL/HTTPS Setup

Your site needs HTTPS. Options:

### Option 1: Nginx Proxy Manager (Recommended)

```bash
docker run -d -p 80:80 -p 443:443 -p 81:81 \
  --name nginx-proxy-manager \
  --restart unless-stopped \
  -v nginx-data:/data \
  -v nginx-letsencrypt:/etc/letsencrypt \
  jc21/nginx-proxy-manager:latest
```

Then:
1. Visit `http://your-server-ip:81`
2. Login (default: `admin@example.com` / `changeme`)
3. Add Proxy Host:
   - Domain: `roosterbots.designbuildautomate.io`
   - Forward to: `ftc-web` (or container IP)
   - Port: `80`
   - Enable SSL with Let's Encrypt

### Option 2: Traefik

Add Traefik to your `docker-compose.yml` (we can do this if needed)

### Option 3: Cloudflare

Use Cloudflare proxy with SSL (easiest if you use Cloudflare DNS)

---

## 📊 Monitoring

### Check Container Status

```bash
docker ps | grep ftc
```

### View Logs

```bash
# Web container
docker logs ftc-web

# API container
docker logs ftc-api -f
```

### Check Resource Usage

In Portainer:
- **Containers** → Click container → **Stats**
- Monitor CPU, Memory, Network usage

---

## 🔐 Security Checklist

- [ ] Stripe keys are in environment variables (not in code)
- [ ] `.env` file is gitignored
- [ ] HTTPS/SSL is enabled
- [ ] Portainer is password protected
- [ ] Webhook URL is kept secret
- [ ] CORS is configured (`ALLOWED_ORIGINS`)
- [ ] Rate limiting is enabled (already in API)

---

## 🆘 Need Help?

**Container logs show errors:**
- Check environment variables are correct
- Verify Stripe keys are valid
- Check port availability

**GitHub Actions failing:**
- Verify `PORTAINER_WEBHOOK_URL` secret is set
- Check webhook URL is accessible
- Review Actions logs in GitHub

**Site not accessible:**
- Check DNS points to your server
- Verify ports 80/443 are open
- Check SSL certificate is valid
- Verify containers are running

---

## 📝 Environment Variables Reference

| Variable | Required | Example | Description |
|----------|----------|---------|-------------|
| `STRIPE_SECRET_KEY` | ✅ Yes | `sk_live_xxx` | Stripe API secret key |
| `STRIPE_WEBHOOK_SECRET` | ✅ Yes | `whsec_xxx` | Stripe webhook signing secret |
| `BASE_URL` | ✅ Yes | `https://roosterbots.designbuildautomate.io` | Your site URL |
| `ALLOWED_ORIGINS` | ✅ Yes | `https://roosterbots.designbuildautomate.io` | CORS allowed origins |
| `NODE_ENV` | ⚠️ Optional | `production` | Node environment (default: production) |
| `PORT` | ⚠️ Optional | `3000` | API port (default: 3000) |

---

## ✨ Next Steps After Deployment

1. **Test the site**: Visit https://roosterbots.designbuildautomate.io
2. **Test API**: `curl https://roosterbots.designbuildautomate.io/api/health`
3. **Configure Stripe webhook**: Point to `https://roosterbots.designbuildautomate.io/api/webhook`
4. **Test checkout flow**: Try purchasing from store (test mode first!)
5. **Monitor logs**: Watch for any errors
6. **Set up backups**: Backup your Portainer configuration

---

**Your site is now live!** 🎉

Any issues? Check the logs in Portainer or let me know!
