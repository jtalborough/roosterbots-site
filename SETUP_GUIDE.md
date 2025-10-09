# Phase 1 Complete: Setup Guide

## ✅ What We've Built

Your FTC team website is now scaffolded with:

### 1. **Hugo Static Site**
- ✅ Hugo initialized with PaperMod theme
- ✅ Navigation menu configured (Home, Blog, Store, Donate, About)
- ✅ Content structure created
- ✅ Custom layouts for Store and Donation pages

### 2. **Node.js/Express API Backend**
- ✅ Stripe Checkout integration
- ✅ Store purchase endpoint
- ✅ Donation endpoint
- ✅ Webhook handler for payment events
- ✅ Security middleware (helmet, CORS, rate limiting)

### 3. **Docker Configuration**
- ✅ Multi-stage Dockerfile for Hugo + Nginx
- ✅ API Dockerfile with Node.js
- ✅ Docker Compose orchestration
- ✅ Nginx reverse proxy configuration

### 4. **CI/CD Pipeline**
- ✅ GitHub Actions workflow
- ✅ Automated Docker image builds
- ✅ Portainer webhook deployment

### 5. **Initial Content**
- ✅ About page
- ✅ Blog section with sample post
- ✅ Store page with product catalog
- ✅ Donation page with preset/custom amounts
- ✅ Success/thank you page

---

## 🚀 Next Steps

### Step 1: Test Locally

```bash
# Start Hugo development server
hugo server -D
```

Visit http://localhost:1313 to see your site!

### Step 2: Set Up Stripe

1. Create a Stripe account at https://stripe.com
2. Get your API keys from Dashboard → Developers → API Keys
3. Copy `api/.env.example` to `api/.env`
4. Add your Stripe keys to `api/.env`

### Step 3: Test API Locally

```bash
cd api
npm install
npm run dev
```

The API will run on http://localhost:3000

### Step 4: Create GitHub Repository

```bash
# Add your GitHub remote
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git

# Push to GitHub
git push -u origin main
```

### Step 5: Configure GitHub Secrets

In your GitHub repository:
1. Go to Settings → Secrets and variables → Actions
2. Add: `PORTAINER_WEBHOOK_URL` (you'll get this from Portainer)

### Step 6: Set Up Portainer

1. Install Portainer on your server
2. Create a new Stack named "ftc-website"
3. Paste the contents of `docker-compose.yml`
4. Add environment variables in Portainer
5. Enable webhook and copy the URL
6. Add webhook URL to GitHub Secrets

---

## 📋 Configuration Checklist

### Before Going Live:

- [ ] Update `hugo.toml` with your actual domain
- [ ] Replace team name and description
- [ ] Add your Stripe API keys to `api/.env`
- [ ] Set up Stripe webhook endpoint
- [ ] Add team photos to `static/images/`
- [ ] Update product catalog in `content/store/products.json`
- [ ] Add actual product images
- [ ] Write your About page content
- [ ] Create initial blog posts
- [ ] Test checkout flow end-to-end
- [ ] Test donation flow
- [ ] Configure DNS for your domain
- [ ] Set up SSL certificate (via Portainer/reverse proxy)

---

## 🎯 Phase 2 Preview: Content Creation

Next, we'll cover:
- Writing effective blog posts with images
- Creating product listings
- Adding team photos and bios
- Customizing the theme colors
- SEO optimization

---

## 🆘 Troubleshooting

### Hugo server won't start
```bash
# Check Hugo version
hugo version

# Should be v0.112.0 or higher
```

### API errors
```bash
# Check Node version
node --version

# Should be v18.0.0 or higher

# Install dependencies
cd api && npm install
```

### Docker build fails
```bash
# Check Docker is running
docker --version

# Rebuild from scratch
docker-compose build --no-cache
```

### Submodule (theme) missing
```bash
git submodule update --init --recursive
```

---

## 📞 Need Help?

Common issues and solutions:

**Q: How do I add a new page?**
```bash
hugo new content/page-name.md
```

**Q: How do I add a blog post?**
```bash
hugo new content/blog/post-title.md
```

**Q: Where do I put images?**
Place them in `static/images/` and reference as `/images/filename.jpg`

**Q: How do I change the theme colors?**
We'll cover this in Phase 6: Customization

**Q: Can I test Stripe without real money?**
Yes! Use test mode keys (start with `sk_test_`) and test card `4242 4242 4242 4242`

---

## 📚 File Structure Reference

```
RoosterBotsSite/
├── content/              # Your content (Markdown files)
│   ├── blog/            # Blog posts
│   ├── store/           # Store pages
│   ├── about.md         # About page
│   └── donate.md        # Donation page
│
├── static/              # Static files (images, CSS, JS)
│   └── images/          # Your images
│
├── layouts/             # Custom page templates
│   └── _default/
│       ├── store.html   # Store page layout
│       └── donate.html  # Donation page layout
│
├── api/                 # Backend API
│   ├── server.js        # Main API server
│   ├── package.json     # Dependencies
│   └── .env             # Environment variables (create this)
│
├── hugo.toml            # Hugo configuration
├── docker-compose.yml   # Docker orchestration
└── README.md            # Main documentation
```

---

## ✨ What's Working Right Now

1. **Navigation**: All menu items are configured
2. **Blog**: Ready to add posts
3. **Store**: Product catalog system in place
4. **Donations**: Fixed and custom amount options
5. **Stripe**: Full payment integration (needs your keys)
6. **Docker**: Ready to deploy
7. **CI/CD**: Automated deployment pipeline

---

## 🎉 You're Ready!

Your FTC team website foundation is complete. You can now:

1. **Develop locally** with `hugo server -D`
2. **Add content** by creating Markdown files
3. **Test payments** with Stripe test mode
4. **Deploy** via Docker and Portainer

When you're ready, we'll move to **Phase 2: Content Creation** where we'll add your team's story, photos, and products!
