# 🚀 Deployment Guide - Apartment Maintenance Manager

This guide provides step-by-step instructions for deploying your apartment maintenance app to various hosting platforms.

## 📋 Pre-Deployment Checklist

- [x] Application is fully tested locally
- [x] All features are working correctly
- [x] PWA manifest is configured
- [x] Icons are in place
- [x] Build process completes successfully

## 🌐 Deployment Options

### Option 1: Vercel (Easiest - Recommended) ⭐

**Why Vercel?**
- Zero configuration needed
- Automatic HTTPS
- Global CDN
- Free tier available
- Perfect for Next.js apps

**Steps:**

1. **Create a Vercel account** at [vercel.com](https://vercel.com)

2. **Install Vercel CLI** (optional):
   ```bash
   npm install -g vercel
   ```

3. **Deploy from CLI**:
   ```bash
   cd apartment-app
   vercel
   ```

4. **Or deploy from GitHub**:
   - Push your code to GitHub
   - Go to [vercel.com/new](https://vercel.com/new)
   - Import your repository
   - Click "Deploy"

5. **Your app will be live** at `https://your-app-name.vercel.app`

**Custom Domain:**
- Go to Project Settings → Domains
- Add your custom domain
- Update DNS records as instructed

---

### Option 2: Netlify

**Steps:**

1. **Create account** at [netlify.com](https://netlify.com)

2. **Deploy via drag & drop**:
   ```bash
   npm run build
   ```
   - Drag the `.next` folder to Netlify

3. **Or deploy from GitHub**:
   - Connect your repository
   - Build settings:
     - Build command: `npm run build`
     - Publish directory: `.next`

4. **Configure `netlify.toml`** (create in root):
   ```toml
   [build]
     command = "npm run build"
     publish = ".next"

   [[redirects]]
     from = "/*"
     to = "/index.html"
     status = 200
   ```

---

### Option 3: AWS (Amazon Web Services)

**Using AWS Amplify:**

1. **Create AWS account** at [aws.amazon.com](https://aws.amazon.com)

2. **Go to AWS Amplify Console**

3. **Connect repository**:
   - Choose GitHub/GitLab/Bitbucket
   - Select your repository
   - Configure build settings:
     ```yaml
     version: 1
     frontend:
       phases:
         preBuild:
           commands:
             - npm install
         build:
           commands:
             - npm run build
       artifacts:
         baseDirectory: .next
         files:
           - '**/*'
       cache:
         paths:
           - node_modules/**/*
     ```

4. **Deploy** - AWS will automatically build and deploy

---

### Option 4: Google Cloud Platform

**Using Google Cloud Run:**

1. **Create `Dockerfile`**:
   ```dockerfile
   FROM node:18-alpine
   WORKDIR /app
   COPY package*.json ./
   RUN npm install
   COPY . .
   RUN npm run build
   EXPOSE 3000
   CMD ["npm", "start"]
   ```

2. **Build and deploy**:
   ```bash
   gcloud builds submit --tag gcr.io/YOUR_PROJECT_ID/apartment-app
   gcloud run deploy apartment-app --image gcr.io/YOUR_PROJECT_ID/apartment-app --platform managed
   ```

---

### Option 5: DigitalOcean App Platform

1. **Create DigitalOcean account**

2. **Create new app**:
   - Connect GitHub repository
   - Select branch
   - Configure:
     - Build command: `npm run build`
     - Run command: `npm start`
     - HTTP Port: 3000

3. **Deploy** - DigitalOcean handles the rest

---

### Option 6: Traditional VPS/Server

**For Ubuntu/Debian servers:**

1. **Install Node.js**:
   ```bash
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   sudo apt-get install -y nodejs
   ```

2. **Install PM2** (process manager):
   ```bash
   sudo npm install -g pm2
   ```

3. **Upload your app**:
   ```bash
   scp -r apartment-app user@your-server:/var/www/
   ```

4. **Install dependencies and build**:
   ```bash
   cd /var/www/apartment-app
   npm install
   npm run build
   ```

5. **Start with PM2**:
   ```bash
   pm2 start npm --name "apartment-app" -- start
   pm2 save
   pm2 startup
   ```

6. **Configure Nginx** (optional, for reverse proxy):
   ```nginx
   server {
       listen 80;
       server_name your-domain.com;

       location / {
           proxy_pass http://localhost:3000;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection 'upgrade';
           proxy_set_header Host $host;
           proxy_cache_bypass $http_upgrade;
       }
   }
   ```

7. **Enable HTTPS with Let's Encrypt**:
   ```bash
   sudo apt install certbot python3-certbot-nginx
   sudo certbot --nginx -d your-domain.com
   ```

---

## 🔧 Environment Variables

If you add backend integration, create `.env.local`:

```env
# Database
DATABASE_URL=your_database_connection_string

# Authentication
JWT_SECRET=your_secret_key
NEXTAUTH_URL=https://your-domain.com
NEXTAUTH_SECRET=your_nextauth_secret

# Email (optional)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASSWORD=your-password

# Payment Gateway (when ready)
STRIPE_PUBLIC_KEY=pk_live_...
STRIPE_SECRET_KEY=sk_live_...
```

**Important:** Never commit `.env.local` to Git!

---

## 📱 Testing PWA Installation

After deployment:

### On Android:
1. Open your deployed URL in Chrome
2. Look for "Install App" prompt
3. Or: Menu → "Add to Home Screen"

### On iOS:
1. Open in Safari
2. Tap Share button
3. "Add to Home Screen"

### Desktop:
1. Open in Chrome/Edge
2. Look for install icon in address bar
3. Click to install

---

## 🔍 Post-Deployment Verification

1. **Test all features**:
   - User registration/login
   - Owner management
   - Task creation
   - Expense tracking
   - Reports generation

2. **Check PWA functionality**:
   - Install on mobile device
   - Test offline capability
   - Verify icons appear correctly

3. **Performance testing**:
   - Use [PageSpeed Insights](https://pagespeed.web.dev/)
   - Aim for 90+ score

4. **Security checks**:
   - HTTPS is enabled
   - No console errors
   - Data persists correctly

---

## 🎯 Recommended: Vercel Deployment (Fastest)

For the quickest deployment:

```bash
# 1. Install Vercel CLI
npm i -g vercel

# 2. Navigate to your app
cd apartment-app

# 3. Deploy!
vercel

# Follow the prompts:
# - Set up and deploy? Yes
# - Which scope? Your account
# - Link to existing project? No
# - Project name? apartment-app
# - Directory? ./
# - Override settings? No

# 4. Your app is live! 🎉
```

You'll get a URL like: `https://apartment-app-xyz.vercel.app`

---

## 🔄 Continuous Deployment

Once connected to GitHub:

1. **Push to main branch**:
   ```bash
   git add .
   git commit -m "Update feature"
   git push origin main
   ```

2. **Automatic deployment** happens on:
   - Vercel
   - Netlify
   - AWS Amplify
   - DigitalOcean App Platform

---

## 🆘 Troubleshooting

### Build fails:
```bash
# Clear cache and rebuild
rm -rf .next node_modules
npm install
npm run build
```

### Port already in use:
```bash
# Change port in package.json
"dev": "next dev -p 3001"
```

### PWA not installing:
- Ensure HTTPS is enabled
- Check manifest.json is accessible
- Verify icons exist in /public

---

## 📊 Monitoring (Optional)

Add analytics:

1. **Google Analytics**:
   - Add tracking code to `app/layout.js`

2. **Vercel Analytics**:
   - Enable in Vercel dashboard

3. **Error tracking**:
   - Integrate Sentry or LogRocket

---

## ✅ You're Ready!

Your apartment maintenance app is now:
- ✅ Built and tested
- ✅ Ready to deploy
- ✅ Optimized for mobile
- ✅ PWA-enabled
- ✅ Production-ready

Choose your deployment platform and launch! 🚀

**Recommended for beginners:** Start with Vercel - it's free, fast, and requires zero configuration.
