# Vue.js – Buổi 14: SEO nâng cao và tối ưu hóa trang web

## 1) Server-Side Rendering (SSR)

### 1.1 SSR là gì?

> **SSR** render Vue components trên server trước khi gửi HTML về client.

**Lợi ích:**
> * Better SEO
> * Faster initial page load
> * Better social media sharing

### 1.2 Nuxt.js

> **Nuxt.js** là framework Vue.js với SSR built-in.

**Cài đặt:**

```bash
npx nuxi@latest init my-app
```

**Cấu trúc:**

```
my-app/
├── pages/
│   ├── index.vue
│   └── about.vue
├── components/
├── nuxt.config.js
└── app.vue
```

---

## 2) Static Site Generation (SSG)

### 2.1 SSG với Nuxt.js

**nuxt.config.js**

```javascript
export default {
  ssr: true, // SSR mode
  // hoặc
  ssr: false, // SPA mode
  // hoặc
  target: 'static' // Static generation
}
```

### 2.2 Pre-rendering

> Generate static HTML cho tất cả routes.

```javascript
// nuxt.config.js
export default {
  target: 'static',
  generate: {
    routes: [
      '/products/1',
      '/products/2',
      '/products/3'
    ]
  }
}
```

---

## 3) Meta Tags Management

### 3.1 Nuxt Head

**pages/index.vue**

```vue
<template>
  <div>
    <h1>Home Page</h1>
  </div>
</template>

<script setup>
useHead({
  title: 'Home - My App',
  meta: [
    {
      hid: 'description',
      name: 'description',
      content: 'Home page description'
    },
    {
      property: 'og:title',
      content: 'Home - My App'
    },
    {
      property: 'og:description',
      content: 'Home page description'
    }
  ]
})
</script>
```

### 3.2 Dynamic Meta Tags

```vue
<script setup>
const route = useRoute()
const { data: product } = await useFetch(`/api/products/${route.params.id}`)

useHead({
  title: product.value.name,
  meta: [
    {
      hid: 'description',
      name: 'description',
      content: product.value.description
    }
  ]
})
</script>
```

---

## 4) Sitemap và Robots.txt

### 4.1 Sitemap

**nuxt.config.js**

```javascript
export default {
  modules: [
    '@nuxtjs/sitemap'
  ],
  sitemap: {
    hostname: 'https://example.com',
    routes: async () => {
      const products = await fetch('/api/products')
      return products.map(p => `/products/${p.id}`)
    }
  }
}
```

### 4.2 Robots.txt

**public/robots.txt**

```
User-agent: *
Allow: /
Disallow: /admin/

Sitemap: https://example.com/sitemap.xml
```

---

## 5) Performance Optimization

### 5.1 Code Splitting

```vue
<script setup>
import { defineAsyncComponent } from 'vue'

const HeavyComponent = defineAsyncComponent(() => 
  import('@/components/HeavyComponent.vue')
)
</script>
```

### 5.2 Lazy Loading Routes

```javascript
// router/index.js
{
  path: '/about',
  component: () => import('@/views/About.vue')
}
```

### 5.3 Image Optimization

**Nuxt Image Module:**

```bash
npm install @nuxt/image
```

**nuxt.config.js**

```javascript
export default {
  modules: ['@nuxt/image'],
  image: {
    domains: ['example.com']
  }
}
```

**Sử dụng:**

```vue
<template>
  <NuxtImg 
    src="/image.jpg"
    width="800"
    height="600"
    loading="lazy"
  />
</template>
```

---

## 6) Prefetching và Preloading

### 6.1 Link Prefetching

```vue
<template>
  <NuxtLink to="/about" prefetch>About</NuxtLink>
</template>
```

### 6.2 Resource Hints

```vue
<script setup>
import { useHead } from '@unhead/vue'

useHead({
  link: [
    {
      rel: 'prefetch',
      href: '/api/data'
    },
    {
      rel: 'preload',
      href: '/fonts/font.woff2',
      as: 'font',
      type: 'font/woff2',
      crossorigin: 'anonymous'
    }
  ]
})
</script>
```

---

## 7) Analytics và Monitoring

### 7.1 Google Analytics

**nuxt.config.js**

```javascript
export default {
  modules: [
    '@nuxtjs/google-analytics'
  ],
  googleAnalytics: {
    id: 'UA-XXXXXXXXX-X'
  }
}
```

### 7.2 Performance Monitoring

```javascript
// plugins/performance.client.js
export default ({ app }) => {
  if (process.client) {
    window.addEventListener('load', () => {
      const perfData = window.performance.timing
      const pageLoadTime = perfData.loadEventEnd - perfData.navigationStart
      console.log('Page load time:', pageLoadTime)
    })
  }
}
```

---

## 8) Tóm tắt

### 8.1 Những gì đã học

> * SSR và SSG với Nuxt.js
> * Meta tags management
> * Sitemap và robots.txt
> * Performance optimization
> * Prefetching và preloading
> * Analytics và monitoring

### 8.2 Best Practices

> * Sử dụng Nuxt.js cho SSR/SSG
> * Quản lý meta tags động
> * Generate sitemap
> * Optimize images
> * Monitor performance

### 8.3 Bài tập thực hành

1. Setup Nuxt.js project
2. Implement SSR cho các pages
3. Add dynamic meta tags
4. Generate sitemap
5. Optimize images
6. Add analytics

---

## 9) Tài liệu tham khảo

> * [Nuxt.js Documentation](https://nuxt.com/)
> * [Vue.js SSR Guide](https://vuejs.org/guide/scaling-up/ssr.html)
> * [Web.dev Performance](https://web.dev/performance/)
