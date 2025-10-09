# Cloudflare Tunnel Setup Guide

## 🌐 Why Cloudflare Tunnel?

- ✅ No need to expose ports 80/443 publicly
- ✅ No port conflicts
- ✅ Free SSL/TLS certificates
- ✅ DDoS protection
- ✅ Works behind firewalls/NAT
- ✅ Built-in load balancing and failover

---

## 📋 Prerequisites

1. Domain name (roosterbots.designbuildautomate.io)
2. Cloudflare account (free tier works!)
3. Domain DNS managed by Cloudflare

---

## 🚀 Step-by-Step Setup

### Step 1: Add Domain to Cloudflare

1. Go to https://dash.cloudflare.com
2. Click **"Add a Site"**
3. Enter: `designbuildautomate.io`
4. Select **Free** plan
5. Update your domain's nameservers to Cloudflare's

**Cloudflare Nameservers** (example):
```
ns1.cloudflare.com
ns2.cloudflare.com
```

Wait for DNS propagation (can take up to 24 hours, usually minutes).

---

### Step 2: Create Cloudflare Tunnel

1. In Cloudflare Dashboard, go to **Zero Trust** (left sidebar)
2. Navigate to **Access** → **Tunnels**
3. Click **"Create a tunnel"**
4. **Name**: `roosterbots-site`
5. Click **"Save tunnel"**

---

### Step 3: Get Tunnel Token

After creating the tunnel, you'll see:

```
Install and run a connector:
docker run cloudflare/cloudflared:latest tunnel --no-autoupdate run --token <YOUR_TOKEN>
```

**Copy the token** (long string after `--token`)

Example token format:
```
eyJhIjoiNzk4M2RkOGYxZjE5NGE3YmI4ZTg5ZjA5YzQ5ZTNhMzMiLCJ0IjoiYjU3YjNhNmUtOTk5Yy00ZGI5LWE3ZjItMzk4ZjE5YzQ5ZTNhIiwicyI6Ik1qQXlOREF4TURBd01EQXdNREF3TURBd01EQXdNREF3TURBd01EQT0ifQ==
```

---

### Step 4: Configure Public Hostname

Still in the tunnel configuration:

1. Click **"Public Hostname"** tab
2. Click **"Add a public hostname"**

**Configuration:**
- **Subdomain**: `roosterbots`
- **Domain**: `designbuildautomate.io`
- **Type**: `HTTP`
- **URL**: `ftc-web:80`

Click **"Save hostname"**

This routes `roosterbots.designbuildautomate.io` → your Docker container `ftc-web` on port 80.

---

### Step 5: Add Tunnel Token to Portainer

In Portainer, when creating/updating your stack, add this environment variable:

```
CLOUDFLARE_TUNNEL_TOKEN=eyJhIjoiNzk4M2RkOGYxZjE5NGE3YmI4ZTg5ZjA5YzQ5ZTNhMzMi...
```

(Use your actual token from Step 3)

---

### Step 6: Deploy Stack in Portainer

1. **Stacks** → **Add Stack** (or update existing)
2. **Name**: `roosterbots-site`
3. **Repository**: `https://github.com/jtalborough/roosterbots-site`
4. **Branch**: `main`
5. **Compose path**: `docker-compose.yml`

**Add Environment Variables:**
```
CLOUDFLARE_TUNNEL_TOKEN=your_actual_token_here
STRIPE_SECRET_KEY=sk_live_your_key
STRIPE_WEBHOOK_SECRET=whsec_your_secret
BASE_URL=https://roosterbots.designbuildautomate.io
ALLOWED_ORIGINS=https://roosterbots.designbuildautomate.io
NODE_ENV=production
PORT=3000
```

6. Click **"Deploy the stack"**

---

## ✅ Verification

### Check Tunnel Status

In Cloudflare Dashboard:
1. Go to **Zero Trust** → **Access** → **Tunnels**
2. Your tunnel should show **"Healthy"** status
3. Green checkmark = working!

### Test Your Site

Visit: **https://roosterbots.designbuildautomate.io**

You should see your Hugo site! 🎉

### Test API

```bash
curl https://roosterbots.designbuildautomate.io/api/health
```

Expected response:
```json
{"status":"ok","timestamp":"2025-10-09T..."}
```

---

## 🔧 Advanced Configuration

### Multiple Subdomains

You can add multiple public hostnames to the same tunnel:

**Example:**
- `roosterbots.designbuildautomate.io` → `ftc-web:80` (main site)
- `api.roosterbots.designbuildautomate.io` → `ftc-api:3000` (direct API access)

### Custom SSL/TLS Settings

In Cloudflare Dashboard:
1. Go to **SSL/TLS** → **Overview**
2. Set encryption mode to **"Full"** or **"Full (strict)"**

### Enable WAF (Web Application Firewall)

1. Go to **Security** → **WAF**
2. Enable managed rules for extra protection

### Enable Caching

1. Go to **Caching** → **Configuration**
2. Set up cache rules for static assets

---

## 🐛 Troubleshooting

### Tunnel Shows "Down" or "Unhealthy"

**Check container logs:**
```bash
docker logs ftc-cloudflared
```

**Common issues:**
- Invalid token
- Container can't reach ftc-web
- Network configuration issue

**Fix:**
- Verify `CLOUDFLARE_TUNNEL_TOKEN` is correct
- Ensure all containers are on `ftc-network`
- Check `docker ps` to see if containers are running

### Site Not Accessible

**Check DNS:**
```bash
nslookup roosterbots.designbuildautomate.io
```

Should return Cloudflare IPs (not your server IP).

**Check tunnel configuration:**
- Verify public hostname is configured correctly
- Ensure subdomain and domain match
- Check service URL is `ftc-web:80`

### SSL/TLS Errors

**In Cloudflare Dashboard:**
1. **SSL/TLS** → **Overview**
2. Change to **"Flexible"** temporarily to test
3. Once working, switch to **"Full"**

### API Requests Failing

**Check CORS settings:**
- Verify `ALLOWED_ORIGINS` matches your domain
- Check API logs: `docker logs ftc-api`

**Test API directly:**
```bash
docker exec -it ftc-api wget -qO- http://localhost:3000/health
```

---

## 🔐 Security Best Practices

### 1. Use Access Policies (Optional)

Restrict access to certain pages:
1. **Zero Trust** → **Access** → **Applications**
2. Create application for admin pages
3. Require authentication

### 2. Enable Rate Limiting

Already configured in the API (`express-rate-limit`), but you can add Cloudflare rate limiting:
1. **Security** → **WAF** → **Rate limiting rules**

### 3. Enable Bot Protection

1. **Security** → **Bots**
2. Enable bot fight mode

### 4. Monitor Traffic

1. **Analytics** → **Traffic**
2. Review requests, threats blocked, etc.

---

## 📊 Architecture Diagram

```
Internet
   ↓
Cloudflare Edge (SSL/TLS, DDoS Protection)
   ↓
Cloudflare Tunnel (cloudflared container)
   ↓
Docker Network (ftc-network)
   ↓
   ├─→ ftc-web (Nginx) → Hugo Static Site
   │
   └─→ ftc-api (Node.js) → Stripe Integration
```

---

## 💰 Cost

**Cloudflare:**
- Free tier includes:
  - Unlimited bandwidth
  - DDoS protection
  - SSL certificates
  - Cloudflare Tunnel
  - Basic WAF rules

**No additional costs for this setup!**

---

## 🔄 Updating the Site

With Cloudflare Tunnel, updates work the same:

1. Push code to GitHub
2. GitHub Actions builds images
3. Portainer webhook redeploys
4. Cloudflare Tunnel automatically reconnects
5. Zero downtime!

---

## 📝 Quick Reference

| What | Where | Value |
|------|-------|-------|
| **Tunnel Name** | Cloudflare Dashboard | `roosterbots-site` |
| **Public Hostname** | Cloudflare Dashboard | `roosterbots.designbuildautomate.io` |
| **Service URL** | Cloudflare Dashboard | `ftc-web:80` |
| **Tunnel Token** | Portainer Env Var | `CLOUDFLARE_TUNNEL_TOKEN` |
| **Site URL** | Browser | `https://roosterbots.designbuildautomate.io` |

---

## 🎯 Next Steps

After tunnel is working:

1. ✅ Test site is accessible
2. ✅ Test API health endpoint
3. ✅ Configure Stripe webhook URL
4. ✅ Test checkout flow
5. ✅ Add content to site
6. ✅ Enable Cloudflare analytics
7. ✅ Set up monitoring/alerts

---

## 🆘 Need Help?

**Cloudflare Tunnel not connecting?**
- Check token is correct
- Verify container is running: `docker ps | grep cloudflared`
- Check logs: `docker logs ftc-cloudflared`

**Site loads but API fails?**
- Check nginx.conf proxy settings
- Verify ftc-api container is healthy
- Test API internally: `docker exec -it ftc-web curl http://api:3000/health`

**SSL errors?**
- Set Cloudflare SSL mode to "Flexible" or "Full"
- Check certificate status in Cloudflare Dashboard

---

**Your site is now accessible via Cloudflare Tunnel with zero port exposure!** 🎉🔒
