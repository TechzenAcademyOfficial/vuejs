# Vue.js – Buổi 5: Slot & Lifecycle

## 1) Slots - Content Distribution

### 1.1 Slot cơ bản

> **Slot** cho phép component nhận và render content từ parent component.

**components/Card.vue**

```vue
<template>
  <div class="card">
    <div class="card-header">
      <slot name="header">Default Header</slot>
    </div>
    <div class="card-body">
      <slot>Default Content</slot>
    </div>
    <div class="card-footer">
      <slot name="footer"></slot>
    </div>
  </div>
</template>
```

**Sử dụng:**

```vue
<template>
  <Card>
    <template #header>
      <h2>Tiêu đề</h2>
    </template>
    
    <p>Nội dung chính</p>
    
    <template #footer>
      <button>Xem thêm</button>
    </template>
  </Card>
</template>
```

### 1.2 Default Slot

> Slot không có name là default slot.

```vue
<!-- Component -->
<template>
  <div>
    <slot>Default content nếu không có content từ parent</slot>
  </div>
</template>

<!-- Sử dụng -->
<MyComponent>
  <p>Content này sẽ thay thế default slot</p>
</MyComponent>
```

### 1.3 Named Slots

> Slot có tên cụ thể để phân biệt các vị trí khác nhau.

```vue
<!-- Component -->
<template>
  <div class="container">
    <header>
      <slot name="header"></slot>
    </header>
    <main>
      <slot name="main"></slot>
    </main>
    <aside>
      <slot name="sidebar"></slot>
    </aside>
  </div>
</template>

<!-- Sử dụng -->
<template>
  <Layout>
    <template #header>
      <h1>Header Content</h1>
    </template>
    
    <template #main>
      <p>Main Content</p>
    </template>
    
    <template #sidebar>
      <nav>Sidebar</nav>
    </template>
  </Layout>
</template>
```

### 1.4 Scoped Slots

> **Scoped slots** cho phép child component truyền data lên parent.

**components/List.vue**

```vue
<template>
  <ul>
    <li v-for="(item, index) in items" :key="item.id">
      <slot :item="item" :index="index"></slot>
    </li>
  </ul>
</template>

<script setup>
defineProps({
  items: Array
})
</script>
```

**Sử dụng:**

```vue
<template>
  <List :items="products">
    <template #default="{ item, index }">
      <div>
        <h3>{{ item.name }}</h3>
        <p>Giá: {{ item.price }} VND</p>
        <span>#{{ index + 1 }}</span>
      </div>
    </template>
  </List>
</template>
```

### 1.5 Slot Props (Vue 3)

> Vue 3 sử dụng `v-slot` với destructuring.

```vue
<template>
  <List :items="users">
    <template v-slot:default="{ item: user }">
      <div>{{ user.name }}</div>
    </template>
  </List>
</template>
```

---

## 2) Component Lifecycle chi tiết

### 2.1 Lifecycle Hooks Overview

> Component có lifecycle từ khi được tạo đến khi bị destroy.

```
beforeCreate → created → beforeMount → mounted 
→ beforeUpdate → updated → beforeUnmount → unmounted
```

### 2.2 Creation Phase

#### 2.2.1 beforeCreate

> Chạy trước khi instance được khởi tạo.

```vue
<script setup>
// Composition API không có beforeCreate
// Setup function chạy tương đương created
// Code ở đây chạy trước khi component được mount
console.log('Setup function - tương đương created')
</script>
```

**Use case:**
> Hiếm khi dùng, thường dùng cho plugins

#### 2.2.2 created

> Chạy sau khi instance được tạo, data và methods đã sẵn sàng.

```vue
<script setup>
import { ref } from 'vue'

const users = ref([])

// Fetch data trong setup function (tương đương created)
const response = await fetch('/api/users')
users.value = await response.json()
</script>
```

**Use case:**
> Fetch data từ API, setup timers, subscriptions

### 2.3 Mounting Phase

#### 2.3.1 beforeMount

> Chạy trước khi component được mount vào DOM.

```vue
<script setup>
import { onBeforeMount } from 'vue'

onBeforeMount(() => {
  // Template đã được compile
  // Nhưng chưa mount vào DOM
  console.log('beforeMount')
})
</script>
```

#### 2.3.2 mounted

> Chạy sau khi component được mount vào DOM.

```vue
<template>
  <div ref="container">
    <input ref="input" type="text" />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const container = ref(null)
const input = ref(null)

const initChart = () => {
  // Initialize chart library
}

const handleResize = () => {
  // Handle window resize
}

onMounted(() => {
  // Có thể access DOM
  input.value?.focus()
  
  // Setup third-party libraries
  initChart()
  
  // Add event listeners
  window.addEventListener('resize', handleResize)
})
</script>
```

**Use case:**
> Truy cập DOM, setup third-party libraries, add event listeners

### 2.4 Updating Phase

#### 2.4.1 beforeUpdate

> Chạy trước khi component được update.

```vue
<script setup>
import { ref, onBeforeUpdate } from 'vue'

const count = ref(0)

onBeforeUpdate(() => {
  // Data đã thay đổi
  // Nhưng DOM chưa được update
  console.log('beforeUpdate', count.value)
})
</script>
```

#### 2.4.2 updated

> Chạy sau khi component được update.

```vue
<script setup>
import { onUpdated } from 'vue'

onUpdated(() => {
  // DOM đã được update
  // Cẩn thận với infinite loops
  console.log('updated')
})
</script>
```

**Lưu ý:**
> Tránh thay đổi data trong `updated` để tránh infinite loops

### 2.5 Unmounting Phase

#### 2.5.1 beforeUnmount

> Chạy trước khi component được unmount.

```vue
<script setup>
import { onMounted, onBeforeUnmount } from 'vue'

let timer = null

const handleResize = () => {
  // Handle resize
}

onMounted(() => {
  timer = setInterval(() => {
    console.log('Tick')
  }, 1000)
  
  window.addEventListener('resize', handleResize)
})

onBeforeUnmount(() => {
  // Cleanup
  if (timer) {
    clearInterval(timer)
  }
  
  // Remove event listeners
  window.removeEventListener('resize', handleResize)
})
</script>
```

**Use case:**
> Cleanup timers, remove event listeners, cancel API requests

#### 2.5.2 unmounted

> Chạy sau khi component được unmount.

```vue
<script setup>
import { onUnmounted } from 'vue'

onUnmounted(() => {
  // Component đã bị remove khỏi DOM
  console.log('Component unmounted')
})
</script>
```

---

## 3) Lifecycle trong thực tế

### 3.1 Fetching Data

```vue
<script setup>
import { ref } from 'vue'

const posts = ref([])
const loading = ref(false)
const error = ref(null)

const fetchPosts = async () => {
  loading.value = true
  try {
    const response = await fetch('/api/posts')
    posts.value = await response.json()
  } catch (err) {
    error.value = err.message
  } finally {
    loading.value = false
  }
}

// Fetch data khi component được tạo
await fetchPosts()
</script>
```

### 3.2 Setup và Cleanup

```vue
<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'

const canvas = ref(null)
let chart = null
let resizeHandler = null

onMounted(() => {
  // Setup chart
  chart = new Chart(canvas.value, {
    // config
  })
  
  // Add resize listener
  resizeHandler = () => {
    chart.resize()
  }
  window.addEventListener('resize', resizeHandler)
})

onBeforeUnmount(() => {
  // Destroy chart
  if (chart) {
    chart.destroy()
  }
  
  // Remove listener
  if (resizeHandler) {
    window.removeEventListener('resize', resizeHandler)
  }
})
</script>
```

### 3.3 Watchers với Lifecycle

```vue
<script setup>
import { ref, watch } from 'vue'

const userId = ref(null)
const user = ref(null)

const fetchUser = async (id) => {
  const response = await fetch(`/api/users/${id}`)
  user.value = await response.json()
}

watch(userId, (newId) => {
  if (newId) {
    fetchUser(newId)
  }
}, { immediate: true })
</script>
```

---

## 4) Advanced Slot Patterns

### 4.1 Conditional Slots

```vue
<template>
  <div>
    <slot v-if="hasHeader" name="header"></slot>
    <slot></slot>
    <slot v-if="hasFooter" name="footer"></slot>
  </div>
</template>

<script setup>
defineProps({
  hasHeader: Boolean,
  hasFooter: Boolean
})
</script>
```

### 4.2 Slot Fallback Content

```vue
<template>
  <div class="alert">
    <slot name="icon">
      <span class="default-icon">⚠️</span>
    </slot>
    <slot>Default message</slot>
  </div>
</template>
```

### 4.3 Multiple Slots với Scoped Slots

```vue
<template>
  <div>
    <slot name="item" v-for="(item, index) in items" :key="item.id" :item="item" :index="index">
      <div>{{ item.name }}</div>
    </slot>
  </div>
</template>
```

---

## 5) Tóm tắt

### 5.1 Những gì đã học

> * Slots: default slots, named slots, scoped slots
> * Component lifecycle: creation, mounting, updating, unmounting
> * Lifecycle hooks: beforeCreate, created, beforeMount, mounted, beforeUpdate, updated, beforeUnmount, unmounted
> * Best practices cho lifecycle hooks
> * Advanced slot patterns

### 5.2 Best Practices

> * Fetch data trong `created` hoặc `mounted`
> * Setup DOM-related code trong `mounted`
> * Cleanup trong `beforeUnmount`
> * Tránh thay đổi data trong `updated`
> * Sử dụng scoped slots để truyền data từ child lên parent
> * Cung cấp fallback content cho slots

### 5.3 Bài tập thực hành

1. Tạo component `Modal` với slots cho header, body, footer
2. Tạo component `DataTable` với scoped slots để custom cell rendering
3. Tạo component fetch data từ API trong `created` hook
4. Setup và cleanup event listeners trong lifecycle hooks
5. Tạo component với conditional slots
6. Sử dụng watchers kết hợp với lifecycle hooks

---

## 6) Tài liệu tham khảo

> * [Vue.js Slots](https://vuejs.org/guide/components/slots.html)
> * [Vue.js Component Lifecycle](https://vuejs.org/guide/essentials/lifecycle.html)
> * [Vue.js Scoped Slots](https://vuejs.org/guide/components/slots.html#scoped-slots)
