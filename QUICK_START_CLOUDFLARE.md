# 🚀 Quick Start: Deploy with Cloudflare Tunnel

## ✅ 5-Minute Setup Checklist

### 1️⃣ Create Cloudflare Tunnel (2 min)

1. Go to https://dash.cloudflare.com
2. **Zero Trust** → **Access** → **Tunnels**
3. Click **"Create a tunnel"**
4. Name: `roosterbots-site`
5. **Copy the token** (long string after `--token`)

### 2️⃣ Configure Public Hostname (1 min)

In the tunnel settings:
1. **Public Hostname** tab → **"Add a public hostname"**
2. **Subdomain**: `roosterbots`
3. **Domain**: `designbuildautomate.io`
4. **Type**: `HTTP`
5. **URL**: `ftc-web:80`
6. Click **"Save"**

### 3️⃣ Deploy in Portainer (2 min)

1. **Stacks** → **Add Stack**
2. **Name**: `roosterbots-site`
3. **Repository**: `https://github.com/jtalborough/roosterbots-site`
4. **Branch**: `main`

**Add these environment variables:**

```env
CLOUDFLARE_TUNNEL_TOKEN=<paste your token here>
STRIPE_SECRET_KEY=sk_test_your_key
STRIPE_WEBHOOK_SECRET=whsec_your_secret
BASE_URL=https://roosterbots.designbuildautomate.io
ALLOWED_ORIGINS=https://roosterbots.designbuildautomate.io
NODE_ENV=production
PORT=3000
```

4. Click **"Deploy the stack"**

### 4️⃣ Verify (30 sec)

Visit: **https://roosterbots.designbuildautomate.io**

You should see your site! 🎉

---

## 🎯 What Changed?

✅ **No port conflicts** - Uses `expose` instead of `ports`  
✅ **No public ports** - Everything goes through Cloudflare Tunnel  
✅ **Free SSL** - Cloudflare handles certificates  
✅ **DDoS protection** - Built-in with Cloudflare  

---

## 📋 Environment Variables You Need

| Variable | Where to Get It | Required |
|----------|----------------|----------|
| `CLOUDFLARE_TUNNEL_TOKEN` | Cloudflare Dashboard (Step 1) | ✅ Yes |
| `STRIPE_SECRET_KEY` | Stripe Dashboard → API Keys | ✅ Yes |
| `STRIPE_WEBHOOK_SECRET` | Stripe Dashboard → Webhooks | ✅ Yes |
| `BASE_URL` | Your domain | ✅ Yes |
| `ALLOWED_ORIGINS` | Your domain | ✅ Yes |
| `NODE_ENV` | `production` | Optional |
| `PORT` | `3000` | Optional |

---

## 🐛 Troubleshooting

**Tunnel shows "Down"?**
- Check token is correct
- Wait 30 seconds for containers to start
- Check logs: `docker logs ftc-cloudflared`

**Site not loading?**
- Verify public hostname is configured
- Check DNS: `nslookup roosterbots.designbuildautomate.io`
- Wait a few minutes for DNS propagation

**API not working?**
- Check all environment variables are set
- Test: `curl https://roosterbots.designbuildautomate.io/api/health`
- Check logs: `docker logs ftc-api`

---

## 📚 Full Documentation

- **Detailed Setup**: See `CLOUDFLARE_TUNNEL_SETUP.md`
- **Portainer Guide**: See `PORTAINER_SETUP.md`
- **General Setup**: See `SETUP_GUIDE.md`

---

**That's it! Your site is now live with zero port exposure.** 🔒✨
