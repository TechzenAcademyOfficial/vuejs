# Vue.js – Buổi 16: Kỹ thuật Animation và tối ưu hiệu suất

## 1) Vue Transitions

### 1.1 Transition Component

> **Transition** component cung cấp cách dễ dàng để thêm animations.

**Cơ bản:**

```vue
<template>
  <button @click="show = !show">Toggle</button>
  <transition name="fade">
    <p v-if="show">Hello</p>
  </transition>
</template>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
```

### 1.2 Transition Classes

| Class | Mô tả |
|-------|-------|
| `v-enter-from` | Trước khi enter |
| `v-enter-active` | Trong quá trình enter |
| `v-enter-to` | Sau khi enter |
| `v-leave-from` | Trước khi leave |
| `v-leave-active` | Trong quá trình leave |
| `v-leave-to` | Sau khi leave |

### 1.3 Named Transitions

```vue
<transition name="slide">
  <div v-if="show">Content</div>
</transition>

<style>
.slide-enter-active {
  transition: transform 0.3s;
}

.slide-enter-from {
  transform: translateX(-100%);
}

.slide-leave-to {
  transform: translateX(100%);
}
</style>
```

---

## 2) Transition Modes

### 2.1 in-out và out-in

```vue
<transition name="fade" mode="out-in">
  <component :is="currentComponent" />
</transition>
```

**Modes:**
> * `default`: Enter và leave cùng lúc
> * `out-in`: Leave trước, sau đó enter
> * `in-out`: Enter trước, sau đó leave

---

## 3) JavaScript Hooks

### 3.1 Transition Hooks

```vue
<template>
  <transition
    @before-enter="beforeEnter"
    @enter="enter"
    @after-enter="afterEnter"
    @enter-cancelled="enterCancelled"
    @before-leave="beforeLeave"
    @leave="leave"
    @after-leave="afterLeave"
    @leave-cancelled="leaveCancelled"
  >
    <div v-if="show">Content</div>
  </transition>
</template>

<script>
export default {
  methods: {
    beforeEnter(el) {
      // Trước khi enter
    },
    enter(el, done) {
      // Animation enter
      // Gọi done() khi hoàn thành
      done()
    },
    afterEnter(el) {
      // Sau khi enter
    }
  }
}
</script>
```

### 3.2 Velocity.js Integration

```bash
npm install velocity-animate
```

```vue
<script setup>
import Velocity from 'velocity-animate'

const enter = (el, done) => {
  Velocity(el, {
    opacity: [1, 0],
    translateY: [0, -20]
  }, {
    duration: 300,
    complete: done
  })
}

const leave = (el, done) => {
  Velocity(el, {
    opacity: [0, 1],
    translateY: [-20, 0]
  }, {
    duration: 300,
    complete: done
  })
}
</script>
```

---

## 4) TransitionGroup

### 4.1 List Transitions

```vue
<template>
  <transition-group name="list" tag="ul">
    <li v-for="item in items" :key="item.id">
      {{ item.text }}
    </li>
  </transition-group>
</template>

<style>
.list-enter-active,
.list-leave-active {
  transition: all 0.3s;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateY(30px);
}

.list-move {
  transition: transform 0.3s;
}
</style>
```

### 4.2 Staggered Transitions

```vue
<script setup>
import Velocity from 'velocity-animate'

const beforeEnter = (el) => {
  el.style.opacity = 0
  el.style.transform = 'translateY(30px)'
}

const enter = (el, done) => {
  const delay = el.dataset.index * 50
  setTimeout(() => {
    Velocity(el, {
      opacity: 1,
      translateY: 0
    }, {
      duration: 300,
      complete: done
    })
  }, delay)
}
</script>

<template>
  <transition-group
    tag="ul"
    @before-enter="beforeEnter"
    @enter="enter"
  >
    <li
      v-for="(item, index) in items"
      :key="item.id"
      :data-index="index"
    >
      {{ item.text }}
    </li>
  </transition-group>
</template>
```

---

## 5) Performance Optimization cho Animations

### 5.1 CSS Transforms thay vì Position

> Sử dụng `transform` và `opacity` cho animations vì chúng được GPU-accelerated.

```css
/* ✅ Good - GPU accelerated */
.animated {
  transform: translateX(100px);
  opacity: 0.5;
}

/* ❌ Bad - Triggers reflow */
.animated {
  left: 100px;
  top: 50px;
}
```

### 5.2 will-change Property

```css
.animated {
  will-change: transform, opacity;
}

/* Remove sau khi animation */
.animated.animation-complete {
  will-change: auto;
}
```

### 5.3 requestAnimationFrame

```javascript
function animate() {
  // Animation logic
  requestAnimationFrame(animate)
}

animate()
```

---

## 6) Vue Transition Libraries

### 6.1 Vue Transition Component

> Built-in transition component của Vue.

### 6.2 Animate.css

```bash
npm install animate.css
```

```vue
<template>
  <transition
    enter-active-class="animate__animated animate__fadeIn"
    leave-active-class="animate__animated animate__fadeOut"
  >
    <div v-if="show">Content</div>
  </transition>
</template>

<script>
import 'animate.css'
</script>
```

### 6.3 GSAP

```bash
npm install gsap
```

```vue
<script setup>
import { gsap } from 'gsap'

const enter = (el, done) => {
  gsap.from(el, {
    opacity: 0,
    y: 50,
    duration: 0.5,
    onComplete: done
  })
}

const leave = (el, done) => {
  gsap.to(el, {
    opacity: 0,
    y: -50,
    duration: 0.5,
    onComplete: done
  })
}
</script>
```

---

## 7) Micro-interactions

### 7.1 Hover Effects

```vue
<template>
  <button class="hover-effect">Button</button>
</template>

<style>
.hover-effect {
  transition: transform 0.2s, box-shadow 0.2s;
}

.hover-effect:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}
</style>
```

### 7.2 Loading Animations

```vue
<template>
  <div class="spinner" v-if="loading"></div>
</template>

<style>
.spinner {
  border: 3px solid #f3f3f3;
  border-top: 3px solid #42b983;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style>
```

---

## 8) Tóm tắt

### 8.1 Những gì đã học

> * Vue transitions
> * Transition modes
> * JavaScript hooks
> * TransitionGroup cho lists
> * Performance optimization
> * Animation libraries
> * Micro-interactions

### 8.2 Best Practices

> * Sử dụng CSS transitions khi có thể
> * Dùng transform và opacity cho performance
> * Sử dụng will-change cẩn thận
> * Cleanup animations khi component unmounts
> * Test animations trên mobile devices

### 8.3 Bài tập thực hành

1. Tạo fade transition
2. Implement slide transition
3. Tạo list transitions với TransitionGroup
4. Add staggered animations
5. Optimize animations cho performance
6. Tạo loading spinner animation

---

## 9) Tài liệu tham khảo

> * [Vue.js Transitions](https://vuejs.org/guide/built-ins/transition.html)
> * [CSS Animations](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations)
> * [GSAP Documentation](https://greensock.com/docs/)
