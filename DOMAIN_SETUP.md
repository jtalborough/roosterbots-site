# Domain Configuration Guide

## Current Setup

- **Development/Testing**: `roosterbots.designbuildautomate.io`
- **Production (Future)**: `roosterbots.org`

## Switching to roosterbots.org

When you're ready to use the production domain:

### 1. Update Hugo Configuration

Edit `hugo.toml`:
```toml
baseURL = 'https://roosterbots.org'
```

### 2. Update Environment Variables in Portainer

Update these variables in your Portainer stack:
```
BASE_URL=https://roosterbots.org
ALLOWED_ORIGINS=https://roosterbots.org
```

### 3. Update Cloudflare Tunnel

In Cloudflare Dashboard:
1. Go to **Zero Trust** → **Access** → **Tunnels**
2. Click on your tunnel (`roosterbots-site`)
3. **Public Hostname** tab
4. Add new hostname:
   - **Domain**: `roosterbots.org`
   - **Type**: `HTTP`
   - **URL**: `ftc-web:80`

Or update the existing hostname to use `roosterbots.org`

### 4. Update Stripe Webhook

In Stripe Dashboard:
1. Go to **Webhooks**
2. Update endpoint URL to: `https://roosterbots.org/api/webhook`

### 5. DNS Configuration for roosterbots.org

If not already done:
1. Add `roosterbots.org` to Cloudflare
2. Update nameservers at your domain registrar
3. Cloudflare Tunnel will handle the rest

### 6. Commit and Deploy

```bash
git add hugo.toml
git commit -m "Switch to production domain roosterbots.org"
git push
```

GitHub Actions will automatically deploy the changes.

---

## Quick Domain Switch Checklist

- [ ] Update `hugo.toml` baseURL
- [ ] Update Portainer environment variables
- [ ] Update Cloudflare Tunnel hostname
- [ ] Update Stripe webhook URL
- [ ] Configure DNS (if needed)
- [ ] Test site at new domain
- [ ] Update any external links

---

## Rollback to Dev Domain

If you need to switch back:
```toml
baseURL = 'https://roosterbots.designbuildautomate.io'
```

And revert the environment variables in Portainer.
