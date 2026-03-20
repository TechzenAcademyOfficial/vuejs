# Vue.js – Buổi 9: Router nâng cao

## 1) Advanced Routing Patterns

### 1.1 Named Views

> Hiển thị nhiều components cùng lúc với named router-view.

**router/index.js**

```javascript
{
  path: '/dashboard',
  components: {
    default: Dashboard,
    sidebar: Sidebar,
    header: Header
  }
}
```

**App.vue**

```vue
<template>
  <div>
    <router-view name="header" />
    <div class="main">
      <router-view name="sidebar" />
      <router-view />
    </div>
  </div>
</template>
```

### 1.2 Redirect và Alias

```javascript
{
  path: '/home',
  redirect: '/'
},
{
  path: '/',
  alias: '/home',
  component: Home
}
```

### 1.3 Catch-all Routes

```javascript
{
  path: '/:pathMatch(.*)*',
  name: 'NotFound',
  component: NotFound
}
```

---

## 2) Route Meta Fields

### 2.1 Meta Fields

```javascript
{
  path: '/admin',
  meta: {
    requiresAuth: true,
    requiresAdmin: true,
    title: 'Admin Panel',
    breadcrumb: 'Admin'
  }
}
```

### 2.2 Sử dụng Meta trong Guards

```javascript
router.beforeEach((to, from, next) => {
  // Set page title
  document.title = to.meta.title || 'My App'
  
  // Check authentication
  if (to.meta.requiresAuth && !isAuthenticated()) {
    next('/login')
  } else {
    next()
  }
})
```

---

## 3) Advanced Navigation Guards

### 3.1 Global Guards

```javascript
// Before each route
router.beforeEach((to, from, next) => {
  // Logic here
  next()
})

// After each route
router.afterEach((to, from) => {
  // Analytics, logging
})

// Before resolve
router.beforeResolve((to, from, next) => {
  // Similar to beforeEach but after components resolved
  next()
})
```

### 3.2 Per-Route Guards

```javascript
{
  path: '/profile',
  beforeEnter: (to, from, next) => {
    if (!isAuthenticated()) {
      next('/login')
    } else {
      next()
    }
  }
}
```

### 3.3 Component Guards

```vue
<script setup>
import { ref } from 'vue'
import { onBeforeRouteLeave, onBeforeRouteUpdate } from 'vue-router'

const hasUnsavedChanges = ref(false)

const checkPermission = (id) => {
  // Check permission logic
  return true
}

const fetchData = () => {
  // Fetch data logic
}

// Fetch data khi route enter (trong setup)
fetchData()

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

## 4) Route Transitions

### 4.1 Transition giữa Routes

```vue
<template>
  <router-view v-slot="{ Component }">
    <transition name="fade" mode="out-in">
      <component :is="Component" />
    </transition>
  </router-view>
</template>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
```

### 4.2 Per-Route Transitions

```vue
<script setup>
import { ref, watch } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const transitionName = ref('fade')

watch(() => route.path, (to, from) => {
  const toDepth = to.split('/').length
  const fromDepth = from.split('/').length
  transitionName.value = toDepth < fromDepth ? 'slide-right' : 'slide-left'
})
</script>

<template>
  <transition :name="transitionName">
    <router-view />
  </transition>
</template>
```

---

## 5) Data Fetching với Router

### 5.1 Fetching Before Navigation

```javascript
router.beforeEach(async (to, from, next) => {
  if (to.meta.requiresAuth) {
    try {
      await checkAuth()
      next()
    } catch (error) {
      next('/login')
    }
  } else {
    next()
  }
})
```

### 5.2 Fetching After Navigation

```vue
<script setup>
import { ref } from 'vue'
import { onBeforeRouteUpdate } from 'vue-router'

const user = ref(null)
const loading = ref(false)

// Fetch user khi component được tạo
const fetchUserData = async (id) => {
  try {
    const userData = await fetchUser(id)
    user.value = userData
  } catch (error) {
    // Handle error
  }
}

// Fetch initial data
const route = useRoute()
await fetchUserData(route.params.id)

onBeforeRouteUpdate(async (to, from, next) => {
  loading.value = true
  try {
    await fetchUserData(to.params.id)
    next()
  } catch (error) {
    next(false)
  } finally {
    loading.value = false
  }
})
</script>
```

---

## 6) Scroll Behavior

### 6.1 Custom Scroll Behavior

```javascript
const router = createRouter({
  scrollBehavior(to, from, savedPosition) {
    if (savedPosition) {
      return savedPosition
    } else if (to.hash) {
      return {
        el: to.hash,
        behavior: 'smooth'
      }
    } else {
      return { top: 0 }
    }
  }
})
```

---

## 7) Route Matching

### 7.1 Custom Matching

```javascript
const router = createRouter({
  routes: [
    {
      path: '/user/:id',
      component: User,
      props: true // Pass params as props
    }
  ]
})
```

### 7.2 Route Props

```javascript
{
  path: '/user/:id',
  component: User,
  props: route => ({
    id: route.params.id,
    query: route.query
  })
}
```

**Component:**

```vue
<script setup>
defineProps(['id', 'query'])
</script>
```

---

## 8) Router Composition API

### 8.1 useRouter và useRoute

```vue
<script setup>
import { useRouter, useRoute } from 'vue-router'

const router = useRouter()
const route = useRoute()

const goToHome = () => {
  router.push('/')
}

const userId = route.params.id
</script>
```

### 8.2 Navigation Guards với Composition API

```vue
<script setup>
import { ref } from 'vue'
import { onBeforeRouteLeave } from 'vue-router'

const hasUnsavedChanges = ref(false)

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

## 9) Tóm tắt

### 9.1 Những gì đã học

> * Named views
> * Redirect và alias
> * Catch-all routes
> * Route meta fields
> * Advanced navigation guards
> * Route transitions
> * Data fetching với router
> * Scroll behavior
> * Router Composition API

### 9.2 Best Practices

> * Sử dụng meta fields cho route metadata
> * Implement proper navigation guards
> * Handle data fetching trong guards hoặc components
> * Custom scroll behavior cho UX tốt hơn
> * Sử dụng Composition API cho Vue 3

### 9.3 Bài tập thực hành

1. Implement authentication guard
2. Tạo 404 page với catch-all route
3. Add transitions giữa routes
4. Implement breadcrumb với meta fields
5. Custom scroll behavior
6. Fetch data trong navigation guards

---

## 10) Tài liệu tham khảo

> * [Vue Router Advanced](https://router.vuejs.org/guide/advanced/)
> * [Vue Router Navigation Guards](https://router.vuejs.org/guide/advanced/navigation-guards.html)
> * [Vue Router Transitions](https://router.vuejs.org/guide/advanced/transitions.html)
