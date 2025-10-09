# FIRST Tech Challenge Team Website

A modern, fast website built with Hugo for our FTC team, featuring a blog, merchandise store, and donation system.

## 🏗️ Architecture

- **Frontend**: Hugo static site generator with PaperMod theme
- **Backend API**: Node.js/Express for Stripe payment processing
- **Deployment**: Docker containers managed via Portainer
- **CI/CD**: GitHub Actions for automated builds and deployments

## 📋 Prerequisites

- Hugo (extended version)
- Node.js 18+
- Docker & Docker Compose
- Git
- Stripe account

## 🚀 Local Development

### 1. Clone the Repository

```bash
git clone <your-repo-url>
cd RoosterBotsSite
git submodule update --init --recursive
```

### 2. Set Up Environment Variables

Create `api/.env` file:

```bash
cp api/.env.example api/.env
```

Edit `api/.env` and add your Stripe keys:

```env
STRIPE_SECRET_KEY=sk_test_your_key_here
STRIPE_WEBHOOK_SECRET=whsec_your_webhook_secret
BASE_URL=http://localhost
ALLOWED_ORIGINS=http://localhost
NODE_ENV=development
```

### 3. Install API Dependencies

```bash
cd api
npm install
cd ..
```

### 4. Run Locally

**Option A: Using Docker Compose (Recommended)**

```bash
docker-compose up --build
```

Visit: http://localhost

**Option B: Run Services Separately**

Terminal 1 - Hugo:
```bash
hugo server -D
```

Terminal 2 - API:
```bash
cd api
npm run dev
```

Visit: http://localhost:1313

## 📝 Content Management

### Adding Blog Posts

```bash
hugo new content/blog/my-post.md
```

Edit the created file with your content.

### Adding Store Products

Edit `content/store/products.json`:

```json
{
  "id": "unique-id",
  "name": "Product Name",
  "description": "Product description",
  "price": 25.00,
  "image": "/images/products/product.jpg",
  "sizes": ["S", "M", "L", "XL"]
}
```

### Adding Images

Place images in `static/images/` directory. Reference them as `/images/filename.jpg` in your content.

## 🐳 Docker Deployment

### Build Images

```bash
# Build web image
docker build -f Dockerfile.hugo -t ftc-web .

# Build API image
docker build -f api/Dockerfile -t ftc-api ./api
```

### Run with Docker Compose

```bash
docker-compose up -d
```

## 🔄 GitHub Actions Deployment

### Required GitHub Secrets

Set these in your GitHub repository settings (Settings → Secrets and variables → Actions):

1. **PORTAINER_WEBHOOK_URL**: Your Portainer webhook URL for stack updates

### Deployment Process

1. Push to `main` branch
2. GitHub Actions builds Docker images
3. Images are pushed to GitHub Container Registry
4. Portainer webhook triggers deployment

## 🔧 Portainer Setup

### 1. Create Stack in Portainer

1. Go to Portainer → Stacks → Add Stack
2. Name: `ftc-website`
3. Use the `docker-compose.yml` from this repo
4. Add environment variables for API

### 2. Set Up Webhook

1. In your stack, enable webhook
2. Copy the webhook URL
3. Add it to GitHub Secrets as `PORTAINER_WEBHOOK_URL`

### 3. Configure Environment Variables

In Portainer stack settings, add:

```
STRIPE_SECRET_KEY=sk_live_your_key
STRIPE_WEBHOOK_SECRET=whsec_your_secret
BASE_URL=https://your-domain.com
ALLOWED_ORIGINS=https://your-domain.com
NODE_ENV=production
```

## 💳 Stripe Configuration

### 1. Get API Keys

- Go to Stripe Dashboard → Developers → API Keys
- Copy your Secret Key to `api/.env`

### 2. Set Up Webhook

1. Go to Stripe Dashboard → Developers → Webhooks
2. Add endpoint: `https://your-domain.com/api/webhook`
3. Select events: `checkout.session.completed`, `payment_intent.payment_failed`
4. Copy webhook signing secret to `api/.env`

### 3. Test Payments

Use Stripe test cards:
- Success: `4242 4242 4242 4242`
- Decline: `4000 0000 0000 0002`

## 📁 Project Structure

```
RoosterBotsSite/
├── .github/
│   └── workflows/
│       └── deploy.yml          # CI/CD pipeline
├── api/                        # Node.js backend
│   ├── server.js              # Express server
│   ├── package.json
│   ├── Dockerfile
│   └── .env.example
├── content/                    # Hugo content
│   ├── blog/                  # Blog posts
│   ├── store/                 # Store pages
│   ├── about.md
│   └── donate.md
├── layouts/                    # Custom Hugo layouts
│   └── _default/
│       ├── store.html         # Store page template
│       └── donate.html        # Donation page template
├── static/                     # Static assets
│   └── images/
├── themes/
│   └── PaperMod/              # Hugo theme (submodule)
├── docker-compose.yml          # Docker orchestration
├── Dockerfile.hugo             # Hugo/Nginx container
├── nginx.conf                  # Nginx configuration
└── hugo.toml                   # Hugo configuration
```

## 🎨 Customization

### Update Team Information

Edit `hugo.toml`:

```toml
baseURL = 'https://your-domain.com/'
title = 'Your Team Name'
```

### Customize Theme Colors

Create `assets/css/extended/custom.css` (will be covered in Phase 6)

### Update Navigation

Edit the `[[menu.main]]` sections in `hugo.toml`

## 🧪 Testing

### Test Hugo Build

```bash
hugo --minify
```

### Test API Endpoints

```bash
# Health check
curl http://localhost:3000/health

# Test checkout (requires API running)
curl -X POST http://localhost:3000/create-checkout-session \
  -H "Content-Type: application/json" \
  -d '{"items":[{"name":"Test","price":10,"quantity":1}]}'
```

## 📚 Resources

- [Hugo Documentation](https://gohugo.io/documentation/)
- [PaperMod Theme](https://github.com/adityatelange/hugo-PaperMod)
- [Stripe API Docs](https://stripe.com/docs/api)
- [Docker Documentation](https://docs.docker.com/)
- [Portainer Documentation](https://docs.portainer.io/)

## 🤝 Contributing

Team members can contribute by:
1. Creating a new branch
2. Making changes
3. Submitting a pull request
4. Getting review from coaches

## 📧 Support

For questions or issues, contact: team@example.com

## 📄 License

MIT License - Feel free to use this for your own FTC team!
