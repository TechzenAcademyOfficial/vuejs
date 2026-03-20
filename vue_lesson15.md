# Vue.js – Buổi 15: Quản lý hiệu suất và CDN

## 1) Performance Optimization

### 1.1 Bundle Size Optimization

> Giảm kích thước bundle để tăng tốc độ load.

**Analyze Bundle:**

```bash
npm run build -- --report
```

**Code Splitting:**

```javascript
// Lazy load components
const HeavyComponent = () => import('@/components/HeavyComponent.vue')
```

### 1.2 Tree Shaking

> Loại bỏ code không sử dụng.

```javascript
// ✅ Good - chỉ import những gì cần
import { ref, computed } from 'vue'

// ❌ Bad - import toàn bộ
import Vue from 'vue'
```

---

## 2) CDN và Asset Optimization

### 2.1 CDN là gì?

> **CDN (Content Delivery Network)** phân phối assets từ servers gần user nhất.

**Lợi ích:**
> * Faster load times
> * Reduced server load
> * Better global performance

### 2.2 Sử dụng CDN cho Libraries

**public/index.html**

```html
<head>
  <!-- Vue từ CDN -->
  <script src="https://cdn.jsdelivr.net/npm/vue@3/dist/vue.global.js"></script>
  
  <!-- Axios từ CDN -->
  <script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
</head>
```

**Vite config:**

```javascript
// vite.config.js
export default {
  build: {
    rollupOptions: {
      external: ['vue', 'axios'],
      output: {
        globals: {
          vue: 'Vue',
          axios: 'axios'
        }
      }
    }
  }
}
```

### 2.3 Asset Optimization

**Image Optimization:**

```vue
<template>
  <!-- WebP format -->
  <picture>
    <source srcset="/image.webp" type="image/webp">
    <img src="/image.jpg" alt="Description">
  </picture>
</template>
```

**Font Optimization:**

```css
@font-face {
  font-family: 'CustomFont';
  src: url('/fonts/font.woff2') format('woff2');
  font-display: swap; /* Hiển thị fallback font ngay */
}
```

---

## 3) Caching Strategies

### 3.1 Browser Caching

**vite.config.js**

```javascript
export default {
  build: {
    rollupOptions: {
      output: {
        // Hash filenames cho cache busting
        entryFileNames: 'assets/[name].[hash].js',
        chunkFileNames: 'assets/[name].[hash].js',
        assetFileNames: 'assets/[name].[hash].[ext]'
      }
    }
  }
}
```

### 3.2 Service Worker Caching

**public/sw.js**

```javascript
const CACHE_NAME = 'my-app-v1'
const urlsToCache = [
  '/',
  '/index.html',
  '/assets/main.js',
  '/assets/main.css'
]

self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then((cache) => cache.addAll(urlsToCache))
  )
})

self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request)
      .then((response) => response || fetch(event.request))
  )
})
```

---

## 4) Lazy Loading và Code Splitting

### 4.1 Route-based Code Splitting

```javascript
// router/index.js
const routes = [
  {
    path: '/about',
    component: () => import('@/views/About.vue')
  },
  {
    path: '/contact',
    component: () => import('@/views/Contact.vue')
  }
]
```

### 4.2 Component Lazy Loading

```vue
<script setup>
import { defineAsyncComponent } from 'vue'

const HeavyComponent = defineAsyncComponent(() => 
  import('@/components/HeavyComponent.vue')
)
</script>
```

### 4.3 Dynamic Imports với Loading States

```vue
<template>
  <div>
    <Suspense>
      <template #default>
        <HeavyComponent />
      </template>
      <template #fallback>
        <LoadingSpinner />
      </template>
    </Suspense>
  </div>
</template>

<script setup>
import { defineAsyncComponent } from 'vue'
import LoadingSpinner from '@/components/LoadingSpinner.vue'

const HeavyComponent = defineAsyncComponent(() => 
  import('@/components/HeavyComponent.vue')
)
</script>
```

---

## 5) Performance Monitoring

### 5.1 Web Vitals

```javascript
// utils/webVitals.js
export function measureWebVitals() {
  if (typeof window !== 'undefined') {
    import('web-vitals').then(({ onCLS, onFID, onLCP }) => {
      onCLS(console.log)
      onFID(console.log)
      onLCP(console.log)
    })
  }
}
```

### 5.2 Performance API

```vue
<script setup>
import { onMounted } from 'vue'

onMounted(() => {
  const start = performance.now()
  // Component logic
  const end = performance.now()
  console.log(`Render time: ${end - start}ms`)
})
</script>
```

---

## 6) Memory Management

### 6.1 Cleanup Event Listeners

```vue
<script setup>
import { onMounted, onBeforeUnmount } from 'vue'

const handleResize = () => {
  // Handle resize
}

onMounted(() => {
  window.addEventListener('resize', handleResize)
})

onBeforeUnmount(() => {
  window.removeEventListener('resize', handleResize)
})
</script>
```

### 6.2 Clear Timers

```vue
<script setup>
import { onMounted, onBeforeUnmount } from 'vue'

let timer = null

onMounted(() => {
  timer = setInterval(() => {
    // Do something
  }, 1000)
})

onBeforeUnmount(() => {
  if (timer) {
    clearInterval(timer)
  }
})
</script>
```

---

## 7) Tóm tắt

### 7.1 Những gì đã học

> * Bundle size optimization
> * CDN và asset optimization
> * Caching strategies
> * Lazy loading và code splitting
> * Performance monitoring
> * Memory management

### 7.2 Best Practices

> * Analyze bundle size
> * Use CDN cho libraries lớn
> * Implement code splitting
> * Optimize images và fonts
> * Monitor performance
> * Cleanup resources

### 7.3 Bài tập thực hành

1. Analyze và optimize bundle size
2. Setup CDN cho static assets
3. Implement lazy loading cho routes
4. Add performance monitoring
5. Optimize images
6. Implement caching strategy

---

## 8) Tài liệu tham khảo

> * [Vue.js Performance](https://vuejs.org/guide/best-practices/performance.html)
> * [Web.dev Performance](https://web.dev/performance/)
> * [Bundle Analyzer](https://www.npmjs.com/package/webpack-bundle-analyzer)
