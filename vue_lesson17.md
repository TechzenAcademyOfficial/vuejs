# Vue.js – Buổi 17: Deploy & Tối ưu

## 1) Build cho Production

### 1.1 Production Build

```bash
npm run build
```

**Output:**
> Tạo thư mục `dist/` chứa các file đã được optimize và minify.

### 1.2 Build Configuration

**vite.config.js**

```javascript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  build: {
    outDir: 'dist',
    assetsDir: 'assets',
    sourcemap: false, // Disable source maps cho production
    minify: 'terser',
    terserOptions: {
      compress: {
        drop_console: true, // Remove console.log
        drop_debugger: true
      }
    },
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['vue', 'vue-router'],
          utils: ['axios', 'lodash']
        }
      }
    }
  }
})
```

---

## 2) Environment Variables

### 2.1 Environment Files

**.env.development**

```
VITE_API_URL=http://localhost:3000/api
VITE_APP_TITLE=My App (Dev)
```

**.env.production**

```
VITE_API_URL=https://api.example.com
VITE_APP_TITLE=My App
```

### 2.2 Sử dụng trong Code

```javascript
// .env variables phải có prefix VITE_
const apiUrl = import.meta.env.VITE_API_URL
const appTitle = import.meta.env.VITE_APP_TITLE
```

---

## 3) Deploy lên Static Hosting

### 3.1 Netlify

**netlify.toml**

```toml
[build]
  command = "npm run build"
  publish = "dist"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

**Deploy:**
1. Connect repository với Netlify
2. Set build command: `npm run build`
3. Set publish directory: `dist`
4. Deploy!

### 3.2 Vercel

**vercel.json**

```json
{
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "rewrites": [
    {
      "source": "/(.*)",
      "destination": "/index.html"
    }
  ]
}
```

**Deploy:**
```bash
npm i -g vercel
vercel
```

### 3.3 GitHub Pages

**deploy.sh**

```bash
#!/usr/bin/env sh

# Build
npm run build

# Deploy
cd dist
git init
git add -A
git commit -m 'deploy'
git push -f git@github.com:username/repo.git master:gh-pages
cd -
```

**package.json**

```json
{
  "scripts": {
    "deploy": "bash deploy.sh"
  }
}
```

---

## 4) Deploy lên Server

### 4.1 Nginx Configuration

**nginx.conf**

```nginx
server {
    listen 80;
    server_name example.com;
    root /var/www/my-app/dist;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # Cache static assets
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|woff|woff2|ttf|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Gzip compression
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
}
```

### 4.2 PM2 cho Node.js Server

```bash
npm install -g pm2
pm2 start server.js --name my-app
pm2 save
pm2 startup
```

---

## 5) Docker Deployment

### 5.1 Dockerfile

**Dockerfile**

```dockerfile
# Build stage
FROM node:18-alpine as build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production stage
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### 5.2 Docker Compose

**docker-compose.yml**

```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "80:80"
    restart: unless-stopped
```

**Build và Run:**

```bash
docker-compose up -d
```

---

## 6) CI/CD Pipeline

### 6.1 GitHub Actions

**.github/workflows/deploy.yml**

```yaml
name: Deploy

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build
        run: npm run build
      
      - name: Deploy to Netlify
        uses: netlify/actions/cli@master
        with:
          args: deploy --prod --dir=dist
        env:
          NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
          NETLIFY_SITE_ID: ${{ secrets.NETLIFY_SITE_ID }}
```

### 6.2 GitLab CI

**.gitlab-ci.yml**

```yaml
stages:
  - build
  - deploy

build:
  stage: build
  image: node:18
  script:
    - npm ci
    - npm run build
  artifacts:
    paths:
      - dist/

deploy:
  stage: deploy
  script:
    - npm install -g netlify-cli
    - netlify deploy --prod --dir=dist
  only:
    - main
```

---

## 7) Performance Monitoring

### 7.1 Lighthouse CI

```bash
npm install -g @lhci/cli
```

**lighthouserc.js**

```javascript
module.exports = {
  ci: {
    collect: {
      url: ['https://example.com'],
      numberOfRuns: 3
    },
    assert: {
      assertions: {
        'categories:performance': ['error', { minScore: 0.9 }],
        'categories:accessibility': ['error', { minScore: 0.9 }]
      }
    }
  }
}
```

### 7.2 Real User Monitoring

**Sentry:**

```bash
npm install @sentry/vue
```

```javascript
import * as Sentry from '@sentry/vue'

Sentry.init({
  app,
  dsn: 'YOUR_DSN',
  integrations: [
    new Sentry.BrowserTracing(),
  ],
  tracesSampleRate: 1.0
})
```

---

## 8) Security Best Practices

### 8.1 Content Security Policy

**public/index.html**

```html
<meta http-equiv="Content-Security-Policy" content="
  default-src 'self';
  script-src 'self' 'unsafe-inline';
  style-src 'self' 'unsafe-inline';
  img-src 'self' data: https:;
">
```

### 8.2 Environment Variables Security

> Không commit `.env` files với sensitive data.

**.gitignore**

```
.env
.env.local
.env.*.local
```

---

## 9) Tóm tắt

### 9.1 Những gì đã học

> * Build cho production
> * Environment variables
> * Deploy lên static hosting (Netlify, Vercel, GitHub Pages)
> * Deploy lên server với Nginx
> * Docker deployment
> * CI/CD pipelines
> * Performance monitoring
> * Security best practices

### 9.2 Best Practices

> * Optimize build output
> * Sử dụng environment variables
> * Setup CI/CD cho automated deployment
> * Monitor performance và errors
> * Implement security headers
> * Test trên staging trước khi production

### 9.3 Bài tập thực hành

1. Build ứng dụng cho production
2. Setup environment variables
3. Deploy lên Netlify hoặc Vercel
4. Setup CI/CD pipeline
5. Configure Nginx cho production
6. Add performance monitoring

---

## 10) Tài liệu tham khảo

> * [Vite Build Guide](https://vitejs.dev/guide/build.html)
> * [Netlify Documentation](https://docs.netlify.com/)
> * [Vercel Documentation](https://vercel.com/docs)
> * [Docker Documentation](https://docs.docker.com/)
