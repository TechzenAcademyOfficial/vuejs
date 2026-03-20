# Vue.js – Buổi 13: Tối ưu hóa màu sắc và SEO cơ bản

## 1) Color System trong Web Design

### 1.1 Color Palette

> Xây dựng color palette nhất quán cho ứng dụng.

**Color Variables:**

```css
:root {
  /* Primary Colors */
  --primary: #42b983;
  --primary-dark: #369870;
  --primary-light: #66c9a3;
  
  /* Secondary Colors */
  --secondary: #6c757d;
  --secondary-dark: #5a6268;
  --secondary-light: #868e96;
  
  /* Neutral Colors */
  --white: #ffffff;
  --black: #000000;
  --gray-100: #f8f9fa;
  --gray-200: #e9ecef;
  --gray-300: #dee2e6;
  --gray-400: #ced4da;
  --gray-500: #adb5bd;
  --gray-600: #6c757d;
  --gray-700: #495057;
  --gray-800: #343a40;
  --gray-900: #212529;
  
  /* Semantic Colors */
  --success: #28a745;
  --warning: #ffc107;
  --error: #dc3545;
  --info: #17a2b8;
}
```

### 1.2 Sử dụng trong Vue Component

```vue
<style scoped>
.button {
  background-color: var(--primary);
  color: var(--white);
}

.button:hover {
  background-color: var(--primary-dark);
}
</style>
```

---

## 2) Dark Mode

### 2.1 Implement Dark Mode

```vue
<script setup>
import { ref, onMounted } from 'vue'

const isDarkMode = ref(false)

const applyTheme = () => {
  if (isDarkMode.value) {
    document.documentElement.classList.add('dark')
  } else {
    document.documentElement.classList.remove('dark')
  }
}

const toggleDarkMode = () => {
  isDarkMode.value = !isDarkMode.value
  localStorage.setItem('darkMode', isDarkMode.value)
  applyTheme()
}

onMounted(() => {
  // Check localStorage hoặc system preference
  const saved = localStorage.getItem('darkMode')
  const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches
  isDarkMode.value = saved !== null ? saved === 'true' : prefersDark
  applyTheme()
})
</script>
```

**CSS Variables cho Dark Mode:**

```css
:root {
  --bg-color: #ffffff;
  --text-color: #000000;
}

.dark {
  --bg-color: #1a1a1a;
  --text-color: #ffffff;
}

body {
  background-color: var(--bg-color);
  color: var(--text-color);
  transition: background-color 0.3s, color 0.3s;
}
```

---

## 3) SEO Cơ bản

### 3.1 Meta Tags

> Sử dụng Vue Meta hoặc manual để quản lý meta tags.

**Cài đặt vue-meta:**

```bash
npm install vue-meta
```

**Sử dụng:**

```vue
<script setup>
import { useHead } from '@unhead/vue'

useHead({
  title: 'Trang chủ - My App',
  meta: [
    {
      name: 'description',
      content: 'Mô tả trang web của bạn'
    },
    {
      property: 'og:title',
      content: 'Trang chủ - My App'
    },
    {
      property: 'og:description',
      content: 'Mô tả trang web của bạn'
    },
    {
      property: 'og:image',
      content: 'https://example.com/image.jpg'
    }
  ]
})
</script>
```

> **Lưu ý:** Với Vue 3 thuần, có thể sử dụng `useHead` từ `@unhead/vue` hoặc quản lý meta tags thủ công trong `onMounted`.

### 3.2 Dynamic Meta Tags với Router

```javascript
// router/index.js
router.beforeEach((to, from, next) => {
  document.title = to.meta.title || 'My App'
  
  // Update meta description
  const metaDescription = document.querySelector('meta[name="description"]')
  if (metaDescription) {
    metaDescription.setAttribute('content', to.meta.description || '')
  }
  
  next()
})
```

---

## 4) Structured Data

### 4.1 JSON-LD Schema

```vue
<script setup>
import { computed, onMounted } from 'vue'

const structuredData = computed(() => {
  return {
    '@context': 'https://schema.org',
    '@type': 'WebSite',
    name: 'My App',
    url: 'https://example.com',
    description: 'Mô tả website'
  }
})

onMounted(() => {
  const script = document.createElement('script')
  script.type = 'application/ld+json'
  script.text = JSON.stringify(structuredData.value)
  document.head.appendChild(script)
})
</script>
```

---

## 5) Semantic HTML

### 5.1 Sử dụng Semantic Elements

```vue
<template>
  <header>
    <nav>
      <!-- Navigation -->
    </nav>
  </header>
  
  <main>
    <article>
      <h1>Tiêu đề bài viết</h1>
      <section>
        <!-- Content -->
      </section>
    </article>
  </main>
  
  <aside>
    <!-- Sidebar -->
  </aside>
  
  <footer>
    <!-- Footer -->
  </footer>
</template>
```

---

## 6) Image Optimization

### 6.1 Lazy Loading Images

```vue
<template>
  <img 
    :src="imageSrc"
    loading="lazy"
    alt="Description"
  />
</template>
```

### 6.2 Responsive Images

```vue
<template>
  <img 
    :srcset="`
      /image-small.jpg 480w,
      /image-medium.jpg 768w,
      /image-large.jpg 1200w
    `"
    sizes="(max-width: 768px) 480px, (max-width: 1200px) 768px, 1200px"
    src="/image-medium.jpg"
    alt="Description"
  />
</template>
```

---

## 7) Tóm tắt

### 7.1 Những gì đã học

> * Color system và palette
> * Dark mode implementation
> * SEO cơ bản: meta tags, structured data
> * Semantic HTML
> * Image optimization

### 7.2 Best Practices

> * Sử dụng CSS variables cho colors
> * Implement dark mode
> * Thêm meta tags cho SEO
> * Sử dụng semantic HTML
> * Optimize images

### 7.3 Bài tập thực hành

1. Tạo color system với CSS variables
2. Implement dark mode toggle
3. Thêm meta tags cho các routes
4. Add structured data
5. Optimize images với lazy loading

---

## 8) Tài liệu tham khảo

> * [Web Content Accessibility Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)
> * [Schema.org](https://schema.org/)
> * [Vue Meta](https://github.com/nuxt/vue-meta)
