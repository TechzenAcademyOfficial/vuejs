# Vue.js – Buổi 4: Component cơ bản

## 1) Component là gì?

### 1.1 Khái niệm Component

> **Component** là các đơn vị có thể tái sử dụng để xây dựng giao diện người dùng. Mỗi component là một instance Vue độc lập với data, methods, và template riêng.

**Lợi ích:**
> * **Tái sử dụng**: Viết một lần, dùng nhiều nơi
> * **Tổ chức code**: Chia nhỏ ứng dụng thành các phần dễ quản lý
> * **Bảo trì**: Dễ sửa đổi và test từng component riêng biệt
> * **Tái sử dụng logic**: Chia sẻ logic giữa các component

### 1.2 Component Structure

```vue
<template>
  <!-- HTML Template -->
</template>

<script setup>
import { ref, computed } from 'vue'

// Component data với ref
const data = ref(null)

// Computed properties
const computedValue = computed(() => {
  // Computed logic
  return data.value
})

// Component methods
const methodName = () => {
  // Method logic
}
</script>

<style scoped>
/* Component styles */
</style>
```

---

## 2) Tạo và sử dụng Component

### 2.1 Tạo Component đơn giản

**components/Button.vue**

```vue
<template>
  <button @click="handleClick" :class="buttonClass">
    {{ text }}
  </button>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  text: {
    type: String,
    required: true
  },
  variant: {
    type: String,
    default: 'primary'
  }
})

const emit = defineEmits(['click'])

const buttonClass = computed(() => {
  return `btn btn-${props.variant}`
})

const handleClick = () => {
  emit('click')
}
</script>

<style scoped>
.btn {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.btn-primary {
  background-color: #42b983;
  color: white;
}

.btn-secondary {
  background-color: #6c757d;
  color: white;
}
</style>
```

### 2.2 Sử dụng Component

**App.vue**

```vue
<template>
  <div id="app">
    <Button text="Click me" variant="primary" @click="handleButtonClick" />
    <Button text="Cancel" variant="secondary" @click="handleCancel" />
  </div>
</template>

<script setup>
import Button from './components/Button.vue'

const handleButtonClick = () => {
  console.log('Button clicked!')
}

const handleCancel = () => {
  console.log('Cancel clicked!')
}
</script>
```

### 2.3 Global vs Local Registration

**Global Registration:**

```javascript
// main.js
import { createApp } from 'vue'
import App from './App.vue'
import Button from './components/Button.vue'

const app = createApp(App)
app.component('Button', Button)
app.mount('#app')
```

**Local Registration (Khuyến nghị):**

```vue
<script>
import Button from './components/Button.vue'

export default {
  components: {
    Button
  }
}
</script>
```

---

## 3) Props - Truyền dữ liệu từ Parent xuống Child

### 3.1 Props cơ bản

> **Props** là cách truyền dữ liệu từ component cha xuống component con.

**components/UserCard.vue**

```vue
<template>
  <div class="user-card">
    <h3>{{ name }}</h3>
    <p>Email: {{ email }}</p>
    <p>Tuổi: {{ age }}</p>
  </div>
</template>

<script setup>
defineProps({
  name: String,
  email: String,
  age: Number
})
</script>
```

**Sử dụng:**

```vue
<template>
  <UserCard name="Nguyễn Văn A" email="nguyenvana@example.com" :age="25" />
</template>
```

### 3.2 Props với Validation

```vue
<script setup>
defineProps({
  name: {
    type: String,
    required: true
  },
  age: {
    type: Number,
    default: 0,
    validator(value) {
      return value >= 0 && value <= 150
    }
  },
  email: {
    type: String,
    required: true,
    validator(value) {
      return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)
    }
  }
})
</script>
```

### 3.3 Props với Object và Array

```vue
<script setup>
defineProps({
  user: {
    type: Object,
    required: true,
    default() {
      return {
        name: '',
        email: ''
      }
    }
  },
  items: {
    type: Array,
    default() {
      return []
    }
  }
})
</script>
```

**Lưu ý:**
> Default cho Object và Array phải là function trả về giá trị

### 3.4 One-Way Data Flow

> Props chỉ truyền từ parent xuống child, không thể thay đổi từ child.

**Không nên:**

```vue
<!-- ❌ Không nên thay đổi props trực tiếp -->
<script setup>
const props = defineProps(['count'])

const increment = () => {
  props.count++ // ❌ Lỗi! Props là readonly
}
</script>
```

**Nên:**

```vue
<!-- ✅ Dùng local data hoặc emit event -->
<script setup>
import { ref } from 'vue'

const props = defineProps(['initialCount'])
const emit = defineEmits(['update:count'])

const localCount = ref(props.initialCount)

const increment = () => {
  localCount.value++
  emit('update:count', localCount.value)
}
</script>
```

---

## 4) Events - Truyền dữ liệu từ Child lên Parent

### 4.1 $emit cơ bản

> **$emit** để gửi event từ component con lên component cha.

**components/Counter.vue**

```vue
<template>
  <div>
    <button @click="decrement">-</button>
    <span>{{ count }}</span>
    <button @click="increment">+</button>
  </div>
</template>

<script setup>
defineProps({
  count: {
    type: Number,
    default: 0
  }
})

const emit = defineEmits(['increment', 'decrement'])

const increment = () => {
  emit('increment')
}

const decrement = () => {
  emit('decrement')
}
</script>
```

**Sử dụng:**

```vue
<template>
  <Counter :count="count" @increment="count++" @decrement="count--" />
</template>
```

### 4.2 Emit với Data

```vue
<script setup>
import { ref } from 'vue'

const username = ref('')
const email = ref('')

const emit = defineEmits(['submit'])

const handleSubmit = () => {
  const formData = {
    username: username.value,
    email: email.value
  }
  emit('submit', formData)
}
</script>
```

**Parent:**

```vue
<template>
  <FormComponent @submit="handleFormSubmit" />
</template>

<script setup>
const handleFormSubmit = (formData) => {
  console.log('Form data:', formData)
}
</script>
```

### 4.3 v-model với Component

> Tạo two-way binding với component bằng `v-model`.

**components/Input.vue**

```vue
<template>
  <input :value="modelValue" @input="handleInput" />
</template>

<script setup>
const props = defineProps({
  modelValue: {
    type: String,
    default: ''
  }
})

const emit = defineEmits(['update:modelValue'])

const handleInput = (event) => {
  emit('update:modelValue', event.target.value)
}
</script>
```

**Sử dụng:**

```vue
<template>
  <Input v-model="username" />
</template>
```

---

## 5) Component Communication Patterns

### 5.1 Parent → Child (Props)

```vue
<!-- Parent -->
<template>
  <ChildComponent :message="parentMessage" />
</template>
```

### 5.2 Child → Parent (Events)

```vue
<!-- Child -->
<button @click="$emit('custom-event', data)">Click</button>

<!-- Parent -->
<ChildComponent @custom-event="handleEvent" />
```

### 5.3 Sibling Components (Event Bus hoặc State Management)

> Sử dụng Vuex/Pinia hoặc Event Bus cho communication giữa siblings

---

## 6) Scoped Styles

### 6.1 Scoped CSS

> **scoped** attribute giới hạn CSS chỉ áp dụng cho component hiện tại.

```vue
<style scoped>
.button {
  padding: 10px;
}
</style>
```

**Kết quả:**
> CSS chỉ áp dụng cho elements trong component này

### 6.2 Deep Selectors

> Sử dụng `:deep()` để style child components.

```vue
<style scoped>
:deep(.child-component) {
  color: red;
}
</style>
```

---

## 7) Component Lifecycle

### 7.1 Lifecycle Hooks

> Component có các lifecycle hooks để thực thi code tại các thời điểm cụ thể.

```vue
<script setup>
import { onBeforeMount, onMounted, onBeforeUpdate, onUpdated, onBeforeUnmount, onUnmounted } from 'vue'

// Composition API không có beforeCreate và created
// Setup function chạy tương đương created

onBeforeMount(() => {
  // Trước khi component được mount vào DOM
})

onMounted(() => {
  // Sau khi component được mount vào DOM
})

onBeforeUpdate(() => {
  // Trước khi component được update
})

onUpdated(() => {
  // Sau khi component được update
})

onBeforeUnmount(() => {
  // Trước khi component được unmount
})

onUnmounted(() => {
  // Sau khi component được unmount
})
</script>
```

**Sử dụng phổ biến:**

```vue
<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'

const users = ref([])
const input = ref(null)
let timer = null

// Fetch data khi component được tạo (setup function)
const response = await fetch('/api/users')
users.value = await response.json()

onMounted(() => {
  // Truy cập DOM sau khi mount
  input.value?.focus()
})

onBeforeUnmount(() => {
  // Cleanup trước khi unmount
  if (timer) {
    clearInterval(timer)
  }
})
</script>
```

---

## 8) Refs - Truy cập DOM và Child Components

### 8.1 Template Refs

```vue
<template>
  <input ref="input" type="text" />
</template>

<script setup>
import { ref, onMounted } from 'vue'

const input = ref(null)

onMounted(() => {
  input.value?.focus()
})
</script>
```

### 8.2 Component Refs

```vue
<template>
  <ChildComponent ref="child" />
</template>

<script setup>
import { ref, onMounted } from 'vue'
import ChildComponent from './ChildComponent.vue'

const child = ref(null)

onMounted(() => {
  // Gọi method của child component
  child.value?.someMethod()
})
</script>
```

---

## 9) Dynamic Components

### 9.1 component :is

> Sử dụng `:is` để render component động.

```vue
<template>
  <component :is="currentComponent" />
</template>

<script setup>
import { ref } from 'vue'
import ComponentA from './ComponentA.vue'
import ComponentB from './ComponentB.vue'

const currentComponent = ref(ComponentA)
</script>
```

### 9.2 keep-alive

> Giữ component trong memory để tránh re-render.

```vue
<template>
  <keep-alive>
    <component :is="currentComponent" />
  </keep-alive>
</template>
```

---

## 10) Tóm tắt

### 10.1 Những gì đã học

> * Component là gì và tại sao sử dụng
> * Tạo và sử dụng component
> * Props: truyền dữ liệu từ parent xuống child
> * Events: truyền dữ liệu từ child lên parent
> * Component communication patterns
> * Scoped styles
> * Component lifecycle hooks
> * Refs để truy cập DOM và child components
> * Dynamic components

### 10.2 Best Practices

> * Đặt tên component với PascalCase
> * Validate props với type và validator
> * Không mutate props trực tiếp
> * Sử dụng scoped styles để tránh CSS conflicts
> * Cleanup trong beforeUnmount
> * Dùng computed cho derived data từ props

### 10.3 Bài tập thực hành

1. Tạo component `ProductCard` hiển thị thông tin sản phẩm
2. Tạo component `ProductList` sử dụng `ProductCard` với v-for
3. Tạo component `SearchBar` emit event khi user search
4. Tạo component `Modal` có thể toggle hiển thị
5. Sử dụng lifecycle hooks để fetch data khi component mount
6. Tạo form component với validation và emit data lên parent

---

## 11) Tài liệu tham khảo

> * [Vue.js Components Basics](https://vuejs.org/guide/essentials/component-basics.html)
> * [Vue.js Props](https://vuejs.org/guide/components/props.html)
> * [Vue.js Events](https://vuejs.org/guide/components/events.html)
> * [Vue.js Component Lifecycle](https://vuejs.org/guide/essentials/lifecycle.html)
