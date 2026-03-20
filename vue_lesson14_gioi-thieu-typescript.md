# Vue.js – Buổi 14: Giới thiệu TypeScript (cho Vue.js)

## 1) TypeScript là gì?
> **TypeScript** là superset của JavaScript, bổ sung **kiểu dữ liệu (types)** để giúp code an toàn hơn và dễ bảo trì hơn.

**Vì sao dùng TypeScript?**
> * Phát hiện lỗi sớm khi viết code (type checking).
> * IDE gợi ý tốt hơn (autocomplete, refactor an toàn).
> * Code rõ ràng hơn nhờ khai báo kiểu.

---

## 2) Các kiểu dữ liệu cơ bản
```ts
let name: string = 'Vue'
let age: number = 3
let isDone: boolean = false

// unknown: chưa biết kiểu, phải kiểm tra trước khi dùng
let payload: unknown

// any: bỏ qua kiểm tra type (cần tránh dùng nhiều)
let raw: any
```

**Union type** (một biến có thể thuộc nhiều kiểu):
```ts
let id: number | string = 1
id = 'u-001'
```

**Literal type** (giá trị cố định):
```ts
let status: 'idle' | 'loading' | 'success' = 'idle'
```

---

## 3) Interface và Type Alias
### 3.1 Interface
```ts
interface User {
  id: number
  name: string
  email?: string // optional
}
```

### 3.2 Type alias
```ts
type Product = {
  id: number
  title: string
}
```

---

## 4) Generics (giới thiệu nhanh)
> Generics giúp viết hàm/component tái sử dụng được với nhiều kiểu khác nhau (giữ type an toàn).

**Ví dụ nhanh:**
```ts
function wrap<T>(value: T) {
  return { value }
}

const a = wrap(123) // value: number
const b = wrap('Vue') // value: string
```

---

## 5) Type Narrowing (thu hẹp kiểu)
Khi dùng union, bạn cần kiểm tra kiểu trước khi truy cập property:
```ts
function formatId(id: number | string) {
  if (typeof id === 'number') {
    return id.toString()
  }
  return id
}
```

---

## 6) Dùng TypeScript trong Vue 3 (`<script setup>`)
### 6.1 Typing Props
```vue
<script setup lang="ts">
interface User {
  id: number
  name: string
}

const props = defineProps<{
  user: User
}>()

// props.user đã được type-safe
console.log(props.user.name)
</script>
```

### 6.2 Typing Emits
```vue
<script setup lang="ts">
const emit = defineEmits<{
  (e: 'select', id: number): void
}>()

function onSelect() {
  emit('select', 1)
}
</script>
```

### 6.3 Typing `ref` và `computed`
```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

const count = ref<number>(0)
const doubleCount = computed(() => count.value * 2)
</script>
```

---

## 7) Gợi ý cấu hình nhanh
Trong Vue 3 dự án (Vite), bạn thường chỉ cần đảm bảo:
* Có file `tsconfig.json`
* Thêm `lang="ts"` cho `<script>`

---

## 8) Tóm tắt
> * TypeScript giúp giảm lỗi bằng cách kiểm tra kiểu.
> * Dùng `interface`/`type` để mô tả dữ liệu.
> * Dùng `generics` để tạo hàm/component tái sử dụng kiểu linh hoạt.
> * Trong Vue 3, typing `props`, `emits`, `ref`, `computed` sẽ làm code rõ ràng và an toàn hơn.

---

## 9) Setup TypeScript cho Vue 3 (Vite) (nhanh)
```bash
npm create vite@latest my-vue-ts -- --template vue-ts
cd my-vue-ts
npm install
npm run dev
```

> Bật `strict` trong `tsconfig.json` (nếu có) để TS cảnh báo sớm hơn.
> Quy ước: dùng `<script setup lang="ts">`, hạn chế `any`, và tách các type dữ liệu domain ra file riêng.

---

## 10) Type inference, annotation và `as const`
### 10.1 Inference là “điểm mạnh” (ít phải ghi kiểu)
```ts
const count = 0
// count có kiểu number (được suy luận)
```

### 10.2 `as const` để literal type chính xác hơn
```ts
const status = 'idle' as const
// status có kiểu 'idle' thay vì string
```

---

## 11) Narrowing nâng cao (làm union “dễ dùng” hơn)
### 11.1 Discriminated Union (nhìn theo “cờ”)
> Có thể dùng union có trường “status” để `switch` ra từng trường hợp một cách an toàn.

### 11.2 Type guard (tự định nghĩa hàm thu hẹp kiểu)
```ts
type User = { id: number; name: string }
type Admin = { id: number; role: 'admin' | 'super' }

function isAdmin(u: User | Admin): u is Admin {
  return 'role' in u
}

function handle(u: User | Admin) {
  if (isAdmin(u)) {
    // u: Admin
    return u.role
  }
  // u: User
  return u.name
}
```

---

## 12) Interface, type alias và Generics (ứng dụng thực tế)
### 12.1 `interface` vs `type alias` (ngắn gọn)
> * `interface`: hợp khi bạn muốn mở rộng (`extends`) nhiều nơi.
> * `type`: linh hoạt khi tạo union/intersection, hoặc mô tả kiểu phức tạp.

> (Rút gọn) Generics chuyên sâu & utility types mình sẽ để dành cho bài tiếp theo.

---

## 13) Tài liệu tham khảo
> * [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
> * [Vue - TypeScript Support](https://vuejs.org/guide/typescript/overview.html)

