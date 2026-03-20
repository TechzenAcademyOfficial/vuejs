# Vue.js – Buổi 7: Form và Validation

## 1) Form Handling trong Vue.js

### 1.1 v-model với Form Elements

> Vue.js sử dụng `v-model` để tạo two-way binding với form elements.

**Form cơ bản:**

```vue
<template>
  <form @submit.prevent="handleSubmit">
    <div>
      <label>Username:</label>
      <input v-model="form.username" type="text" />
    </div>
    
    <div>
      <label>Email:</label>
      <input v-model="form.email" type="email" />
    </div>
    
    <div>
      <label>Password:</label>
      <input v-model="form.password" type="password" />
    </div>
    
    <button type="submit">Submit</button>
  </form>
</template>

<script setup>
import { reactive } from 'vue'

const form = reactive({
  username: '',
  email: '',
  password: ''
})

const handleSubmit = () => {
  console.log('Form data:', form)
  // Submit form
}
</script>
```

### 1.2 Các loại Input

#### 1.2.1 Text Inputs

```vue
<input v-model="text" type="text" />
<input v-model="email" type="email" />
<input v-model="password" type="password" />
<input v-model="number" type="number" />
<input v-model="url" type="url" />
<input v-model="tel" type="tel" />
```

#### 1.2.2 Textarea

```vue
<textarea v-model="message"></textarea>
```

#### 1.2.3 Checkbox

```vue
<!-- Single checkbox -->
<input v-model="agree" type="checkbox" />

<!-- Multiple checkboxes -->
<input v-model="selectedItems" type="checkbox" value="item1" />
<input v-model="selectedItems" type="checkbox" value="item2" />
<input v-model="selectedItems" type="checkbox" value="item3" />
```

#### 1.2.4 Radio

```vue
<input v-model="selectedOption" type="radio" value="option1" />
<input v-model="selectedOption" type="radio" value="option2" />
<input v-model="selectedOption" type="radio" value="option3" />
```

#### 1.2.5 Select

```vue
<select v-model="selectedCity">
  <option value="">Chọn thành phố</option>
  <option value="hanoi">Hà Nội</option>
  <option value="hcm">Hồ Chí Minh</option>
  <option value="danang">Đà Nẵng</option>
</select>
```

---

## 2) Form Validation

### 2.1 Validation cơ bản

```vue
<template>
  <form @submit.prevent="handleSubmit">
    <div>
      <label>Email:</label>
      <input 
        v-model="form.email" 
        type="email"
        :class="{ 'error': errors.email }"
      />
      <span v-if="errors.email" class="error-message">
        {{ errors.email }}
      </span>
    </div>
    
    <button type="submit" :disabled="!isValid">Submit</button>
  </form>
</template>

<script setup>
import { reactive, computed, watch } from 'vue'

const form = reactive({
  email: ''
})

const errors = reactive({})

const isValid = computed(() => {
  return form.email && !errors.email
})

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
  validateEmail()
  if (isValid.value) {
    // Submit form
    console.log('Form submitted:', form)
  }
}

watch(() => form.email, () => {
  validateEmail()
})
</script>
```

### 2.2 Validation với VeeValidate (Khuyến nghị)

**Cài đặt:**

```bash
npm install vee-validate yup
```

**Sử dụng:**

```vue
<template>
  <form @submit="onSubmit">
    <Field name="email" rules="required|email" v-slot="{ field, errors }">
      <input v-bind="field" type="email" />
      <span v-if="errors.length">{{ errors[0] }}</span>
    </Field>
    
    <button type="submit">Submit</button>
  </form>
</template>

<script>
import { Form, Field } from 'vee-validate'
import * as yup from 'yup'

const onSubmit = (values) => {
  console.log('Form values:', values)
}
</script>
```
```

---

## 3) Form Modifiers

### 3.1 v-model Modifiers

| Modifier | Mô tả |
|----------|-------|
| `.lazy` | Update khi blur thay vì input |
| `.number` | Chuyển thành số |
| `.trim` | Xóa khoảng trắng đầu/cuối |

**Ví dụ:**

```vue
<input v-model.lazy="message" />
<input v-model.number="age" type="number" />
<input v-model.trim="username" />
```

### 3.2 Event Modifiers

```vue
<form @submit.prevent="handleSubmit">
  <!-- .prevent ngăn default form submit -->
</form>

<input @keyup.enter="handleEnter" />
<!-- Chỉ trigger khi nhấn Enter -->
```

---

## 4) Advanced Form Patterns

### 4.1 Dynamic Form Fields

```vue
<template>
  <form @submit.prevent="handleSubmit">
    <div v-for="(field, index) in formFields" :key="index">
      <label>{{ field.label }}:</label>
      <input 
        v-model="form[field.name]" 
        :type="field.type"
        :placeholder="field.placeholder"
      />
    </div>
    <button type="submit">Submit</button>
  </form>
</template>

<script setup>
import { ref, reactive } from 'vue'

const formFields = ref([
  { name: 'username', label: 'Username', type: 'text', placeholder: 'Enter username' },
  { name: 'email', label: 'Email', type: 'email', placeholder: 'Enter email' },
  { name: 'age', label: 'Age', type: 'number', placeholder: 'Enter age' }
])

const form = reactive({})

const handleSubmit = () => {
  console.log('Form data:', form)
}
</script>
```

### 4.2 Form với File Upload

```vue
<template>
  <form @submit.prevent="handleSubmit">
    <div>
      <label>Upload File:</label>
      <input 
        type="file" 
        @change="handleFileChange"
        ref="fileInput"
      />
      <p v-if="selectedFile">Selected: {{ selectedFile.name }}</p>
    </div>
    <button type="submit">Upload</button>
  </form>
</template>

<script setup>
import { ref } from 'vue'

const selectedFile = ref(null)
const fileInput = ref(null)

const handleFileChange = (event) => {
  selectedFile.value = event.target.files[0]
}

const handleSubmit = async () => {
  if (!selectedFile.value) return
  
  const formData = new FormData()
  formData.append('file', selectedFile.value)
  
  try {
    const response = await fetch('/api/upload', {
      method: 'POST',
      body: formData
    })
    const result = await response.json()
    console.log('Upload success:', result)
  } catch (error) {
    console.error('Upload error:', error)
  }
}
</script>
```

### 4.3 Multi-step Form

```vue
<template>
  <form @submit.prevent="handleSubmit">
    <!-- Step 1 -->
    <div v-show="currentStep === 1">
      <h3>Step 1: Personal Info</h3>
      <input v-model="form.name" placeholder="Name" />
      <input v-model="form.email" placeholder="Email" />
      <button @click.prevent="nextStep">Next</button>
    </div>
    
    <!-- Step 2 -->
    <div v-show="currentStep === 2">
      <h3>Step 2: Address</h3>
      <input v-model="form.address" placeholder="Address" />
      <button @click.prevent="prevStep">Previous</button>
      <button @click.prevent="nextStep">Next</button>
    </div>
    
    <!-- Step 3 -->
    <div v-show="currentStep === 3">
      <h3>Step 3: Review</h3>
      <pre>{{ form }}</pre>
      <button @click.prevent="prevStep">Previous</button>
      <button type="submit">Submit</button>
    </div>
  </form>
</template>

<script setup>
import { ref, reactive } from 'vue'

const currentStep = ref(1)
const form = reactive({
  name: '',
  email: '',
  address: ''
})

const validateStep = () => {
  // Validation logic
  return true
}

const nextStep = () => {
  if (validateStep()) {
    currentStep.value++
  }
}

const prevStep = () => {
  currentStep.value--
}

const handleSubmit = () => {
  console.log('Final form:', form)
}
</script>
```

---

## 5) Form Validation Library - VeeValidate

### 5.1 Setup VeeValidate

```bash
npm install vee-validate@4 yup
```

### 5.2 Basic Usage

```vue
<template>
  <Form @submit="onSubmit" v-slot="{ errors }">
    <Field name="email" rules="required|email" v-slot="{ field, errors }">
      <input v-bind="field" type="email" />
      <span v-if="errors.length" class="error">{{ errors[0] }}</span>
    </Field>
    
    <Field name="password" rules="required|min:8" v-slot="{ field, errors }">
      <input v-bind="field" type="password" />
      <span v-if="errors.length" class="error">{{ errors[0] }}</span>
    </Field>
    
    <button type="submit">Submit</button>
  </Form>
</template>

<script setup>
import { Form, Field } from 'vee-validate'

const onSubmit = (values) => {
  console.log('Form values:', values)
}
</script>
```

### 5.3 Custom Validation với Yup

```vue
<script>
import { Form, Field } from 'vee-validate'
import * as yup from 'yup'

const schema = yup.object({
  email: yup.string().required().email(),
  password: yup.string().required().min(8),
  confirmPassword: yup.string()
    .required()
    .oneOf([yup.ref('password')], 'Passwords must match')
})

const onSubmit = (values) => {
  console.log('Form values:', values)
}
</script>

<template>
  <Form @submit="onSubmit" :validation-schema="schema">
    <!-- Fields -->
  </Form>
</template>
```

---

## 6) Tóm tắt

### 6.1 Những gì đã học

> * Form handling với v-model
> * Các loại input elements
> * Form validation cơ bản và với VeeValidate
> * Form modifiers
> * Advanced patterns: dynamic fields, file upload, multi-step
> * VeeValidate và Yup

### 6.2 Best Practices

> * Luôn validate trên cả client và server
> * Hiển thị lỗi rõ ràng cho người dùng
> * Disable submit button khi form invalid
> * Validate real-time khi có thể
> * Sử dụng validation library cho form phức tạp

### 6.3 Bài tập thực hành

1. Tạo form đăng ký với validation
2. Tạo form đăng nhập với error handling
3. Tạo multi-step form (3 steps)
4. Tạo form với file upload
5. Implement real-time validation
6. Sử dụng VeeValidate cho form phức tạp

---

## 7) Tài liệu tham khảo

> * [Vue.js Form Input Binding](https://vuejs.org/guide/essentials/forms.html)
> * [VeeValidate Documentation](https://vee-validate.logaretm.com/)
> * [Yup Validation](https://github.com/jquense/yup)
