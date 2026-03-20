# Vue.js – Buổi 12: Tối ưu UX và kiểm tra Form

## 1) UX Best Practices

### 1.1 Loading States

> Hiển thị loading state khi đang fetch data hoặc xử lý.

```vue
<template>
  <div>
    <div v-if="loading" class="loading">
      <Spinner />
      <p>Đang tải...</p>
    </div>
    <div v-else-if="error">
      <ErrorMessage :error="error" />
    </div>
    <div v-else>
      <!-- Content -->
    </div>
  </div>
</template>
```

### 1.2 Skeleton Screens

> Hiển thị placeholder content khi đang load.

```vue
<template>
  <div>
    <div v-if="loading" class="skeleton">
      <div class="skeleton-header"></div>
      <div class="skeleton-content"></div>
    </div>
    <div v-else>
      <!-- Real content -->
    </div>
  </div>
</template>

<style>
.skeleton {
  animation: pulse 1.5s ease-in-out infinite;
}

@keyframes pulse {
  0%, 100% {
    opacity: 1;
  }
  50% {
    opacity: 0.5;
  }
}
</style>
```

### 1.3 Error Handling

```vue
<template>
  <div>
    <div v-if="error" class="error-banner">
      <p>{{ error }}</p>
      <button @click="retry">Thử lại</button>
    </div>
  </div>
</template>
```

---

## 2) Form UX Improvements

### 2.1 Real-time Validation

```vue
<template>
  <form @submit.prevent="handleSubmit">
    <div>
      <label>Email:</label>
      <input 
        v-model="form.email"
        @blur="validateEmail"
        :class="{ 'error': errors.email }"
      />
      <span v-if="errors.email" class="error-message">
        {{ errors.email }}
      </span>
    </div>
  </form>
</template>

<script setup>
import { reactive } from 'vue'

const form = reactive({
  email: ''
})

const errors = reactive({})

const validateEmail = () => {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  if (!form.email) {
    errors.email = 'Email là bắt buộc'
  } else if (!emailRegex.test(form.email)) {
    errors.email = 'Email không hợp lệ'
  } else {
    delete errors.email
  }
}

const handleSubmit = () => {
  // Handle submit
}
</script>
```

### 2.2 Form Feedback

```vue
<template>
  <form @submit.prevent="handleSubmit">
    <div v-if="success" class="success-message">
      Form đã được gửi thành công!
    </div>
    
    <button type="submit" :disabled="loading || !isValid">
      <span v-if="loading">Đang gửi...</span>
      <span v-else>Gửi</span>
    </button>
  </form>
</template>
```

### 2.3 Auto-save

```vue
<script setup>
import { reactive, watch } from 'vue'
import api from '@/utils/api'

const form = reactive({
  title: '',
  content: ''
})

let autoSaveTimer = null

const autoSave = () => {
  clearTimeout(autoSaveTimer)
  autoSaveTimer = setTimeout(() => {
    saveDraft()
  }, 1000)
}

const saveDraft = async () => {
  await api.saveDraft(form)
}

watch(() => form.title, () => {
  autoSave()
})

watch(() => form.content, () => {
  autoSave()
})
</script>
```

---

## 3) Form Validation Patterns

### 3.1 Validation Rules

```vue
<script setup>
const rules = {
  required: (value) => !!value || 'Trường này là bắt buộc',
  email: (value) => {
    const pattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
    return pattern.test(value) || 'Email không hợp lệ'
  },
  minLength: (length) => (value) => 
    value.length >= length || `Tối thiểu ${length} ký tự`,
  maxLength: (length) => (value) => 
    value.length <= length || `Tối đa ${length} ký tự`
}
</script>
```

### 3.2 Validation Composable

**composables/useFormValidation.js**

```javascript
import { ref } from 'vue'

export function useFormValidation() {
  const errors = ref({})
  
  const validate = (field, value, rules) => {
    for (const rule of rules) {
      const result = rule(value)
      if (result !== true) {
        errors.value[field] = result
        return false
      }
    }
    delete errors.value[field]
    return true
  }
  
  const validateAll = (fields) => {
    let isValid = true
    for (const [field, { value, rules }] of Object.entries(fields)) {
      if (!validate(field, value, rules)) {
        isValid = false
      }
    }
    return isValid
  }
  
  return {
    errors,
    validate,
    validateAll
  }
}
```

---

## 4) Accessibility (a11y)

### 4.1 ARIA Labels

```vue
<template>
  <div>
    <label for="email">Email:</label>
    <input 
      id="email"
      v-model="email"
      type="email"
      aria-describedby="email-error"
      :aria-invalid="errors.email ? 'true' : 'false'"
    />
    <span id="email-error" role="alert" v-if="errors.email">
      {{ errors.email }}
    </span>
  </div>
</template>
```

### 4.2 Keyboard Navigation

```vue
<template>
  <div>
    <input 
      @keyup.enter="submit"
      @keyup.esc="cancel"
    />
  </div>
</template>
```

---

## 5) Performance Optimization

### 5.1 Debounce Input

```vue
<script setup>
import { ref, watch } from 'vue'

const searchQuery = ref('')
let debounceTimer = null

const performSearch = () => {
  // Search logic
}

watch(searchQuery, () => {
  clearTimeout(debounceTimer)
  debounceTimer = setTimeout(() => {
    performSearch()
  }, 300)
})
</script>
```

### 5.2 Lazy Loading Forms

```vue
<template>
  <div>
    <button @click="showForm = true" v-if="!showForm">
      Hiển thị form
    </button>
    <FormComponent v-if="showForm" />
  </div>
</template>
```

---

## 6) Tóm tắt

### 6.1 Những gì đã học

> * UX best practices: loading states, skeleton screens, error handling
> * Form UX improvements: real-time validation, feedback, auto-save
> * Form validation patterns
> * Accessibility
> * Performance optimization

### 6.2 Best Practices

> * Luôn hiển thị loading states
> * Validate real-time khi có thể
> * Cung cấp feedback rõ ràng
> * Đảm bảo accessibility
> * Optimize performance với debounce

### 6.3 Bài tập thực hành

1. Implement loading states cho form submission
2. Tạo real-time validation cho form
3. Implement auto-save functionality
4. Thêm accessibility features
5. Optimize search với debounce
6. Tạo form validation composable

---

## 7) Tài liệu tham khảo

> * [Vue.js Form Handling](https://vuejs.org/guide/essentials/forms.html)
> * [Web Accessibility Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)
> * [UX Best Practices](https://www.nngroup.com/articles/)
