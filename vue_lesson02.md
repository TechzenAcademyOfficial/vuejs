# Vue.js – Buổi 2: Templating & Binding

## 1) Template Syntax trong Vue.js

### 1.1 Text Interpolation

> **Text Interpolation** là cách đơn giản để hiển thị dữ liệu từ component data vào template.

**Cú pháp:**
```vue
<template>
  <div>
    <h1>{{ title }}</h1>
    <p>{{ description }}</p>
    <span>Giá: {{ price }} VND</span>
  </div>
</template>
```

**Lưu ý:**
> * Sử dụng cặp dấu ngoặc kép `{{ }}` để hiển thị giá trị
> * Vue tự động cập nhật khi dữ liệu thay đổi
> * Chỉ hiển thị giá trị, không thực thiện JavaScript code

**Ví dụ:**

```vue
<script setup>
import { ref } from 'vue'

const title = ref('Vue.js Tutorial')
const description = ref('Học Vue.js từ cơ bản')
const price = ref(29.99)
</script>
```

### 1.2 Raw HTML

> Khi cần hiển thị HTML thô (không escape), sử dụng `v-html`.

```vue
<template>
  <div v-html="htmlContent"></div>
</template>

<script setup>
import { ref } from 'vue'

const htmlContent = ref('<strong>Văn bản!</strong> <em>Quan trọng</em>')
</script>
```

**Cảnh báo:**
> Luôn luôn sử dụng `v-html` với dữ liệu từ người dùng để tránh XSS attacks.
> Chỉ dùng với dữ liệu tin cậy từ server hoặc nguồn đáng tin cậy.

### 1.3 Attributes Binding

> Sử dụng `v-bind` để bind giá trị vào thuộc tính HTML.

```vue
<template>
  <a v-bind:href="url">Liên kết</a>
  <img v-bind:src="imageUrl" alt="Logo">
  <button v-bind:disabled="isDisabled">Gửi</button>
</template>

<script setup>
import { ref } from 'vue'

const url = ref('https://vuejs.org')
const imageUrl = ref('/logo.png')
const isDisabled = ref(false)
</script>
```

**Shorthand:**
> Có thể viết `v-bind:href` thành `:href`
> Có thể viết `v-bind:src` thành `:src`

---

## 2) Two-Way Data Binding

### 2.1 v-model Directive

> **v-model** tạo two-way binding giữa input và data.

```vue
<template>
  <div>
    <input v-model="username" type="text" placeholder="Nhập tên đăng nhập">
    <p>Tên đăng nhập: {{ username }}</p>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const username = ref('')
</script>
```

**Cách hoạt động:**
> Khi người dùng nhập vào input → `username` cập nhật
> Khi `username` thay đổi trong code → input cập nhật
> Hai chiều tự động đồng bộ

### 2.2 v-model với các loại Input

#### 2.2.1 Text Input

```vue
<input v-model="text" type="text">
```

#### 2.2.2 Textarea

```vue
<textarea v-model="message"></textarea>
```

#### 2.2.3 Checkbox

```vue
<input v-model="isAgreed" type="checkbox">
```

**Lưu ý:**
> Với checkbox, `v-model` bind với `boolean` value

#### 2.2.4 Radio Buttons

```vue
<input v-model="selectedOption" type="radio" value="option1">
<input v-model="selectedOption" type="radio" value="option2">
```

**Lưu ý:**
> Tất cả radio cùng `v-model` phải có cùng `name` attribute

#### 2.2.5 Select

```vue
<select v-model="selectedCity">
  <option value="">Chọn thành phố</option>
  <option value="hanoi">Hà Nội</option>
  <option value="hochiminh">Hồ Chí Minh</option>
</select>
```

### 2.3 v-model Modifiers

> **Modifiers** là các từ khóa để chỉnh sửa hành vi của `v-model`.

| Modifier | Mô tả | Ví dụ |
|--------|-------|-------|
| `.number` | Chuyển thành số | `v-model.number="age"` |
| `.trim` | Xóa khoảng trắng đầu/cuối | `v-model.trim="text"` |
| `.lazy` | Cập nhật khi blur thay vì input | `v-model.lazy="email"` |

**Ví dụ:**

```vue
<input v-model.number="price" type="number">
<input v-model.trim="username" type="text">
<input v-model.lazy="email" type="email">
```

---

## 3) JavaScript Expressions trong Template

### 3.1 Expressions trong {{ }}

> Có thể sử dụng JavaScript expressions trong interpolation.

```vue
<template>
  <div>
    <p>{{ message.toUpperCase() }}</p>
    <p>{{ count + 1 }}</p>
    <p>{{ isActive ? 'Hoạt động' : 'Không hoạt động' }}</p>
    <p>{{ items.length }} sản phẩm</p>
  </div>
</template>
```

**Hạn chế:**
> Chỉ dùng **single expressions**, không phức tạp statements
> Không thể gọi methods trực tiếp trong template (dùng computed hoặc methods)

### 3.2 Computed Properties

> **Computed** là các thuộc tính được tính toán tự động dựa trên data.

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('Nguyễn')
const lastName = ref('Văn A')

const fullName = computed(() => {
  return `${firstName.value} ${lastName.value}`
})
</script>
```

**Sử dụng:**

```vue
<template>
  <div>
    <h2>{{ fullName }}</h2>
  </div>
</template>
```

**Đặc điểm:**
> Computed được **cache** - chỉ tính lại khi dependencies thay đổi
> Tốt hơn methods cho hiển thị dữ liệu được tính toán

### 3.3 Methods trong Template

> Có thể gọi methods từ template.

```vue
<template>
  <div>
    <button @click="formatPrice(price)">Định dạng giá</button>
    <p>{{ getFullName() }}</p>
  </div>
</template>
```

<script setup>
import { ref } from 'vue'

const price = ref(1000000)
const firstName = ref('Nguyễn')
const lastName = ref('Văn A')

const formatPrice = (value) => {
  return new Intl.NumberFormat('vi-VN').format(value)
}

const getFullName = () => {
  return `${firstName.value} ${lastName.value}`
}
</script>
```

---

## 4) Class và Style Binding

### 4.1 Class Binding động

> Sử dụng `:class` để bind class động.

```vue
<template>
  <div :class="{ active: isActive, error: hasError }">
    Nội dung
  </div>
</template>

<script setup>
import { ref } from 'vue'

const isActive = ref(true)
const hasError = ref(false)
</script>
```

**Kết quả:**
> `<div class="active">` khi `isActive` là `true`

### 4.2 Class Binding với Array

```vue
<template>
  <div :class="[activeClass, errorClass]">
    Nội dung
  </div>
</template>
```

### 4.3 Class Binding với Object

```vue
<template>
  <div :class="{ 'text-primary': isPrimary, 'bg-success': isSuccess }">
    Nội dung
  </div>
</template>
```

### 4.4 Inline Style Binding

> Sử dụng `:style` để bind style động.

```vue
<template>
  <div :style="{ color: textColor, fontSize: fontSize + 'px' }">
    Nội dung
  </div>
</template>

<script setup>
import { ref } from 'vue'

const textColor = ref('#42b983')
const fontSize = ref(16)
</script>
```

**Shorthand:**
```vue
<div :style="`color: ${textColor}; font-size: ${fontSize}px`">
```

---

## 5) Conditional Rendering

### 5.1 v-if Directive

> **v-if** hiển thị element khi điều kiện là `true`.

```vue
<template>
  <div>
    <p v-if="isLoggedIn">Xin chào, {{ username }}!</p>
    <p v-else>Vui lòng đăng nhập</p>
  </div>
</template>
```

**v-if vs v-show:**
> * **v-if**: Thực sự thêm/xóa element khỏi DOM (tốt hơn cho điều kiện thay đổi thường xuyên)
> * **v-show**: Chỉ ẩn/hiện element (dùng CSS `display: none`) (tốt hơn cho toggle thường xuyên)

### 5.2 v-else và v-else-if

```vue
<template>
  <div>
    <p v-if="score >= 90">Xuất sắc!</p>
    <p v-else-if="score >= 70">Khá</p>
    <p v-else>Cần cố gắng</p>
  </div>
</template>
```

### 5.3 v-show Directive

```vue
<template>
  <div v-show="isVisible">
    Nội dung được ẩn/hiện
  </div>
</template>
```

---

## 6) List Rendering

### 6.1 v-for Directive

> **v-for** lặp qua array hoặc object để render list.

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

**Quan trọng:**
> Luôn luôn cung cấp `:key` để Vue tối ưu render performance

### 6.2 v-for với Index

```vue
<template>
  <li v-for="(item, index) in items" :key="index">
    {{ index + 1 }}. {{ item.name }}
  </li>
</template>
```

### 6.3 v-for với Object

```vue
<template>
  <li v-for="(value, key) in userInfo" :key="key">
    {{ key }}: {{ value }}
  </li>
</template>
```

### 6.4 Nested v-for

```vue
<template>
  <div v-for="category in categories" :key="category.id">
    <h3>{{ category.name }}</h3>
    <ul>
      <li v-for="product in category.products" :key="product.id">
        {{ product.name }}
      </li>
    </ul>
  </div>
</template>
```

---

## 7) Event Handling

### 7.1 v-on Directive

> **v-on** hoặc `@` để lắng nghe events.

```vue
<template>
  <div>
    <button @click="handleClick">Click</button>
    <button v-on:click="handleClick">Click</button>
    <input @input="handleInput" type="text">
  </div>
</template>

<script setup>
const handleClick = () => {
  alert('Button được click!')
}

const handleInput = (event) => {
  console.log(event.target.value)
}
</script>
```

### 7.2 Event Modifiers

| Modifier | Mô tả |
|--------|-------|
| `.stop` | Ngừng chuyển event |
| `.prevent` | Ngừng default hành vi |
| `.capture` | Capture phase thay vì bubble |
| `.self` | Chỉ trigger khi click chính element |
| `.nce` | Chỉ trigger một lần |

**Ví dụ:**

```vue
<template>
  <div @click.stop="handleClick">
    <button @click="handleClick">Click</button>
  </div>

  <form @submit.prevent="handleSubmit">
    <!-- Form sẽ không reload page -->
  </form>

  <a @click.prevent="handleLink" href="#">
    <!-- Link sẽ không navigate -->
  </a>
</template>
```

### 7.3 Event Object Syntax

```vue
<template>
  <button v-on="{ click: handleClick, mouseenter: handleMouseEnter }">
    Nhiều events
  </button>
</template>
```

### 7.4 Inline Handlers

```vue
<template>
  <button @click="count++">Tăng: {{ count }}</button>
  <button @click="message = 'New message'">Đổi message</button>
</template>
```

---

## 8) Tóm tắt

### 8.1 Những gì đã học

> * Template syntax: interpolation, attributes binding
> * Two-way binding với `v-model`
> * JavaScript expressions trong template
> * Computed properties
> * Class và style binding
> * Conditional rendering (`v-if`, `v-show`)
> * List rendering (`v-for`)
> * Event handling (`v-on`, `@`)

### 8.2 Best Practices

> * Luôn dùng `:key` với `v-for`
> * Sử dụng `computed` thay vì complex expressions trong template
> * Dùng `v-show` cho toggle thường xuyên, `v-if` cho conditional render
> * Prevent default hành vi với `.prevent` modifier
> * Validate và sanitize input với `v-model`

### 8.3 Bài tập thực hành

1. Tạo form đăng ký với các trường: username, email, password
2. Sử dụng `v-model` để bind dữ liệu
3. Thêm validation hiển thị lỗi khi thiếu dữ liệu
4. Tạo list sản phẩm với `v-for`
5. Thêm chức năng tìm kiếm và lọc sản phẩm
6. Sử dụng computed properties để tính toán giá trị tổng hợp

---

## 9) Tài liệu tham khảo

> * [Vue.js Template Syntax](https://vuejs.org/guide/essentials/template-syntax.html)
> * [Vue.js Reactivity](https://vuejs.org/guide/essentials/reactivity-fundamentals.html)
> * [Vue.js Event Handling](https://vuejs.org/guide/essentials/event-handling.html)
