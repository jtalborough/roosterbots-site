# 🎯 Next Steps: Getting Your Site Live

## ✅ Completed
- [x] Hugo site scaffolded
- [x] Docker configuration created
- [x] API backend with Stripe integration
- [x] GitHub repository created: https://github.com/jtalborough/roosterbots-site
- [x] Code pushed to GitHub

---

## 📝 Immediate Actions

### 1. Set Up Stripe Account (15 minutes)

1. **Create Stripe Account**
   - Go to https://stripe.com
   - Sign up for a free account
   - Verify your email

2. **Get API Keys**
   - Dashboard → Developers → API Keys
   - Copy your **Publishable key** (starts with `pk_test_`)
   - Copy your **Secret key** (starts with `sk_test_`)

3. **Update Local Environment**
   - You already have `api/.env` open
   - Replace `sk_test_your_stripe_secret_key` with your actual secret key
   - Save the file

4. **Set Up Webhook** (do this after testing locally)
   - Dashboard → Developers → Webhooks
   - Add endpoint: `https://your-domain.com/api/webhook`
   - Select events: `checkout.session.completed`, `payment_intent.payment_failed`
   - Copy webhook signing secret (starts with `whsec_`)
   - Add to `api/.env`

---

### 2. Test Locally (10 minutes)

**Terminal 1 - Start Hugo:**
```bash
cd /Users/jta/git-projects/RoosterBotsSite
hugo server -D
```
Visit: http://localhost:1313

**Terminal 2 - Start API:**
```bash
cd /Users/jta/git-projects/RoosterBotsSite/api
npm install
npm run dev
```

**Test the site:**
- ✅ Navigate through all pages
- ✅ Check blog posts display
- ✅ View store page (products should load)
- ✅ View donation page
- ⚠️ Don't test checkout yet (need Stripe keys first)

---

### 3. Customize Content (30 minutes)

**Update Team Information:**

Edit `hugo.toml`:
```toml
title = 'RoosterBots FTC Team'  # Your team name
baseURL = 'https://roosterbots.com/'  # Your domain
```

**Edit About Page:**
```bash
# Edit content/about.md
# Add team members, coaches, robot info
```

**Add Team Photos:**
```bash
# Copy photos to static/images/
# Reference as /images/team-photo.jpg in markdown
```

**Update Products:**
```bash
# Edit content/store/products.json
# Add your actual merchandise items
```

**Write First Blog Post:**
```bash
hugo new content/blog/season-kickoff.md
# Edit the created file with your content
```

---

### 4. Set Up Portainer (45 minutes)

**Prerequisites:**
- A server (VPS, dedicated server, or home server)
- Docker installed on the server
- Domain name pointing to your server

**Install Portainer:**
```bash
# On your server
docker volume create portainer_data
docker run -d -p 9000:9000 -p 8000:8000 \
  --name portainer --restart always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
```

**Access Portainer:**
- Visit: http://your-server-ip:9000
- Create admin account
- Connect to local Docker environment

**Create Stack:**
1. Stacks → Add Stack
2. Name: `roosterbots-site`
3. Web editor → Paste contents of `docker-compose.yml`
4. Add environment variables:
   ```
   STRIPE_SECRET_KEY=sk_live_your_key
   STRIPE_WEBHOOK_SECRET=whsec_your_secret
   BASE_URL=https://roosterbots.com
   ALLOWED_ORIGINS=https://roosterbots.com
   NODE_ENV=production
   ```
5. Deploy the stack

**Enable Webhook:**
1. Click on your stack
2. Enable webhook
3. Copy webhook URL
4. Save for GitHub Secrets

---

### 5. Configure GitHub Actions (5 minutes)

**Add Secrets to GitHub:**
1. Go to: https://github.com/jtalborough/roosterbots-site/settings/secrets/actions
2. Click "New repository secret"
3. Add:
   - Name: `PORTAINER_WEBHOOK_URL`
   - Value: (paste webhook URL from Portainer)

**Test Deployment:**
```bash
# Make a small change
echo "# Test" >> README.md
git add README.md
git commit -m "Test deployment"
git push
```

Check GitHub Actions tab to see deployment progress.

---

### 6. Configure Domain & SSL (30 minutes)

**DNS Configuration:**
- Add A record: `@` → Your server IP
- Add A record: `www` → Your server IP

**SSL Certificate (Recommended: Use Nginx Proxy Manager or Traefik):**

Option A - Nginx Proxy Manager (Easiest):
```bash
# Install on your server
docker run -d -p 80:80 -p 443:443 -p 81:81 \
  --name nginx-proxy-manager \
  --restart unless-stopped \
  -v nginx-data:/data \
  -v nginx-letsencrypt:/etc/letsencrypt \
  jc21/nginx-proxy-manager:latest
```

Then:
1. Visit http://your-server-ip:81
2. Add proxy host
3. Forward to `ftc-web:80`
4. Enable SSL with Let's Encrypt

Option B - Update docker-compose.yml to include Traefik (we can do this in Phase 4)

---

## 🧪 Testing Checklist

Before going live:

- [ ] All pages load correctly
- [ ] Navigation works
- [ ] Blog posts display with images
- [ ] Store page shows products
- [ ] Donation page loads
- [ ] Test Stripe checkout (test mode)
- [ ] Test donation flow (test mode)
- [ ] Verify webhook receives events
- [ ] Check mobile responsiveness
- [ ] Test on different browsers
- [ ] Verify SSL certificate
- [ ] Check page load speed

---

## 🎨 Optional Customizations (Phase 2)

- Custom theme colors
- Team logo in header
- Photo gallery
- Sponsor logos
- Social media links
- Contact form
- Calendar/events section

---

## 📊 Current Status

**Repository:** https://github.com/jtalborough/roosterbots-site  
**Local Dev:** Ready to test  
**API:** Configured, needs Stripe keys  
**Docker:** Ready to deploy  
**CI/CD:** Configured, needs Portainer webhook  

---

## 🆘 Need Help?

**Common Issues:**

1. **"Hugo command not found"**
   ```bash
   brew install hugo
   ```

2. **"npm: command not found"**
   ```bash
   brew install node
   ```

3. **Theme not loading**
   ```bash
   git submodule update --init --recursive
   ```

4. **API won't start**
   ```bash
   cd api
   npm install
   ```

5. **Docker build fails**
   - Ensure Docker Desktop is running
   - Check Docker version: `docker --version`

---

## 📞 Questions?

Ask me about:
- Stripe setup and testing
- Content creation best practices
- Docker deployment
- Portainer configuration
- Custom theme modifications
- SEO optimization
- Performance tuning

---

**Ready to proceed?** Let me know when you:
1. Have Stripe keys and want to test payments
2. Need help with content creation
3. Are ready to deploy to Portainer
4. Want to customize the design

We can tackle these in any order that works best for your team!
