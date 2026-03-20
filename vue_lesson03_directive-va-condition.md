# Vue.js – Buổi 3: Directive & Condition

## 1) Vue Directives tổng quan

### 1.1 Directive là gì?

> **Directive** là các thuộc tính đặc biệt với tiền tố `v-` để áp dụng hành vi reactive lên DOM elements.

**Cấu trúc:**
```vue
<element v-directive:argument.modifier="value">
```

**Ví dụ:**
```vue
<div v-if="isVisible" v-show="isActive" @click="handleClick">
```

### 1.2 Các loại Directive

| Directive | Mô tả | Ví dụ |
|-----------|-------|-------|
| **v-if** | Conditional rendering | `v-if="isVisible"` |
| **v-show** | Toggle visibility | `v-show="isActive"` |
| **v-for** | List rendering | `v-for="item in items"` |
| **v-bind** | Attribute binding | `v-bind:href` hoặc `:href` |
| **v-model** | Two-way binding | `v-model="input"` |
| **v-on** | Event handling | `v-on:click` hoặc `@click` |
| **v-text** | Text content | `v-text="message"` |
| **v-html** | Raw HTML | `v-html="htmlContent"` |
| **v-pre** | Skip compilation | `v-pre` |
| **v-cloak** | Hide until compiled | `v-cloak` |
| **v-once** | Render once | `v-once` |

---

## 2) Conditional Directives chi tiết

### 2.1 v-if Directive

> **v-if** thực sự thêm/xóa element khỏi DOM dựa trên điều kiện.

```vue
<template>
  <div>
    <h1 v-if="isLoggedIn">Xin chào, {{ username }}!</h1>
    <h1 v-else>Vui lòng đăng nhập</h1>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const isLoggedIn = ref(false)
const username = ref('Nguyễn Văn A')
</script>
```

**Đặc điểm:**
> * Element được tạo/xóa khỏi DOM
> * Tốt cho điều kiện ít thay đổi
> * Có thể dùng với `v-else` và `v-else-if`

### 2.2 v-else và v-else-if

```vue
<template>
  <div>
    <p v-if="score >= 90">Xuất sắc!</p>
    <p v-else-if="score >= 80">Giỏi</p>
    <p v-else-if="score >= 70">Khá</p>
    <p v-else-if="score >= 50">Trung bình</p>
    <p v-else>Cần cố gắng</p>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const score = ref(85)
</script>
```

**Lưu ý:**
> `v-else` và `v-else-if` phải ngay sau element có `v-if` hoặc `v-else-if`

### 2.3 v-show Directive

> **v-show** chỉ toggle CSS `display` property, không xóa element khỏi DOM.

```vue
<template>
  <div>
    <div v-show="isVisible">
      Nội dung được ẩn/hiện
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const isVisible = ref(true)
</script>
```

**Kết quả:**
> Khi `isVisible = false`: `<div style="display: none;">`

### 2.4 So sánh v-if vs v-show

| Tiêu chí | v-if | v-show |
|----------|------|--------|
| **DOM** | Thêm/xóa element | Chỉ toggle CSS |
| **Performance** | Tốt khi ít toggle | Tốt khi toggle thường xuyên |
| **Initial render** | Lazy - chỉ render khi true | Render ngay, chỉ ẩn |
| **Use case** | Điều kiện ít thay đổi | Toggle thường xuyên (tabs, modals) |

**Khuyến nghị:**
> * Dùng **v-if** khi điều kiện ít thay đổi hoặc cần lazy loading
> * Dùng **v-show** khi cần toggle thường xuyên (tabs, dropdowns, modals)

---

## 3) List Rendering với v-for

### 3.1 v-for cơ bản

```vue
<template>
  <ul>
    <li v-for="item in items" :key="item.id">
      {{ item.name }}
    </li>
  </ul>
</template>

<script setup>
import { ref } from 'vue'

const items = ref([
  { id: 1, name: 'Sản phẩm 1' },
  { id: 2, name: 'Sản phẩm 2' },
  { id: 3, name: 'Sản phẩm 3' }
])
</script>
```

### 3.2 v-for với Index

```vue
<template>
  <ul>
    <li v-for="(item, index) in items" :key="item.id">
      {{ index + 1 }}. {{ item.name }}
    </li>
  </ul>
</template>
```

### 3.3 v-for với Object

```vue
<template>
  <ul>
    <li v-for="(value, key, index) in userInfo" :key="key">
      {{ index + 1 }}. {{ key }}: {{ value }}
    </li>
  </ul>
</template>

<script setup>
import { reactive } from 'vue'

const userInfo = reactive({
  name: 'Nguyễn Văn A',
  age: 25,
  email: 'nguyenvana@example.com'
})
</script>
```

### 3.4 v-for với Range

```vue
<template>
  <span v-for="n in 10" :key="n">{{ n }}</span>
</template>
```

**Kết quả:** `1 2 3 4 5 6 7 8 9 10`

### 3.5 v-for với v-if

**Không nên:**

```vue
<!-- ❌ Không nên -->
<li v-for="item in items" v-if="item.isActive" :key="item.id">
```

**Nên:**

```vue
<!-- ✅ Nên -->
<template v-for="item in items" :key="item.id">
  <li v-if="item.isActive">
    {{ item.name }}
  </li>
</template>
```

**Hoặc dùng computed:**

```vue
<template>
  <li v-for="item in activeItems" :key="item.id">
    {{ item.name }}
  </li>
</template>

<script setup>
import { ref, computed } from 'vue'

const items = ref([
  { id: 1, name: 'Sản phẩm 1', isActive: true },
  { id: 2, name: 'Sản phẩm 2', isActive: false },
  { id: 3, name: 'Sản phẩm 3', isActive: true }
])

const activeItems = computed(() => {
  return items.value.filter(item => item.isActive)
})
</script>
```

---

## 4) Key Attribute trong v-for

### 4.1 Tại sao cần :key?

> **:key** giúp Vue theo dõi identity của mỗi node để tối ưu re-render.

**Không có key:**
```vue
<li v-for="item in items">{{ item.name }}</li>
```

**Có key:**
```vue
<li v-for="item in items" :key="item.id">{{ item.name }}</li>
```

### 4.2 Best Practices cho Key

> * Luôn dùng **unique** và **stable** value cho key
> * Không dùng `index` làm key khi list có thể thay đổi thứ tự
> * Dùng `id` từ database hoặc unique identifier

**Ví dụ:**

```vue
<!-- ✅ Tốt -->
<li v-for="user in users" :key="user.id">

<!-- ❌ Không tốt khi list thay đổi -->
<li v-for="(user, index) in users" :key="index">
```

---

## 5) Các Directive khác

### 5.1 v-text

> Hiển thị text content (tương tự `{{ }}`)

```vue
<span v-text="message"></span>
<!-- Tương đương -->
<span>{{ message }}</span>
```

### 5.2 v-html

> Hiển thị raw HTML

```vue
<div v-html="htmlContent"></div>
```

**Cảnh báo:**
> Chỉ dùng với dữ liệu tin cậy để tránh XSS attacks

### 5.3 v-pre

> Bỏ qua compilation cho element này

```vue
<span v-pre>{{ Đây sẽ không được compile }}</span>
```

**Kết quả:** `{{ Đây sẽ không được compile }}`

### 5.4 v-cloak

> Ẩn element cho đến khi Vue instance được mount

```vue
<style>
[v-cloak] {
  display: none;
}
</style>

<div v-cloak>
  {{ message }}
</div>
```

**Use case:**
> Tránh flash của un-compiled template khi load page

### 5.5 v-once

> Render element chỉ một lần, không update khi data thay đổi

```vue
<span v-once>{{ message }}</span>
```

**Use case:**
> Tối ưu performance cho static content

---

## 6) Custom Directives

### 6.1 Tạo Custom Directive

```vue
<script setup>
const vFocus = {
  mounted(el) {
    el.focus()
  }
}
</script>

<template>
  <input v-focus>
</template>
```

### 6.2 Directive Hooks

| Hook | Mô tả |
|------|-------|
| `created` | Trước khi bind attributes |
| `beforeMount` | Trước khi mount vào DOM |
| `mounted` | Sau khi mount vào DOM |
| `beforeUpdate` | Trước khi update |
| `updated` | Sau khi update |
| `beforeUnmount` | Trước khi unmount |
| `unmounted` | Sau khi unmount |

**Ví dụ:**

```vue
<script setup>
const vHighlight = {
  mounted(el, binding) {
    el.style.backgroundColor = binding.value
  },
  updated(el, binding) {
    el.style.backgroundColor = binding.value
  }
}
</script>

<template>
  <div v-highlight="'yellow'">Highlighted text</div>
</template>
```

### 6.3 Directive Arguments và Modifiers

```vue
<script setup>
const vPin = {
  mounted(el, binding) {
    el.style.position = 'fixed'
    const s = binding.arg || 'top'
    el.style[s] = binding.value + 'px'
  }
}
</script>

<template>
  <div v-pin:bottom="200">Pinned 200px from bottom</div>
</template>
```

---

## 7) Conditional Rendering Patterns

### 7.1 Template với v-if

```vue
<template v-if="isLoggedIn">
  <h1>Welcome</h1>
  <p>You are logged in</p>
</template>
```

### 7.2 Multiple Conditions

```vue
<template>
  <div>
    <template v-if="userType === 'admin'">
      <AdminPanel />
    </template>
    <template v-else-if="userType === 'user'">
      <UserPanel />
    </template>
    <template v-else>
      <GuestPanel />
    </template>
  </div>
</template>
```

### 7.3 Conditional với Computed

```vue
<script setup>
import { ref, computed } from 'vue'

const isLoggedIn = ref(false)
const username = ref('Nguyễn Văn A')

const displayMessage = computed(() => {
  if (isLoggedIn.value) {
    return `Welcome, ${username.value}!`
  }
  return 'Please log in'
})
</script>

<template>
  <p>{{ displayMessage }}</p>
</template>
```

---

## 8) Tóm tắt

### 8.1 Những gì đã học

> * Vue directives tổng quan
> * Conditional rendering: `v-if`, `v-else`, `v-else-if`, `v-show`
> * List rendering: `v-for` với array, object, range
> * Key attribute và best practices
> * Các directive khác: `v-text`, `v-html`, `v-pre`, `v-cloak`, `v-once`
> * Custom directives

### 8.2 Best Practices

> * Dùng `v-if` cho conditional render, `v-show` cho toggle
> * Luôn dùng `:key` với `v-for` và dùng unique identifier
> * Không dùng `v-if` và `v-for` trên cùng element
> * Dùng computed để filter list thay vì `v-if` trong `v-for`
> * Dùng `v-cloak` để tránh flash của un-compiled template

### 8.3 Bài tập thực hành

1. Tạo component hiển thị danh sách sản phẩm với `v-for`
2. Thêm chức năng filter sản phẩm theo category
3. Sử dụng `v-if` để hiển thị message khi không có sản phẩm
4. Tạo custom directive để auto-focus input
5. Sử dụng `v-show` để toggle hiển thị chi tiết sản phẩm
6. Thêm pagination với conditional rendering

---

## 9) Tài liệu tham khảo

> * [Vue.js Conditional Rendering](https://vuejs.org/guide/essentials/conditional.html)
> * [Vue.js List Rendering](https://vuejs.org/guide/essentials/list.html)
> * [Vue.js Custom Directives](https://vuejs.org/guide/reusability/custom-directives.html)
