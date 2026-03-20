# Vue.js – Buổi 8: Kết nối API & Axios

## 1) HTTP Requests trong Vue.js

### 1.1 Fetch API

> **Fetch API** là native browser API để thực hiện HTTP requests.

**Ví dụ cơ bản:**

```vue
<script setup>
import { ref } from 'vue'

const users = ref([])
const loading = ref(false)
const error = ref(null)

const fetchUsers = async () => {
  loading.value = true
  try {
    const response = await fetch('/api/users')
    if (!response.ok) {
      throw new Error('Failed to fetch users')
    }
    users.value = await response.json()
  } catch (err) {
    error.value = err.message
  } finally {
    loading.value = false
  }
}

// Fetch data khi component được tạo
await fetchUsers()
</script>
```

### 1.2 POST Request với Fetch

```javascript
async createUser(userData) {
  try {
    const response = await fetch('/api/users', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(userData)
    })
    
    if (!response.ok) {
      throw new Error('Failed to create user')
    }
    
    const newUser = await response.json()
    return newUser
  } catch (error) {
    console.error('Error:', error)
    throw error
  }
}
```

---

## 2) Axios - HTTP Client Library

### 2.1 Tại sao dùng Axios?

> **Axios** là HTTP client library phổ biến với nhiều tính năng hơn Fetch API.

**Ưu điểm:**
> * Interceptors cho requests và responses
> * Automatic JSON transformation
> * Request/Response transformation
> * Better error handling
> * Request cancellation
> * Browser và Node.js support

### 2.2 Cài đặt Axios

```bash
npm install axios
```

### 2.3 Axios cơ bản

**utils/api.js**

```javascript
import axios from 'axios'

const api = axios.create({
  baseURL: 'https://api.example.com',
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json'
  }
})

export default api
```

**Sử dụng:**

```vue
<script setup>
import { ref } from 'vue'
import api from '@/utils/api'

const users = ref([])

const fetchUsers = async () => {
  try {
    const response = await api.get('/users')
    users.value = response.data
  } catch (err) {
    console.error('Error fetching users:', err)
  }
}

// Fetch data khi component được tạo
await fetchUsers()
</script>
```

---

## 3) Axios Methods

### 3.1 GET Request

```javascript
// Simple GET
const response = await api.get('/users')

// GET với params
const response = await api.get('/users', {
  params: {
    page: 1,
    limit: 10
  }
})

// GET với config
const response = await api.get('/users', {
  headers: {
    'Authorization': 'Bearer token'
  }
})
```

### 3.2 POST Request

```javascript
// POST với data
const response = await api.post('/users', {
  name: 'Nguyễn Văn A',
  email: 'nguyenvana@example.com'
})

// POST với config
const response = await api.post('/users', userData, {
  headers: {
    'Content-Type': 'application/json'
  }
})
```

### 3.3 PUT và PATCH

```javascript
// PUT - Update toàn bộ
const response = await api.put('/users/1', {
  name: 'Updated Name',
  email: 'updated@example.com'
})

// PATCH - Update một phần
const response = await api.patch('/users/1', {
  name: 'Updated Name'
})
```

### 3.4 DELETE Request

```javascript
// DELETE
const response = await api.delete('/users/1')
```

---

## 4) Axios Interceptors

### 4.1 Request Interceptor

> Thêm headers, transform request trước khi gửi.

**utils/api.js**

```javascript
import axios from 'axios'

const api = axios.create({
  baseURL: 'https://api.example.com'
})

// Request interceptor
api.interceptors.request.use(
  (config) => {
    // Thêm auth token
    const token = localStorage.getItem('token')
    if (token) {
      config.headers.Authorization = `Bearer ${token}`
    }
    
    // Log request
    console.log('Request:', config.method, config.url)
    
    return config
  },
  (error) => {
    return Promise.reject(error)
  }
)

export default api
```

### 4.2 Response Interceptor

> Xử lý response, handle errors.

```javascript
// Response interceptor
api.interceptors.response.use(
  (response) => {
    // Transform response
    return response
  },
  (error) => {
    // Handle errors
    if (error.response) {
      // Server responded with error
      switch (error.response.status) {
        case 401:
          // Unauthorized - redirect to login
          window.location.href = '/login'
          break
        case 403:
          // Forbidden
          console.error('Access forbidden')
          break
        case 404:
          // Not found
          console.error('Resource not found')
          break
        case 500:
          // Server error
          console.error('Server error')
          break
      }
    } else if (error.request) {
      // Request made but no response
      console.error('No response from server')
    } else {
      // Error setting up request
      console.error('Error:', error.message)
    }
    
    return Promise.reject(error)
  }
)
```

---

## 5) Error Handling

### 5.1 Try-Catch với Axios

```vue
<script setup>
import api from '@/utils/api'

const fetchData = async () => {
  try {
    const response = await api.get('/data')
    return response.data
  } catch (error) {
    if (error.response) {
      // Server responded with error status
      console.error('Error status:', error.response.status)
      console.error('Error data:', error.response.data)
    } else if (error.request) {
      // Request made but no response
      console.error('No response received')
    } else {
      // Error setting up request
      console.error('Error:', error.message)
    }
    throw error
  }
}
</script>
```

### 5.2 Error Component

**components/ErrorMessage.vue**

```vue
<template>
  <div v-if="error" class="error-message">
    <p>{{ error }}</p>
    <button @click="emit('dismiss')">Dismiss</button>
  </div>
</template>

<script setup>
defineProps({
  error: String
})

const emit = defineEmits(['dismiss'])
</script>
```

---

## 6) Loading States

### 6.1 Loading Component

```vue
<template>
  <div>
    <div v-if="loading" class="loading">
      <p>Đang tải...</p>
    </div>
    <div v-else-if="error">
      <p>Lỗi: {{ error }}</p>
    </div>
    <div v-else>
      <!-- Content -->
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import api from '@/utils/api'

const loading = ref(false)
const error = ref(null)
const data = ref(null)

const fetchData = async () => {
  loading.value = true
  error.value = null
  
  try {
    const response = await api.get('/data')
    data.value = response.data
  } catch (err) {
    error.value = err.message
  } finally {
    loading.value = false
  }
}

// Fetch data khi component được tạo
await fetchData()
</script>
```

---

## 7) API Service Pattern

### 7.1 Tạo API Service

**services/userService.js**

```javascript
import api from '@/utils/api'

export default {
  async getAllUsers() {
    const response = await api.get('/users')
    return response.data
  },
  
  async getUserById(id) {
    const response = await api.get(`/users/${id}`)
    return response.data
  },
  
  async createUser(userData) {
    const response = await api.post('/users', userData)
    return response.data
  },
  
  async updateUser(id, userData) {
    const response = await api.put(`/users/${id}`, userData)
    return response.data
  },
  
  async deleteUser(id) {
    const response = await api.delete(`/users/${id}`)
    return response.data
  }
}
```

**Sử dụng:**

```vue
<script setup>
import { ref } from 'vue'
import userService from '@/services/userService'

const users = ref([])

const loadUsers = async () => {
  try {
    users.value = await userService.getAllUsers()
  } catch (error) {
    console.error('Error loading users:', error)
  }
}

// Load users khi component được tạo
await loadUsers()
</script>
```

---

## 8) Concurrent Requests

### 8.1 Promise.all

```javascript
// Trong component
import { ref } from 'vue'

const users = ref([])
const posts = ref([])
const comments = ref([])

const loadAllData = async () => {
  try {
    const [usersRes, postsRes, commentsRes] = await Promise.all([
      api.get('/users'),
      api.get('/posts'),
      api.get('/comments')
    ])
    
    users.value = usersRes.data
    posts.value = postsRes.data
    comments.value = commentsRes.data
  } catch (error) {
    console.error('Error loading data:', error)
  }
}
```

### 8.2 Promise.allSettled

```javascript
async loadData() {
  const results = await Promise.allSettled([
    api.get('/users'),
    api.get('/posts'),
    api.get('/comments')
  ])
  
  results.forEach((result, index) => {
    if (result.status === 'fulfilled') {
      console.log(`Request ${index} succeeded:`, result.value.data)
    } else {
      console.error(`Request ${index} failed:`, result.reason)
    }
  })
}
```

---

## 9) Request Cancellation

### 9.1 Cancel Token

```vue
<script setup>
import { ref, onBeforeUnmount } from 'vue'
import axios from 'axios'
import api from '@/utils/api'

let cancelTokenSource = ref(null)

const fetchData = async () => {
  // Cancel previous request if exists
  if (cancelTokenSource.value) {
    cancelTokenSource.value.cancel('New request started')
  }
  
  // Create new cancel token
  cancelTokenSource.value = axios.CancelToken.source()
  
  try {
    const response = await api.get('/data', {
      cancelToken: cancelTokenSource.value.token
    })
    return response.data
  } catch (error) {
    if (axios.isCancel(error)) {
      console.log('Request cancelled')
    } else {
      console.error('Error:', error)
    }
  }
}

onBeforeUnmount(() => {
  // Cancel request when component unmounts
  if (cancelTokenSource.value) {
    cancelTokenSource.value.cancel('Component unmounted')
  }
})
</script>
```

---

## 10) Tóm tắt

### 10.1 Những gì đã học

> * HTTP requests với Fetch API
> * Axios và tại sao sử dụng
> * Axios methods: GET, POST, PUT, PATCH, DELETE
> * Axios interceptors
> * Error handling
> * Loading states
> * API service pattern
> * Concurrent requests
> * Request cancellation

### 10.2 Best Practices

> * Sử dụng Axios cho ứng dụng phức tạp
> * Tạo API service layer
> * Sử dụng interceptors cho auth và error handling
> * Luôn handle errors
> * Hiển thị loading states
> * Cancel requests khi component unmounts
> * Sử dụng try-catch với async/await

### 10.3 Bài tập thực hành

1. Tạo API service cho users
2. Implement CRUD operations với Axios
3. Thêm request/response interceptors
4. Handle errors và hiển thị messages
5. Implement loading states
6. Tạo form submit với API call

---

## 11) Tài liệu tham khảo

> * [Axios Documentation](https://axios-http.com/)
> * [Fetch API MDN](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
> * [Vue.js HTTP Requests](https://vuejs.org/guide/scaling-up/state-management.html)
