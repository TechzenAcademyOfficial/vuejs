# Vue.js – Buổi 11: Composition API

## 1) Composition API là gì?

### 1.1 Options API vs Composition API

> **Composition API** là cách viết component mới trong Vue 3, bổ sung cho Options API.

**Options API (Vue 2 style - Legacy):**

```vue
<script>
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment() {
      this.count++
    }
  },
  computed: {
    doubleCount() {
      return this.count * 2
    }
  }
}
</script>
```

> **Lưu ý:** Options API vẫn được hỗ trợ trong Vue 3, nhưng Composition API được khuyến nghị cho các dự án mới.

**Composition API (Vue 3):**

```vue
<script setup>
import { ref, computed } from 'vue'

const count = ref(0)
const doubleCount = computed(() => count.value * 2)

function increment() {
  count.value++
}
</script>
```

### 1.2 Lợi ích của Composition API

> * Better code organization
> * Better TypeScript support
> * Better reusability với composables
> * Better performance với tree-shaking

---

## 2) Setup Function

### 2.1 Basic Setup

```vue
<script>
import { ref, computed, onMounted } from 'vue'

export default {
  setup() {
    const count = ref(0)
    
    const increment = () => {
      count.value++
    }
    
    onMounted(() => {
      console.log('Component mounted')
    })
    
    return {
      count,
      increment
    }
  }
}
</script>
```

### 2.2 Script Setup (Khuyến nghị)

```vue
<script setup>
import { ref, computed, onMounted } from 'vue'

const count = ref(0)

const increment = () => {
  count.value++
}

onMounted(() => {
  console.log('Component mounted')
})
</script>
```

---

## 3) Reactivity API

### 3.1 ref

> **ref** tạo reactive reference cho primitive values.

```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)
const message = ref('Hello')
const isActive = ref(false)

// Access value với .value
console.log(count.value) // 0
count.value++ // Update value
</script>
```

### 3.2 reactive

> **reactive** tạo reactive object.

```vue
<script setup>
import { reactive } from 'vue'

const state = reactive({
  count: 0,
  message: 'Hello',
  user: {
    name: 'John',
    age: 30
  }
})

// Không cần .value
state.count++
state.user.name = 'Jane'
</script>
```

### 3.3 ref vs reactive

| ref | reactive |
|-----|----------|
| Primitive values | Objects |
| Cần `.value` | Không cần `.value` |
| Có thể reassign | Không thể reassign |

**Khuyến nghị:**
> * Dùng `ref` cho hầu hết cases
> * Dùng `reactive` cho objects không thay đổi structure

---

## 4) Computed và Watch

### 4.1 computed

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

const fullName = computed(() => {
  return `${firstName.value} ${lastName.value}`
})
</script>
```

### 4.2 watch

```vue
<script setup>
import { ref, watch } from 'vue'

const count = ref(0)

watch(count, (newValue, oldValue) => {
  console.log(`Count changed from ${oldValue} to ${newValue}`)
})
</script>
```

### 4.3 watchEffect

```vue
<script setup>
import { ref, watchEffect } from 'vue'

const count = ref(0)

watchEffect(() => {
  console.log(`Count is: ${count.value}`)
})
</script>
```

---

## 5) Lifecycle Hooks

### 5.1 Composition API Lifecycle

```vue
<script setup>
import { onMounted, onUpdated, onUnmounted } from 'vue'

onMounted(() => {
  console.log('Mounted')
})

onUpdated(() => {
  console.log('Updated')
})

onUnmounted(() => {
  console.log('Unmounted')
})
</script>
```

**Mapping:**

| Options API | Composition API |
|------------|----------------|
| beforeCreate | setup() |
| created | setup() |
| beforeMount | onBeforeMount |
| mounted | onMounted |
| beforeUpdate | onBeforeUpdate |
| updated | onUpdated |
| beforeUnmount | onBeforeUnmount |
| unmounted | onUnmounted |

---

## 6) Composables

### 6.1 Tạo Composable

> **Composables** là functions có thể tái sử dụng logic.

**composables/useCounter.js**

```javascript
import { ref, computed } from 'vue'

export function useCounter(initialValue = 0) {
  const count = ref(initialValue)
  
  const increment = () => {
    count.value++
  }
  
  const decrement = () => {
    count.value--
  }
  
  const doubleCount = computed(() => count.value * 2)
  
  return {
    count,
    increment,
    decrement,
    doubleCount
  }
}
```

**Sử dụng:**

```vue
<script setup>
import { useCounter } from '@/composables/useCounter'

const { count, increment, decrement, doubleCount } = useCounter(10)
</script>
```

### 6.2 useFetch Composable

**composables/useFetch.js**

```javascript
import { ref } from 'vue'
import axios from 'axios'

export function useFetch(url) {
  const data = ref(null)
  const loading = ref(false)
  const error = ref(null)
  
  const fetchData = async () => {
    loading.value = true
    error.value = null
    try {
      const response = await axios.get(url)
      data.value = response.data
    } catch (err) {
      error.value = err.message
    } finally {
      loading.value = false
    }
  }
  
  fetchData()
  
  return {
    data,
    loading,
    error,
    fetchData
  }
}
```

**Sử dụng:**

```vue
<script setup>
import { useFetch } from '@/composables/useFetch'

const { data, loading, error } = useFetch('/api/users')
</script>
```

---

## 7) Provide và Inject

### 7.1 Provide

```vue
<script setup>
import { provide, ref } from 'vue'

const theme = ref('dark')

provide('theme', theme)
</script>
```

### 7.2 Inject

```vue
<script setup>
import { inject } from 'vue'

const theme = inject('theme', 'light') // default value
</script>
```

---

## 8) Template Refs

### 8.1 Template Refs với Composition API

```vue
<template>
  <input ref="inputRef" type="text" />
</template>

<script setup>
import { ref, onMounted } from 'vue'

const inputRef = ref(null)

onMounted(() => {
  inputRef.value.focus()
})
</script>
```

---

## 9) Tóm tắt

### 9.1 Những gì đã học

> * Composition API là gì
> * Setup function và script setup
> * Reactivity API: ref, reactive
> * Computed và watch
> * Lifecycle hooks
> * Composables
> * Provide và inject
> * Template refs

### 9.2 Best Practices

> * Sử dụng script setup khi có thể
> * Dùng ref cho hầu hết cases
> * Tạo composables cho reusable logic
> * Sử dụng provide/inject cho dependency injection

### 9.3 Bài tập thực hành

1. Convert component từ Options API sang Composition API
2. Tạo useCounter composable
3. Tạo useFetch composable
4. Implement provide/inject cho theme
5. Sử dụng template refs với Composition API
6. Tạo custom composable cho form validation

---

## 10) Tài liệu tham khảo

> * [Vue.js Composition API](https://vuejs.org/guide/extras/composition-api-faq.html)
> * [Composition API Guide](https://vuejs.org/guide/composition-api-introduction.html)
> * [Composables Guide](https://vuejs.org/guide/reusability/composables.html)
