Lenis is a lightweight smooth-scrolling library made for modern websites. It gives you controlled, animated scrolling, normalizes wheel and touch behavior, works well for parallax/WebGL/scroll-linked effects, and is designed to stay simple to integrate. The current project is maintained under `darkroomengineering/lenis`, and the official site presents it as the smooth scroll library for immersive interfaces. ([GitHub][1])

## 1) What Lenis actually does

Normally, browser scroll happens instantly based on input. Lenis sits in between user input and the final scroll position:

* user scrolls wheel / touch / trackpad
* Lenis captures that delta
* Lenis interpolates toward a target position
* your page appears to scroll more smoothly

That is why Lenis is commonly used when building premium landing pages, parallax sections, scroll-synced animations, and GSAP/WebGL experiences. The project README specifically highlights smooth scrolling, performance, accessibility considerations, sticky support, IntersectionObserver compatibility, external RAF control, and custom easing/duration. ([GitHub][1])

## 2) When to use Lenis

Use Lenis when you want:

* smoother scroll feel
* unified feel across mouse wheel and touch input
* easier control for animation-heavy sites
* scroll progress values for effects
* programmatic smooth scrolling to sections

Do **not** use it just because “smooth looks cool.” For simple dashboards, admin panels, or content-heavy sites, native browser scroll is often better for simplicity and predictability. That is an implementation judgment, but it follows from Lenis being positioned for immersive, animation-driven interfaces. ([Lenis][2])

---

# 3) Install Lenis

## Using npm

```bash
npm install lenis
```

The current package name is `lenis`. The React package is provided under `lenis/react`. ([GitHub][3])

## Basic import

```js
import Lenis from 'lenis'
```

---

# 4) Your first Lenis example

This is the smallest working version.

## HTML

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Lenis Basic</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    html, body {
      width: 100%;
      min-height: 100%;
    }

    section {
      height: 100vh;
      display: grid;
      place-items: center;
      font-size: 3rem;
      font-family: sans-serif;
    }

    .one { background: #f4a261; }
    .two { background: #2a9d8f; }
    .three { background: #e9c46a; }
    .four { background: #264653; color: white; }
  </style>
</head>
<body>
  <section class="one">Section 1</section>
  <section class="two">Section 2</section>
  <section class="three">Section 3</section>
  <section class="four">Section 4</section>

  <script type="module">
    import Lenis from 'https://unpkg.com/lenis@1.3.18/dist/lenis.min.js'

    const lenis = new Lenis()

    function raf(time) {
      lenis.raf(time)
      requestAnimationFrame(raf)
    }

    requestAnimationFrame(raf)
  </script>
</body>
</html>
```

## Why this works

Lenis needs a render loop. The `requestAnimationFrame` loop keeps updating Lenis every frame so it can animate the scroll position smoothly. Lenis’s official examples show the `raf` pattern as the standard setup. ([GitHub][1])

---

# 5) Core idea: `raf()`

This part is the heart of Lenis:

```js
function raf(time) {
  lenis.raf(time)
  requestAnimationFrame(raf)
}
requestAnimationFrame(raf)
```

Without this, Lenis cannot animate scroll progression correctly. If you are already using a render loop through GSAP ticker or another animation engine, you typically feed Lenis from that loop instead of creating two separate timing systems. Lenis explicitly supports external RAF control. ([GitHub][4])

---

# 6) Important options

A common beginner mistake is using Lenis without understanding its options. Here is a practical starter config:

```js
const lenis = new Lenis({
  duration: 1.2,
  smoothWheel: true,
  syncTouch: false,
  touchMultiplier: 1,
  wheelMultiplier: 1,
  infinite: false,
})
```

These options control how quickly Lenis interpolates, how wheel input is handled, whether touch behavior is synchronized, and whether scrolling loops infinitely. Lenis exposes configurable behavior for easing, duration, wheel/touch input, and infinite scroll behavior. ([GitHub][1])

## Practical meaning of common options

### `duration`

How long the scroll easing lasts.

```js
duration: 1.2
```

Higher value = heavier/slower feeling.
Lower value = snappier feeling.

### `smoothWheel`

Smooths mouse wheel / trackpad input.

```js
smoothWheel: true
```

### `syncTouch`

Attempts smoother touch syncing.

```js
syncTouch: true
```

Be careful on mobile because touch behavior can feel different than desktop depending on device/browser.

### `wheelMultiplier`

Makes wheel input stronger or weaker.

```js
wheelMultiplier: 0.8
```

### `touchMultiplier`

Same idea for touch movement.

### `infinite`

Creates looped scrolling in suitable layouts.

```js
infinite: true
```

Use only when your design is intentionally infinite.

---

# 7) Listening to scroll events

Lenis can emit scroll data, which is useful for building effects.

```js
lenis.on('scroll', (e) => {
  console.log(e)
})
```

This lets you read progress-like information and react to scrolling for parallax, progress bars, number counters, or custom transforms. Lenis documents a `scroll` event and commonly uses it in examples. ([GitHub][1])

Example:

```js
const progressBar = document.querySelector('.progress')

lenis.on('scroll', ({ scroll, limit, velocity, direction, progress }) => {
  progressBar.style.transform = `scaleX(${progress})`
})
```

---

# 8) Scrolling to a section with Lenis

One of the most useful methods is `scrollTo()`.

```js
const btn = document.querySelector('#go-services')
const section = document.querySelector('#services')

btn.addEventListener('click', () => {
  lenis.scrollTo(section, {
    offset: -20,
    duration: 1.5,
  })
})
```

This is much better than manually calculating native scroll if you want the same Lenis feel everywhere. The project includes `scrollTo` support for programmatic smooth navigation. ([GitHub][1])

### Example HTML

```html
<nav>
  <button id="go-services">Go to Services</button>
</nav>

<section id="hero">Hero</section>
<section id="services">Services</section>
```

---

# 9) Real project 1: smooth scrolling landing page

This is a realistic beginner project.

## Goal

Build a landing page with:

* sticky nav
* smooth section scrolling
* progress bar
* anchor button navigation

## HTML

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Lenis Landing Page</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    html, body {
      width: 100%;
      min-height: 100%;
      font-family: Arial, sans-serif;
    }

    body {
      overflow-x: hidden;
    }

    .progress-wrap {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 6px;
      background: rgba(0,0,0,0.08);
      z-index: 1000;
      transform-origin: left;
    }

    .progress {
      width: 100%;
      height: 100%;
      background: #111;
      transform: scaleX(0);
      transform-origin: left;
    }

    nav {
      position: fixed;
      top: 20px;
      left: 50%;
      transform: translateX(-50%);
      z-index: 999;
      display: flex;
      gap: 12px;
      padding: 12px 18px;
      background: rgba(255,255,255,0.8);
      backdrop-filter: blur(10px);
      border-radius: 999px;
    }

    nav button {
      border: none;
      background: #111;
      color: white;
      padding: 10px 14px;
      border-radius: 999px;
      cursor: pointer;
    }

    section {
      min-height: 100vh;
      display: grid;
      place-items: center;
      padding: 100px 20px;
      text-align: center;
    }

    #hero { background: #f5f5f5; }
    #about { background: #d8f3dc; }
    #services { background: #bee1e6; }
    #contact { background: #fde2e4; }

    h1, h2 {
      margin-bottom: 20px;
    }

    p {
      max-width: 700px;
      line-height: 1.6;
      font-size: 1.1rem;
    }
  </style>
</head>
<body>
  <div class="progress-wrap">
    <div class="progress"></div>
  </div>

  <nav>
    <button data-target="#hero">Hero</button>
    <button data-target="#about">About</button>
    <button data-target="#services">Services</button>
    <button data-target="#contact">Contact</button>
  </nav>

  <section id="hero">
    <div>
      <h1>Modern Agency</h1>
      <p>Scroll down to explore our smooth scrolling landing page powered by Lenis.</p>
    </div>
  </section>

  <section id="about">
    <div>
      <h2>About</h2>
      <p>We create premium digital experiences with animation, storytelling, and performance-first engineering.</p>
    </div>
  </section>

  <section id="services">
    <div>
      <h2>Services</h2>
      <p>Brand design, web development, interactive websites, motion systems, and scroll-based experiences.</p>
    </div>
  </section>

  <section id="contact">
    <div>
      <h2>Contact</h2>
      <p>Let’s build something memorable together.</p>
    </div>
  </section>

  <script type="module">
    import Lenis from 'https://unpkg.com/lenis@1.3.18/dist/lenis.min.js'

    const lenis = new Lenis({
      duration: 1.1,
      smoothWheel: true
    })

    function raf(time) {
      lenis.raf(time)
      requestAnimationFrame(raf)
    }
    requestAnimationFrame(raf)

    const progress = document.querySelector('.progress')
    lenis.on('scroll', ({ progress: p }) => {
      progress.style.transform = `scaleX(${p})`
    })

    document.querySelectorAll('nav button').forEach((button) => {
      button.addEventListener('click', () => {
        const target = button.dataset.target
        lenis.scrollTo(target, { offset: -40, duration: 1.2 })
      })
    })
  </script>
</body>
</html>
```

## What you learned here

This project teaches:

* initializing Lenis
* RAF loop
* scroll event usage
* programmatic section navigation
* progress bar sync

That is the foundation for most real-world Lenis sites.

---

# 10) Real project 2: parallax on scroll

Lenis becomes really useful when paired with transforms.

## HTML

```html
<section class="hero">
  <img class="bg" src="https://picsum.photos/1200/800" alt="background">
  <h1 class="title">Creative Studio</h1>
</section>
<section class="spacer"></section>
```

## CSS

```css
body {
  margin: 0;
  overflow-x: hidden;
}

.hero {
  position: relative;
  height: 100vh;
  overflow: hidden;
  display: grid;
  place-items: center;
  background: black;
}

.bg {
  position: absolute;
  width: 110%;
  height: 110%;
  object-fit: cover;
  will-change: transform;
}

.title {
  position: relative;
  color: white;
  font-size: 5rem;
  z-index: 2;
}

.spacer {
  height: 200vh;
  background: #111;
}
```

## JS

```js
import Lenis from 'https://unpkg.com/lenis@1.3.18/dist/lenis.min.js'

const lenis = new Lenis()

const bg = document.querySelector('.bg')
const title = document.querySelector('.title')

lenis.on('scroll', ({ scroll }) => {
  bg.style.transform = `translateY(${scroll * 0.2}px)`
  title.style.transform = `translateY(${scroll * 0.1}px)`
})

function raf(time) {
  lenis.raf(time)
  requestAnimationFrame(raf)
}
requestAnimationFrame(raf)
```

## Why this feels better with Lenis

Because Lenis smooths the scroll progression, your transform-based animations also appear more fluid and less jumpy than raw wheel spikes. That fits Lenis’s intended use for parallax and animation-heavy sites. ([GitHub][1])

---

# 11) Lenis with GSAP

Lenis is very commonly paired with GSAP. The safest pattern is to let GSAP’s ticker drive Lenis so both animation systems stay in sync.

```html
<script type="module">
  import Lenis from 'https://unpkg.com/lenis@1.3.18/dist/lenis.min.js'
  import gsap from 'https://cdn.skypack.dev/gsap'

  const lenis = new Lenis({
    duration: 1.2
  })

  gsap.ticker.add((time) => {
    lenis.raf(time * 1000)
  })

  gsap.ticker.lagSmoothing(0)
</script>
```

The reason for `time * 1000` is that GSAP ticker time is in seconds while `requestAnimationFrame`-style time passed to Lenis is in milliseconds. Lenis supports external RAF, which is why this pattern works well. ([GitHub][4])

## Example with ScrollTrigger refresh

```js
import Lenis from 'https://unpkg.com/lenis@1.3.18/dist/lenis.min.js'
import gsap from 'https://cdn.skypack.dev/gsap'
import ScrollTrigger from 'https://cdn.skypack.dev/gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

const lenis = new Lenis()

lenis.on('scroll', ScrollTrigger.update)

gsap.ticker.add((time) => {
  lenis.raf(time * 1000)
})

gsap.ticker.lagSmoothing(0)

gsap.to('.box', {
  x: 700,
  scrollTrigger: {
    trigger: '.box',
    start: 'top 80%',
    end: 'top 20%',
    scrub: true
  }
})
```

This setup is popular because Lenis handles smooth movement while ScrollTrigger reads the updated scroll state.

---

# 12) Lenis in React

Lenis has an official React package with a `ReactLenis` component and a `useLenis` hook. ([GitHub][3])

## Install

```bash
npm install lenis
```

## Basic React component

```jsx
import { ReactLenis } from 'lenis/react'

export default function App() {
  return (
    <ReactLenis root options={{ duration: 1.2 }}>
      <main>
        <section style={{ height: '100vh', display: 'grid', placeItems: 'center' }}>
          <h1>Hero Section</h1>
        </section>
        <section style={{ height: '100vh', display: 'grid', placeItems: 'center' }}>
          <h2>About Section</h2>
        </section>
        <section style={{ height: '100vh', display: 'grid', placeItems: 'center' }}>
          <h2>Services Section</h2>
        </section>
      </main>
    </ReactLenis>
  )
}
```

## Accessing Lenis instance

```jsx
import { ReactLenis, useLenis } from 'lenis/react'
import { useEffect } from 'react'

function ScrollLogger() {
  const lenis = useLenis()

  useEffect(() => {
    if (!lenis) return

    const handler = (e) => {
      console.log('scroll progress:', e.progress)
    }

    lenis.on('scroll', handler)

    return () => {
      lenis.off('scroll', handler)
    }
  }, [lenis])

  return null
}

export default function App() {
  return (
    <ReactLenis root options={{ duration: 1.1 }}>
      <ScrollLogger />
      <main>
        <section style={{ height: '100vh' }}>One</section>
        <section style={{ height: '100vh' }}>Two</section>
      </main>
    </ReactLenis>
  )
}
```

---

# 13) Lenis in Next.js

In Next.js, usually put Lenis in a client component.

## `app/providers.jsx`

```jsx
'use client'

import { ReactLenis } from 'lenis/react'

export default function Providers({ children }) {
  return (
    <ReactLenis root options={{ duration: 1.2 }}>
      {children}
    </ReactLenis>
  )
}
```

## `app/layout.jsx`

```jsx
import Providers from './providers'

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <Providers>{children}</Providers>
      </body>
    </html>
  )
}
```

This follows the official React integration idea of wrapping your app and providing the Lenis instance through context. ([GitHub][3])

---

# 14) Common methods you should know

## `scrollTo()`

```js
lenis.scrollTo('#contact')
lenis.scrollTo(800)
lenis.scrollTo(document.querySelector('.card'))
```

## Stop scrolling temporarily

```js
lenis.stop()
```

Useful for opening menus, drawers, or modal states.

## Resume

```js
lenis.start()
```

## Destroy

```js
lenis.destroy()
```

Useful when removing a page/component in SPA setups.

Lenis supports instance control and programmatic scrolling through its API. ([GitHub][1])

---

# 15) Modal and overlay patterns

A common real-world problem is: “When modal opens, background scroll should stop.”

```js
const openBtn = document.querySelector('#openModal')
const closeBtn = document.querySelector('#closeModal')
const modal = document.querySelector('.modal')

openBtn.addEventListener('click', () => {
  modal.classList.add('active')
  lenis.stop()
})

closeBtn.addEventListener('click', () => {
  modal.classList.remove('active')
  lenis.start()
})
```

There is also discussion in the project around data attributes such as `data-lenis-stop` and `data-lenis-prevent` for managing scroll behavior around overlays and nested areas. ([GitHub][5])

---

# 16) Nested scroll containers

If you have a scrollable element inside the page, such as:

* sidebar
* modal body
* chat panel
* long dropdown

you often need to prevent Lenis from hijacking that specific inner scroll region. That is where Lenis-specific prevention patterns become important, otherwise the whole page may keep moving when you want the inner container to scroll. The project discussion around `data-lenis-prevent` exists for this exact kind of situation. ([GitHub][5])

Example idea:

```html
<div class="modal-content" data-lenis-prevent>
  ...
</div>
```

---

# 17) Common beginner mistakes

## Mistake 1: forgetting the RAF loop

```js
const lenis = new Lenis()
// forgot lenis.raf(...)
```

Result: Lenis initializes, but smooth scrolling does not actually behave correctly.

## Mistake 2: using body overflow hacks carelessly

If your CSS fights Lenis, scrolling may feel broken.

Bad examples:

```css
body {
  overflow: hidden;
}
```

Use only when you intentionally need it.

## Mistake 3: mixing multiple scroll engines

Do not let native smooth scroll, another scroll library, and Lenis all try to control the same page.

## Mistake 4: too much smoothing

Very large duration values can make scrolling feel laggy.

Bad:

```js
duration: 3
```

Usually start around `1` to `1.3`, then tune.

## Mistake 5: heavy scroll callbacks

This is bad:

```js
lenis.on('scroll', () => {
  // huge DOM queries every frame
  document.querySelectorAll('.card').forEach(...)
})
```

Better:

```js
const cards = document.querySelectorAll('.card')

lenis.on('scroll', ({ scroll }) => {
  cards.forEach((card, i) => {
    card.style.transform = `translateY(${scroll * 0.03 * (i + 1)}px)`
  })
})
```

Because Lenis updates frequently, your callback code must also be efficient.

---

# 18) Accessibility and UX notes

Lenis emphasizes keeping useful browser behaviors such as keyboard navigation, page search, sticky, and IntersectionObserver compatibility. Still, you should test:

* keyboard scrolling
* focus jumping to anchors
* reduced motion preference
* mobile touch feel
* modal lock behavior
* screen-reader navigation

The README specifically mentions accessibility-related considerations and compatibility with sticky and IntersectionObserver. ([GitHub][4])

A good reduced-motion pattern:

```js
const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches

const lenis = new Lenis({
  duration: prefersReducedMotion ? 0 : 1.2
})
```

That reduced-motion example is an implementation recommendation, not a direct Lenis requirement.

---

# 19) Performance tips

For best results:

* animate `transform`, not `top/left`
* cache DOM selectors
* avoid expensive layout thrashing in scroll handlers
* use `will-change: transform` only where needed
* keep images optimized
* do not attach hundreds of heavy scroll listeners

Lenis is performance-oriented, but poor animation code can still make the page janky. Lenis being lightweight and performance-minded does not automatically fix unoptimized DOM work. ([GitHub][1])

---

# 20) Full real project: portfolio page with Lenis + GSAP

This is closer to what agencies and portfolio sites do.

## Features

* smooth scroll with Lenis
* progress bar
* navbar section jumps
* parallax hero image
* GSAP reveal animations

## HTML

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Portfolio with Lenis</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    html, body { width: 100%; min-height: 100%; font-family: Arial, sans-serif; }
    body { overflow-x: hidden; background: #0d0d0d; color: white; }

    .progress-wrap {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 4px;
      background: rgba(255,255,255,0.08);
      z-index: 9999;
    }

    .progress {
      width: 100%;
      height: 100%;
      background: white;
      transform: scaleX(0);
      transform-origin: left;
    }

    nav {
      position: fixed;
      top: 20px;
      right: 20px;
      z-index: 999;
      display: flex;
      gap: 10px;
    }

    nav button {
      border: 1px solid rgba(255,255,255,0.2);
      background: rgba(255,255,255,0.08);
      color: white;
      padding: 10px 14px;
      border-radius: 999px;
      cursor: pointer;
    }

    section {
      min-height: 100vh;
      padding: 100px 8vw;
      display: flex;
      align-items: center;
      justify-content: center;
      position: relative;
    }

    .hero {
      overflow: hidden;
    }

    .hero-image {
      position: absolute;
      inset: 0;
      width: 100%;
      height: 120%;
      object-fit: cover;
      opacity: 0.4;
      will-change: transform;
    }

    .hero-content {
      position: relative;
      text-align: center;
      z-index: 2;
    }

    .hero-content h1 {
      font-size: clamp(3rem, 10vw, 8rem);
      margin-bottom: 20px;
    }

    .panel {
      width: 100%;
      max-width: 1000px;
    }

    .panel h2 {
      font-size: clamp(2rem, 5vw, 4rem);
      margin-bottom: 20px;
    }

    .panel p {
      font-size: 1.1rem;
      line-height: 1.7;
      max-width: 700px;
    }

    .work-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
      gap: 20px;
      margin-top: 40px;
      width: 100%;
    }

    .card {
      background: rgba(255,255,255,0.06);
      border-radius: 24px;
      padding: 24px;
      min-height: 180px;
      transform: translateY(80px);
      opacity: 0;
    }
  </style>
</head>
<body>
  <div class="progress-wrap"><div class="progress"></div></div>

  <nav>
    <button data-target="#hero">Home</button>
    <button data-target="#about">About</button>
    <button data-target="#work">Work</button>
    <button data-target="#contact">Contact</button>
  </nav>

  <section class="hero" id="hero">
    <img class="hero-image" src="https://picsum.photos/1600/1200" alt="hero image">
    <div class="hero-content">
      <h1>Creative Developer</h1>
      <p>Building cinematic web experiences with smooth scrolling and motion.</p>
    </div>
  </section>

  <section id="about">
    <div class="panel">
      <h2>About Me</h2>
      <p>I build interactive websites, smooth interfaces, and immersive front-end systems using modern animation tools.</p>
    </div>
  </section>

  <section id="work">
    <div class="panel">
      <h2>Selected Work</h2>
      <div class="work-grid">
        <div class="card">Project One</div>
        <div class="card">Project Two</div>
        <div class="card">Project Three</div>
        <div class="card">Project Four</div>
      </div>
    </div>
  </section>

  <section id="contact">
    <div class="panel">
      <h2>Contact</h2>
      <p>Email me for freelance work, collaborations, or product design partnerships.</p>
    </div>
  </section>

  <script type="module">
    import Lenis from 'https://unpkg.com/lenis@1.3.18/dist/lenis.min.js'
    import gsap from 'https://cdn.skypack.dev/gsap'
    import ScrollTrigger from 'https://cdn.skypack.dev/gsap/ScrollTrigger'

    gsap.registerPlugin(ScrollTrigger)

    const lenis = new Lenis({
      duration: 1.2,
      smoothWheel: true
    })

    lenis.on('scroll', ScrollTrigger.update)

    gsap.ticker.add((time) => {
      lenis.raf(time * 1000)
    })

    gsap.ticker.lagSmoothing(0)

    const progress = document.querySelector('.progress')
    const heroImage = document.querySelector('.hero-image')

    lenis.on('scroll', ({ progress: p, scroll }) => {
      progress.style.transform = `scaleX(${p})`
      heroImage.style.transform = `translateY(${scroll * 0.15}px)`
    })

    document.querySelectorAll('nav button').forEach((button) => {
      button.addEventListener('click', () => {
        lenis.scrollTo(button.dataset.target, {
          duration: 1.2,
          offset: -20
        })
      })
    })

    gsap.utils.toArray('.card').forEach((card) => {
      gsap.to(card, {
        y: 0,
        opacity: 1,
        duration: 1,
        ease: 'power3.out',
        scrollTrigger: {
          trigger: card,
          start: 'top 85%'
        }
      })
    })
  </script>
</body>
</html>
```

## Why this is a real project example

This combines the most common production patterns:

* global smooth scroll
* scroll progress UI
* hero parallax
* animated section navigation
* reveal cards on scroll
* GSAP + Lenis sync

That is very close to how many portfolio and agency sites are structured.

---

# 21) Simple mental model to remember

Think of Lenis like this:

* **native scroll** = raw movement
* **Lenis** = animated movement layer
* **GSAP / transforms** = visual effects driven by that movement

So the workflow becomes:

1. initialize Lenis
2. feed it every frame with `raf`
3. listen to scroll events or call `scrollTo`
4. connect animations to the resulting smooth scroll state

---

# 22) Best starter config

If you are just learning, start here:

```js
const lenis = new Lenis({
  duration: 1.1,
  smoothWheel: true,
  syncTouch: false
})

function raf(time) {
  lenis.raf(time)
  requestAnimationFrame(raf)
}
requestAnimationFrame(raf)
```

Then gradually add:

* `scrollTo()`
* progress bar
* parallax
* GSAP integration
* React wrapper

---

# 23) Final advice

Do not start with a giant complex animation site. Learn Lenis in this order:

1. basic setup
2. `raf`
3. `scrollTo`
4. `on('scroll')`
5. progress bar
6. parallax
7. GSAP integration
8. React / Next integration

That path will make the library feel very easy.

If you want, I can give you a **Lenis full roadmap from beginner to advanced**, or build a **complete React + GSAP + Lenis portfolio project** step by step.

[1]: https://github.com/darkroomengineering/lenis?utm_source=chatgpt.com "darkroomengineering/lenis: Smooth scroll at it should be"
[2]: https://lenis.darkroom.engineering/?utm_source=chatgpt.com "Lenis – Get smooth or die trying"
[3]: https://github.com/darkroomengineering/lenis/blob/main/packages/react/README.md?utm_source=chatgpt.com "lenis/packages/react/README.md at main"
[4]: https://github.com/jongsin/lib-animations-lenis?utm_source=chatgpt.com "jongsin/lib-animations-lenis: How smooth scroll should be"
[5]: https://github.com/darkroomengineering/lenis/discussions/292?utm_source=chatgpt.com "How to stop scroll on modal pop-up & keep smooth ..."
