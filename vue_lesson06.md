# Vue.js – Buổi 6: Router căn bản

## 1) Vue Router là gì?

### 1.1 Single Page Application (SPA)

> **SPA** là ứng dụng web chỉ có một HTML page, navigation được xử lý bởi JavaScript thay vì reload page.

**Lợi ích:**
> * Trải nghiệm người dùng mượt mà
> * Không cần reload page khi navigate
> * Có thể quản lý state giữa các trang
> * Tốt hơn cho performance

### 1.2 Vue Router

> **Vue Router** là official router cho Vue.js, cho phép xây dựng SPA với client-side routing.

**Tính năng:**
> * Nested routes
> * Route params và query
> * Navigation guards
> * Lazy loading components
> * Transition animations

---

## 2) Cài đặt Vue Router

### 2.1 Cài đặt Package

```bash
npm install vue-router@4
```

### 2.2 Cấu hình Router cơ bản

**router/index.js**

```javascript
import { createRouter, createWebHistory } from 'vue-router'
import Home from '../views/Home.vue'
import About from '../views/About.vue'

const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/about',
    name: 'About',
    component: About
  }
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

export default router
```

### 2.3 Sử dụng Router trong App

**main.js**

```javascript
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

const app = createApp(App)
app.use(router)
app.mount('#app')
```

**App.vue**

```vue
<template>
  <div id="app">
    <nav>
      <router-link to="/">Home</router-link>
      <router-link to="/about">About</router-link>
    </nav>
    
    <router-view />
  </div>
</template>
```

---

## 3) Navigation

### 3.1 router-link

> **router-link** là component để tạo navigation links.

```vue
<template>
  <nav>
    <router-link to="/">Home</router-link>
    <router-link to="/about">About</router-link>
    <router-link :to="{ name: 'About' }">About (by name)</router-link>
  </nav>
</template>
```

**Props:**
> * `to`: Path hoặc route object
> * `replace`: Thay thế history thay vì push
> * `active-class`: Class khi route active

### 3.2 Programmatic Navigation

```vue
<script setup>
import { useRouter } from 'vue-router'

const router = useRouter()

const goToHome = () => {
  router.push('/')
}

const goToAbout = () => {
  router.push({ name: 'About' })
}

const goBack = () => {
  router.go(-1)
}

const replaceRoute = () => {
  router.replace('/about')
}
</script>
```

### 3.3 Navigation Methods

| Method | Mô tả |
|--------|-------|
| `push(path)` | Điều hướng đến route mới |
| `replace(path)` | Thay thế route hiện tại |
| `go(n)` | Đi/tiến trong history |
| `back()` | Quay lại trang trước |
| `forward()` | Đi tiếp trang sau |

---

## 4) Route Params và Query

### 4.1 Dynamic Routes

**router/index.js**

```javascript
{
  path: '/user/:id',
  name: 'User',
  component: User
}
```

**Truy cập params:**

```vue
<template>
  <div>
    <h1>User ID: {{ userId }}</h1>
  </div>
</template>

<script setup>
import { useRoute } from 'vue-router'

const route = useRoute()
const userId = route.params.id

console.log('User ID:', userId)
</script>
```

### 4.2 Query Parameters

```vue
<template>
  <div>
    <p>Search: {{ query.q }}</p>
    <p>Page: {{ query.page }}</p>
  </div>
</template>

<script setup>
import { useRoute } from 'vue-router'

const route = useRoute()
const query = route.query
</script>
```

**Navigation với query:**

```vue
<script setup>
import { useRouter } from 'vue-router'

const router = useRouter()

router.push({
  path: '/search',
  query: {
    q: 'vue.js',
    page: 1
  }
})
</script>
```

### 4.3 Multiple Params

```javascript
{
  path: '/user/:userId/post/:postId',
  name: 'UserPost',
  component: UserPost
}
```

---

## 5) Nested Routes

### 5.1 Cấu hình Nested Routes

**router/index.js**

```javascript
{
  path: '/user/:id',
  component: UserLayout,
  children: [
    {
      path: '',
      name: 'UserProfile',
      component: UserProfile
    },
    {
      path: 'posts',
      name: 'UserPosts',
      component: UserPosts
    },
    {
      path: 'settings',
      name: 'UserSettings',
      component: UserSettings
    }
  ]
}
```

**UserLayout.vue**

```vue
<template>
  <div>
    <h2>User Layout</h2>
    <nav>
      <router-link :to="{ name: 'UserProfile', params: { id: $route.params.id } }">
        Profile
      </router-link>
      <router-link :to="{ name: 'UserPosts', params: { id: $route.params.id } }">
        Posts
      </router-link>
      <router-link :to="{ name: 'UserSettings', params: { id: $route.params.id } }">
        Settings
      </router-link>
    </nav>
    
    <router-view />
  </div>
</template>
```

---

## 6) Navigation Guards

### 6.1 Global Before Guards

**router/index.js**

```javascript
router.beforeEach((to, from, next) => {
  // Check authentication
  if (to.meta.requiresAuth && !isAuthenticated()) {
    next('/login')
  } else {
    next()
  }
})
```

### 6.2 Route Guards

```javascript
{
  path: '/dashboard',
  name: 'Dashboard',
  component: Dashboard,
  meta: {
    requiresAuth: true
  },
  beforeEnter(to, from, next) {
    if (!isAuthenticated()) {
      next('/login')
    } else {
      next()
    }
  }
}
```

### 6.3 Component Guards

```vue
<script setup>
import { onBeforeRouteLeave, onBeforeRouteUpdate } from 'vue-router'
import { ref } from 'vue'

const hasUnsavedChanges = ref(false)

const checkPermission = (id) => {
  // Check permission logic
  return true
}

onBeforeRouteUpdate((to, from, next) => {
  // Có access đến setup scope
  if (checkPermission(to.params.id)) {
    next()
  } else {
    next(false)
  }
})

onBeforeRouteLeave((to, from, next) => {
  if (hasUnsavedChanges.value) {
    if (confirm('Bạn có muốn rời khỏi trang?')) {
      next()
    } else {
      next(false)
    }
  } else {
    next()
  }
})
</script>
```

---

## 7) Lazy Loading

### 7.1 Lazy Load Components

```javascript
const routes = [
  {
    path: '/about',
    name: 'About',
    component: () => import('../views/About.vue')
  },
  {
    path: '/dashboard',
    name: 'Dashboard',
    component: () => import('../views/Dashboard.vue')
  }
]
```

### 7.2 Lazy Load với Loading Component

```javascript
const routes = [
  {
    path: '/heavy',
    name: 'Heavy',
    component: () => ({
      component: import('../views/Heavy.vue'),
      loading: LoadingComponent,
      error: ErrorComponent,
      delay: 200,
      timeout: 3000
    })
  }
]
```

---

## 8) Route Meta và Transitions

### 8.1 Route Meta

```javascript
{
  path: '/admin',
  name: 'Admin',
  component: Admin,
  meta: {
    requiresAuth: true,
    requiresAdmin: true,
    title: 'Admin Panel'
  }
}
```

**Sử dụng:**

```javascript
router.beforeEach((to, from, next) => {
  document.title = to.meta.title || 'My App'
  next()
})
```

### 8.2 Transitions

```vue
<template>
  <transition name="fade">
    <router-view />
  </transition>
</template>

<style>
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s;
}

.fade-enter-from, .fade-leave-to {
  opacity: 0;
}
</style>
```

---

## 9) Tóm tắt

### 9.1 Những gì đã học

> * Vue Router là gì và tại sao sử dụng
> * Cài đặt và cấu hình router
> * Navigation: router-link và programmatic
> * Route params và query
> * Nested routes
> * Navigation guards
> * Lazy loading
> * Route meta và transitions

### 9.2 Best Practices

> * Sử dụng named routes thay vì paths
> * Lazy load các route lớn
> * Sử dụng navigation guards cho authentication
> * Validate route params
> * Sử dụng meta để quản lý page titles

### 9.3 Bài tập thực hành

1. Tạo router với các routes: Home, About, Contact
2. Thêm dynamic route `/user/:id`
3. Tạo nested routes cho user profile
4. Thêm navigation guards cho protected routes
5. Implement lazy loading cho các routes
6. Thêm transitions giữa các routes

---

## 10) Tài liệu tham khảo

> * [Vue Router Official Docs](https://router.vuejs.org/)
> * [Vue Router Guide](https://router.vuejs.org/guide/)
> * [Vue Router API Reference](https://router.vuejs.org/api/)
