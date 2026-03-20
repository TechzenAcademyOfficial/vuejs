# Vue.js – Buổi 10: Quản lý trạng thái – Vuex/Pinia

## 1) State Management là gì?

### 1.1 Vấn đề với Props và Events

> Khi ứng dụng lớn, việc truyền data qua props và events trở nên phức tạp và khó quản lý.

**Vấn đề:**
> * Props drilling: truyền props qua nhiều component
> * Event bubbling: emit events qua nhiều tầng
> * Khó đồng bộ state giữa các components
> * Khó debug và maintain

### 1.2 State Management Solution

> **State Management** tập trung quản lý state của ứng dụng ở một nơi.

**Lợi ích:**
> * Centralized state
> * Predictable state changes
> * Easier debugging
> * Better code organization

---

## 2) Vuex (Vue 2)

### 2.1 Vuex là gì?

> **Vuex** là state management pattern và library cho Vue.js applications.

**Core Concepts:**
> * **State**: Dữ liệu của ứng dụng
> * **Getters**: Computed properties cho state
> * **Mutations**: Synchronous changes to state
> * **Actions**: Asynchronous operations
> * **Modules**: Chia nhỏ store thành modules

### 2.2 Cài đặt Vuex

```bash
npm install vuex@4
```

### 2.3 Store cơ bản

**store/index.js**

```javascript
import { createStore } from 'vuex'

export default createStore({
  state: {
    count: 0,
    user: null
  },
  getters: {
    doubleCount(state) {
      return state.count * 2
    },
    isAuthenticated(state) {
      return state.user !== null
    }
  },
  mutations: {
    INCREMENT(state) {
      state.count++
    },
    SET_USER(state, user) {
      state.user = user
    }
  },
  actions: {
    increment({ commit }) {
      commit('INCREMENT')
    },
    async login({ commit }, credentials) {
      const user = await api.login(credentials)
      commit('SET_USER', user)
    }
  }
})
```

**main.js**

```javascript
import { createApp } from 'vue'
import App from './App.vue'
import store from './store'

const app = createApp(App)
app.use(store)
app.mount('#app')
```

### 2.4 Sử dụng trong Component

```vue
<template>
  <div>
    <p>Count: {{ count }}</p>
    <p>Double: {{ doubleCount }}</p>
    <button @click="increment">Increment</button>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { useStore } from 'vuex'

const store = useStore()

const count = computed(() => store.state.count)
const doubleCount = computed(() => store.getters.doubleCount)

const increment = () => {
  store.dispatch('increment')
}
</script>
```

---

## 3) Pinia (Vue 3 - Khuyến nghị)

### 3.1 Pinia là gì?

> **Pinia** là state management library mới cho Vue 3, thay thế Vuex.

**Ưu điểm:**
> * TypeScript support tốt hơn
> * DevTools support
> * Simpler API
> * Better code splitting
> * No mutations, chỉ có actions

### 3.2 Cài đặt Pinia

```bash
npm install pinia
```

### 3.3 Store với Pinia

**stores/counter.js**

```javascript
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0,
    user: null
  }),
  getters: {
    doubleCount(state) {
      return state.count * 2
    },
    isAuthenticated: (state) => state.user !== null
  },
  actions: {
    increment() {
      this.count++
    },
    async login(credentials) {
      const user = await api.login(credentials)
      this.user = user
    },
    logout() {
      this.user = null
    }
  }
})
```

**main.js**

```javascript
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import App from './App.vue'

const app = createApp(App)
app.use(createPinia())
app.mount('#app')
```

### 3.4 Sử dụng Pinia Store

```vue
<template>
  <div>
    <p>Count: {{ counterStore.count }}</p>
    <p>Double: {{ counterStore.doubleCount }}</p>
    <button @click="counterStore.increment">Increment</button>
  </div>
</template>

<script setup>
import { useCounterStore } from '@/stores/counter'

const counterStore = useCounterStore()
</script>
```

---

## 4) Pinia Patterns

### 4.1 Multiple Stores

**stores/user.js**

```javascript
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', {
  state: () => ({
    user: null,
    token: null
  }),
  getters: {
    isAuthenticated: (state) => state.user !== null
  },
  actions: {
    async login(credentials) {
      const response = await api.login(credentials)
      this.user = response.user
      this.token = response.token
      localStorage.setItem('token', this.token)
    },
    logout() {
      this.user = null
      this.token = null
      localStorage.removeItem('token')
    }
  }
})
```

**stores/products.js**

```javascript
import { defineStore } from 'pinia'

export const useProductStore = defineStore('products', {
  state: () => ({
    products: [],
    loading: false
  }),
  getters: {
    productById: (state) => (id) => {
      return state.products.find(p => p.id === id)
    }
  },
  actions: {
    async fetchProducts() {
      this.loading = true
      try {
        const response = await api.get('/products')
        this.products = response.data
      } finally {
        this.loading = false
      }
    }
  }
})
```

### 4.2 Sử dụng Multiple Stores

```vue
<script setup>
import { useUserStore } from '@/stores/user'
import { useProductStore } from '@/stores/products'

const userStore = useUserStore()
const productStore = useProductStore()
</script>
```

---

## 5) State Persistence

### 5.1 Persist State với Pinia Plugin

```bash
npm install pinia-plugin-persistedstate
```

**main.js**

```javascript
import { createPinia } from 'pinia'
import piniaPluginPersistedstate from 'pinia-plugin-persistedstate'

const pinia = createPinia()
pinia.use(piniaPluginPersistedstate)
```

**Store:**

```javascript
export const useUserStore = defineStore('user', {
  state: () => ({
    user: null
  }),
  persist: true // Tự động persist state
})
```

---

## 6) Best Practices

### 6.1 Organize Stores

```
stores/
  index.js
  user.js
  products.js
  cart.js
  ui.js
```

### 6.2 Naming Conventions

> * Store names: camelCase (user, productStore)
> * Actions: camelCase (fetchUser, updateProduct)
> * Getters: camelCase (isAuthenticated, productById)

### 6.3 When to Use State Management

> * Shared state giữa nhiều components
> * Complex state logic
> * Need for time-travel debugging
> * Server state caching

---

## 7) Tóm tắt

### 7.1 Những gì đã học

> * State management là gì và tại sao cần
> * Vuex cho Vue 2
> * Pinia cho Vue 3
> * Store patterns và best practices
> * State persistence

### 7.2 Best Practices

> * Sử dụng Pinia cho Vue 3 projects
> * Chia stores theo domain
> * Sử dụng getters cho derived state
> * Actions cho async operations
> * Persist state khi cần

### 7.3 Bài tập thực hành

1. Tạo user store với Pinia
2. Tạo product store với CRUD operations
3. Implement cart functionality với store
4. Add state persistence
5. Connect stores với API calls
6. Implement authentication flow với store

---

## 8) Tài liệu tham khảo

> * [Pinia Documentation](https://pinia.vuejs.org/)
> * [Vuex Documentation](https://vuex.vuejs.org/)
> * [State Management Guide](https://vuejs.org/guide/scaling-up/state-management.html)
