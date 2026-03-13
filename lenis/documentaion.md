## Lenis — Full Guide (with Code)

**Lenis** is a modern **smooth scrolling JavaScript library** created by **Studio Freight**. It replaces the browser’s default scrolling with **buttery smooth, performant scrolling** while staying lightweight and easy to integrate.

It works well with animation libraries like **GSAP**, **Framer Motion**, and **Three.js**.

---

# 1. Install Lenis

### Using npm

```bash
npm install @studio-freight/lenis
```

### Using CDN

```html
<script src="https://unpkg.com/@studio-freight/lenis@1/bundled/lenis.min.js"></script>
```

---

# 2. Basic Setup (Vanilla JS)

### HTML

```html
<body>
  <div class="content">
    <section class="section">Section 1</section>
    <section class="section">Section 2</section>
    <section class="section">Section 3</section>
  </div>
</body>
```

### CSS

```css
body {
  margin: 0;
}

.section {
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 40px;
}
```

### JavaScript

```javascript
import Lenis from '@studio-freight/lenis'

const lenis = new Lenis({
  duration: 1.2,
  smoothWheel: true,
  smoothTouch: false
})

function raf(time) {
  lenis.raf(time)
  requestAnimationFrame(raf)
}

requestAnimationFrame(raf)
```

---

# 3. Lenis Options (Configuration)

```javascript
const lenis = new Lenis({
  duration: 1.2,
  easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
  direction: 'vertical',
  gestureDirection: 'vertical',
  smooth: true,
  smoothTouch: false,
  touchMultiplier: 2,
  infinite: false
})
```

### Important Options

| Option      | Description            |
| ----------- | ---------------------- |
| duration    | Scroll speed           |
| easing      | Custom easing function |
| direction   | vertical / horizontal  |
| smoothWheel | Smooth mouse wheel     |
| smoothTouch | Smooth touch scroll    |
| infinite    | Infinite scroll        |

---

# 4. Scroll To Section

Lenis provides a built-in **scrollTo** method.

```javascript
const button = document.querySelector('#scrollBtn')

button.addEventListener('click', () => {
  lenis.scrollTo('#section2')
})
```

Custom offset:

```javascript
lenis.scrollTo('#section2', {
  offset: -100,
  duration: 2
})
```

---

# 5. Detect Scroll Position

```javascript
lenis.on('scroll', (e) => {
  console.log(e.scroll)
})
```

Example:

```javascript
lenis.on('scroll', ({ scroll, limit, velocity }) => {
  console.log(scroll)
})
```

---

# 6. Lenis + GSAP Integration

Lenis works perfectly with **GSAP** and **ScrollTrigger**.

```javascript
import Lenis from '@studio-freight/lenis'
import gsap from 'gsap'
import ScrollTrigger from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

const lenis = new Lenis()

lenis.on('scroll', ScrollTrigger.update)

gsap.ticker.add((time)=>{
  lenis.raf(time * 1000)
})

gsap.ticker.lagSmoothing(0)
```

---

# 7. React Setup

Install:

```bash
npm install @studio-freight/react-lenis
```

Example:

```jsx
import { ReactLenis } from '@studio-freight/react-lenis'

function App() {
  return (
    <ReactLenis root>
      <main>
        <section style={{height:'100vh'}}>Section 1</section>
        <section style={{height:'100vh'}}>Section 2</section>
      </main>
    </ReactLenis>
  )
}

export default App
```

---

# 8. Horizontal Scroll

```javascript
const lenis = new Lenis({
  direction: 'horizontal',
  gestureDirection: 'both'
})
```

CSS:

```css
.wrapper {
  display: flex;
  width: max-content;
}
```

---

# 9. Stop / Start Scroll

Pause scrolling:

```javascript
lenis.stop()
```

Resume scrolling:

```javascript
lenis.start()
```

---

# 10. Destroy Lenis

```javascript
lenis.destroy()
```

Used when removing the scroll instance.

---

# 11. Advanced Example

```javascript
const lenis = new Lenis({
  duration: 1.5,
  smoothWheel: true
})

lenis.on('scroll', ({ scroll }) => {
  document.querySelector('.progress').style.width =
    (scroll / document.body.scrollHeight) * 100 + '%'
})

function raf(time) {
  lenis.raf(time)
  requestAnimationFrame(raf)
}

requestAnimationFrame(raf)
```

---

# 12. When to Use Lenis

Use Lenis when you want:

* Smooth scrolling websites
* Portfolio sites
* Creative landing pages
* WebGL + scroll animations
* GSAP scroll animations

Many modern portfolio sites and **award-winning websites** (like those seen on **Awwwards**) use libraries like Lenis.

---

# 13. Performance Tips

✔ Use `requestAnimationFrame`
✔ Avoid heavy DOM updates in scroll event
✔ Combine with `transform` animations
✔ Avoid large images without lazy loading

---

✅ **Summary**

Lenis gives:

* smooth inertia scrolling
* better animation control
* easy integration with **GSAP**
* lightweight (~8kb)

---

💡 If you want, I can also show:

* **Lenis + GSAP real portfolio animation tutorial**
* **Lenis + React + Next.js setup**
* **10 real Lenis animation examples used by award-winning websites**.
