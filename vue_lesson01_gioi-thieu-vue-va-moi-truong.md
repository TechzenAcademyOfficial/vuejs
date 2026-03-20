# Vue.js – Buổi 1: Giới thiệu Vue.js & Môi trường

## 1) Giới thiệu Vue.js

### 1.1 Vue.js là gì?

> * **Vue.js** là một framework JavaScript tiến bộ và dễ học, được thiết kế để xây dựng giao diện người dùng (UI) một cách linh hoạt và hiệu quả.
> * Vue.js được tạo ra bởi **Evan You** vào năm 2014, với mục tiêu kết hợp những điểm tốt nhất của các framework như Angular và React.
> * Vue.js là **progressive framework** - có thể tích hợp từng phần vào dự án hiện có hoặc xây dựng ứng dụng SPA (Single Page Application) hoàn chỉnh.

### 1.2 Đặc điểm nổi bật của Vue.js

| Đặc điểm | Mô tả |
|----------|-------|
| **Dễ học** | Cú pháp đơn giản, gần gũi với HTML/CSS/JavaScript thuần |
| **Reactive** | Hệ thống reactive tự động cập nhật DOM khi dữ liệu thay đổi |
| **Component-based** | Xây dựng ứng dụng từ các component có thể tái sử dụng |
| **Virtual DOM** | Sử dụng Virtual DOM để tối ưu hiệu suất render |
| **Lightweight** | Kích thước nhỏ gọn (~34KB khi minified + gzip) |
| **Flexible** | Có thể tích hợp vào dự án hiện có hoặc xây dựng ứng dụng mới |

### 1.3 So sánh Vue.js với các framework khác

| Framework | Ưu điểm | Nhược điểm |
|-----------|---------|------------|
| **Vue.js** | Dễ học, linh hoạt, hiệu suất tốt, cộng đồng lớn | Ít công ty lớn sử dụng hơn React/Angular |
| **React** | Cộng đồng lớn, nhiều thư viện, được Facebook hỗ trợ | Cần học JSX, cấu hình phức tạp |
| **Angular** | Framework đầy đủ tính năng, TypeScript native | Khó học, nặng, phức tạp cho dự án nhỏ |

---

## 2) Cài đặt môi trường phát triển

### 2.1 Yêu cầu hệ thống

> * **Node.js**: Phiên bản 16.x trở lên (khuyến nghị LTS)
> * **npm** hoặc **yarn**: Quản lý package
> * **Code Editor**: VS Code (khuyến nghị) với extension Volar
> * **Browser**: Chrome, Firefox, Safari, Edge (phiên bản mới nhất)

### 2.2 Kiểm tra Node.js và npm

```bash
# Kiểm tra phiên bản Node.js
node --version

# Kiểm tra phiên bản npm
npm --version
```

### 2.3 Cài đặt Vue CLI (Command Line Interface)

> **Vue CLI** là công cụ chính thức để tạo và quản lý dự án Vue.js.

```bash
# Cài đặt Vue CLI globally
npm install -g @vue/cli

# Hoặc sử dụng yarn
yarn global add @vue/cli

# Kiểm tra phiên bản
vue --version
```

### 2.4 Tạo dự án Vue.js đầu tiên

#### 2.4.1 Sử dụng Vue CLI

```bash
# Tạo dự án mới
vue create my-vue-app

# Chọn cấu hình:
# - Default (babel, eslint)
# - Manually select features (tùy chọn: Router, Vuex, CSS Pre-processors, etc.)
```

#### 2.4.2 Sử dụng Vite (Khuyến nghị cho Vue 3)

> **Vite** là build tool hiện đại, nhanh hơn nhiều so với webpack.

```bash
# Tạo dự án Vue 3 với Vite
npm create vue@latest my-vue-app

# Hoặc với yarn
yarn create vue my-vue-app

# Chọn các tùy chọn:
# ✓ Add TypeScript? No
# ✓ Add JSX Support? No
# ✓ Add Vue Router? Yes
# ✓ Add Pinia? Yes (hoặc Vuex)
# ✓ Add Vitest? No
# ✓ Add ESLint? Yes
```

### 2.5 Cấu trúc thư mục dự án Vue.js

```
my-vue-app/
├── public/              # File tĩnh (index.html, favicon, etc.)
│   └── index.html
├── src/                 # Source code chính
│   ├── assets/          # Hình ảnh, CSS, fonts
│   ├── components/      # Vue components
│   ├── router/          # Vue Router configuration
│   ├── store/           # Vuex/Pinia store
│   ├── views/           # Page components
│   ├── App.vue          # Root component
│   └── main.js          # Entry point
├── .gitignore
├── package.json         # Dependencies
├── vite.config.js       # Vite configuration (nếu dùng Vite)
└── README.md
```

---

## 3) Hello World với Vue.js

### 3.1 Sử dụng CDN (Nhanh nhất để bắt đầu)

> Phù hợp cho học tập và prototype nhanh, không phù hợp cho production.

```html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue.js Hello World</title>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
</head>
<body>
    <div id="app">
        <h1>{{ message }}</h1>
        <button @click="changeMessage">Đổi thông điệp</button>
    </div>

    <script>
        const { createApp } = Vue;

        createApp({
            data() {
                return {
                    message: 'Xin chào Vue.js!'
                }
            },
            methods: {
                changeMessage() {
                    this.message = 'Bạn đã click vào nút!';
                }
            }
        }).mount('#app');
    </script>
</body>
</html>
```

### 3.2 Sử dụng Single File Component (SFC)

> Cách tiếp cận chuyên nghiệp, khuyến nghị cho dự án thực tế.

**src/App.vue**

```vue
<template>
  <div id="app">
    <h1>{{ message }}</h1>
    <button @click="changeMessage">Đổi thông điệp</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const message = ref('Xin chào Vue.js!')

const changeMessage = () => {
  message.value = 'Bạn đã click vào nút!'
}
</script>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

button {
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
}
</style>
```

**src/main.js**

```javascript
import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```

### 3.3 Chạy dự án

```bash
# Di chuyển vào thư mục dự án
cd my-vue-app

# Cài đặt dependencies
npm install

# Chạy development server
npm run dev

# Build cho production
npm run build
```

> Development server thường chạy tại `http://localhost:5173` (Vite) hoặc `http://localhost:8080` (Vue CLI)

---

## 4) Khái niệm cơ bản về Vue.js

### 4.1 Instance và Component

> * **Vue Instance**: Là root của ứng dụng Vue, được tạo bằng `createApp()`
> * **Component**: Là các đơn vị có thể tái sử dụng, có thể lồng nhau

### 4.2 Template Syntax

> Vue.js sử dụng template syntax đơn giản dựa trên HTML:

| Syntax | Mô tả | Ví dụ |
|--------|-------|-------|
| **Interpolation** | Hiển thị dữ liệu | `{{ message }}` |
| **v-bind** | Ràng buộc thuộc tính | `v-bind:href` hoặc `:href` |
| **v-on** | Lắng nghe sự kiện | `v-on:click` hoặc `@click` |
| **v-model** | Two-way binding | `v-model="input"` |

### 4.3 Data và Reactivity

> Vue.js sử dụng hệ thống **reactive** để tự động cập nhật DOM khi dữ liệu thay đổi.

**Với Composition API (Vue 3):**

```javascript
import { ref } from 'vue'

const count = ref(0)
const message = ref('Hello Vue')
```

> Khi `count` hoặc `message` thay đổi, Vue tự động cập nhật các phần tử DOM liên quan.
> Với Composition API, sử dụng `ref()` cho primitive values và `reactive()` cho objects.

---

## 5) DevTools và công cụ hỗ trợ

### 5.1 Vue DevTools

> **Vue DevTools** là extension trình duyệt giúp debug và kiểm tra ứng dụng Vue.js.

**Cài đặt:**
* Chrome: [Vue.js devtools](https://chrome.google.com/webstore/detail/vuejs-devtools)
* Firefox: [Vue.js devtools](https://addons.mozilla.org/en-US/firefox/addon/vue-js-devtools/)

**Tính năng:**
* Xem component tree
* Kiểm tra và chỉnh sửa data, props, computed
* Theo dõi events
* Time travel debugging với Vuex

### 5.2 VS Code Extensions

| Extension | Mô tả |
|-----------|-------|
| **Volar** | Hỗ trợ Vue 3 với TypeScript, IntelliSense |
| **Vetur** | Hỗ trợ Vue 2 (đã deprecated, dùng Volar cho Vue 3) |
| **ESLint** | Kiểm tra code quality |
| **Prettier** | Format code tự động |

---

## 6) Tóm tắt

### 6.1 Những gì đã học

> * Vue.js là framework JavaScript progressive để xây dựng UI
> * Cài đặt môi trường: Node.js, npm, Vue CLI hoặc Vite
> * Tạo dự án Vue.js đầu tiên
> * Cấu trúc thư mục cơ bản
> * Hello World với Vue.js (CDN và SFC)
> * Khái niệm về instance, component, template syntax, reactivity

### 6.2 Bài tập thực hành

1. Cài đặt Node.js và npm
2. Tạo dự án Vue.js mới bằng Vite
3. Tạo component HelloWorld hiển thị tên của bạn
4. Thêm nút để thay đổi màu sắc của text
5. Cài đặt Vue DevTools và kiểm tra component trong DevTools

---

## 7) Tài liệu tham khảo

> * [Vue.js Official Documentation](https://vuejs.org/)
> * [Vue.js Guide](https://vuejs.org/guide/)
> * [Vite Documentation](https://vitejs.dev/)
> * [Vue Router](https://router.vuejs.org/)
> * [Pinia (State Management)](https://pinia.vuejs.org/)
