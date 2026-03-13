Here’s a **start-to-end guide to Framer Motion** for React.

One important update first: the library is now documented under **Motion for React** on `motion.dev`, and the docs describe it as **“Motion for React (previously Framer Motion)”**. So you’ll still hear people say *Framer Motion*, but current docs and package usage are centered around Motion. ([Motion][1])

---

# 1) What Framer Motion is

Framer Motion is a React animation library for building UI animations like fades, slides, gestures, layout transitions, scroll effects, and exit animations. Its core idea is simple: replace normal elements like `div` with `motion.div`, then add animation props such as `initial`, `animate`, `exit`, `whileHover`, and `transition`. The docs also note that motion components can animate at high performance without causing React re-renders for every frame. ([Motion][1])

You use it when you want:

* smoother UI interactions
* less CSS keyframe boilerplate
* easy mount/unmount animations
* drag and gesture support
* layout animations when components move or resize

---

# 2) Install and first setup

## Install

```bash
npm install motion
```

For React, import from `motion/react`: ([Motion][1])

```jsx
import { motion } from "motion/react";
```

## Your first animation

```jsx
import React from "react";
import { motion } from "motion/react";

export default function App() {
  return (
    <div style={{ padding: 40 }}>
      <motion.div
        initial={{ opacity: 0, y: 40 }}
        animate={{ opacity: 1, y: 0 }}
        transition={{ duration: 0.6 }}
        style={{
          width: 200,
          height: 100,
          borderRadius: 16,
          background: "#111",
        }}
      />
    </div>
  );
}
```

## How this works

* `initial`: starting state
* `animate`: target state
* `transition`: how to move from start to target

This prop-based animation style is one of the main entry points in the official React guide. ([Motion][1])

---

# 3) Core mental model

Think of Framer Motion as 5 main layers:

1. **Basic animation**
2. **Transitions**
3. **Variants**
4. **Presence and layout animation**
5. **Gestures / scroll / advanced orchestration**

If you master these, you can build almost every real UI animation.

---

# 4) The motion component

There is a `motion` version of HTML and SVG elements, like:

```jsx
<motion.div />
<motion.button />
<motion.h1 />
<motion.img />
<motion.svg />
<motion.circle />
```

The motion component extends normal elements with animation props and gesture props. ([Motion][2])

Example:

```jsx
import { motion } from "motion/react";

export default function Card() {
  return (
    <motion.button
      whileHover={{ scale: 1.05 }}
      whileTap={{ scale: 0.96 }}
      style={{
        padding: "16px 24px",
        borderRadius: 12,
        border: "none",
        background: "black",
        color: "white",
        cursor: "pointer",
      }}
    >
      Click me
    </motion.button>
  );
}
```

---

# 5) Basic animation props

## `initial`

The starting style before animation begins.

## `animate`

The style to animate to.

## `exit`

The style to animate to when component leaves the tree. Used with `AnimatePresence`. ([Motion][3])

## Example

```jsx
<motion.div
  initial={{ opacity: 0, scale: 0.8 }}
  animate={{ opacity: 1, scale: 1 }}
  exit={{ opacity: 0, scale: 0.8 }}
/>
```

## Animatable values

You can animate many kinds of values:

* opacity
* x, y
* scale
* rotate
* borderRadius
* backgroundColor
* width/height
* SVG properties

Motion also supports independent transforms like `x`, `y`, and `rotateZ` without needing extra wrapper elements. ([Motion][4])

Example:

```jsx
<motion.div
  animate={{
    x: 120,
    rotate: 180,
    scale: 1.2,
  }}
/>
```

---

# 6) Transition system

Motion supports three main transition types: **tween**, **spring**, and **inertia**. The official docs explain that tweens are duration/easing based, springs can be physics-based, and inertia is useful for momentum-like movement. ([Motion][5])

## Tween

```jsx
<motion.div
  animate={{ x: 200 }}
  transition={{
    type: "tween",
    duration: 0.8,
    ease: "easeInOut",
  }}
/>
```

Use tween when:

* you want precise timing
* hero sections
* intro animations
* loader sequences

## Spring

```jsx
<motion.div
  animate={{ x: 200 }}
  transition={{
    type: "spring",
    stiffness: 120,
    damping: 12,
    mass: 1,
  }}
/>
```

Use spring when:

* buttons
* cards
* toggles
* menus
* natural-feeling UI interactions

## Inertia

```jsx
<motion.div
  drag
  dragConstraints={{ left: 0, right: 300 }}
  dragTransition={{
    power: 0.2,
    timeConstant: 200,
    modifyTarget: (target) => Math.round(target / 50) * 50,
  }}
/>
```

Use inertia when:

* draggable sliders
* momentum-based UI
* snapping interactions

---

# 7) Keyframes

You can animate through multiple values:

```jsx
<motion.div
  animate={{
    scale: [1, 1.2, 1, 1.3, 1],
    rotate: [0, 0, 180, 180, 0],
  }}
  transition={{
    duration: 2,
    repeat: Infinity,
  }}
/>
```

This is useful for:

* pulsing CTA buttons
* attention indicators
* playful loading states

---

# 8) Variants: the most important concept for bigger projects

When your animation gets bigger, don’t write everything inline. Use **variants**.

Variants let you define named states and reuse them across many elements.

```jsx
import { motion } from "motion/react";

const boxVariants = {
  hidden: { opacity: 0, y: 40 },
  visible: {
    opacity: 1,
    y: 0,
    transition: {
      duration: 0.5,
    },
  },
};

export default function Example() {
  return (
    <motion.div
      variants={boxVariants}
      initial="hidden"
      animate="visible"
      style={{
        width: 200,
        height: 100,
        background: "tomato",
        borderRadius: 16,
      }}
    />
  );
}
```

## Why variants matter

Variants help with:

* keeping code clean
* orchestrating parent/child animations
* building reusable animation systems
* making large apps maintainable

## Parent-child orchestration

```jsx
import { motion } from "motion/react";

const container = {
  hidden: {},
  show: {
    transition: {
      staggerChildren: 0.15,
      delayChildren: 0.2,
    },
  },
};

const item = {
  hidden: { opacity: 0, y: 20 },
  show: { opacity: 1, y: 0 },
};

export default function StaggerList() {
  const items = ["HTML", "CSS", "JavaScript", "React"];

  return (
    <motion.ul
      variants={container}
      initial="hidden"
      animate="show"
      style={{ listStyle: "none", padding: 0 }}
    >
      {items.map((text) => (
        <motion.li
          key={text}
          variants={item}
          style={{
            padding: 12,
            marginBottom: 12,
            background: "#eee",
            borderRadius: 10,
          }}
        >
          {text}
        </motion.li>
      ))}
    </motion.ul>
  );
}
```

This pattern is used everywhere in real apps:

* staggered feature sections
* animated nav menus
* dashboard cards
* onboarding flows

---

# 9) Hover, tap, focus, drag, in-view gestures

The official docs list support for gestures like **hover, tap, pan, drag, focus, and inView**, with both event listeners and `while-` animation props. ([Motion][6])

## Hover

```jsx
<motion.button
  whileHover={{ scale: 1.08, y: -2 }}
  whileTap={{ scale: 0.95 }}
>
  Buy now
</motion.button>
```

## Focus

```jsx
<motion.input
  whileFocus={{ scale: 1.02 }}
  style={{ padding: 12, borderRadius: 10 }}
/>
```

## Drag

```jsx
<motion.div
  drag
  whileDrag={{ scale: 1.1 }}
  style={{
    width: 120,
    height: 120,
    borderRadius: 20,
    background: "royalblue",
    cursor: "grab",
  }}
/>
```

## Drag constraints

```jsx
import { useRef } from "react";
import { motion } from "motion/react";

export default function DragBox() {
  const ref = useRef(null);

  return (
    <div
      ref={ref}
      style={{
        width: 400,
        height: 200,
        border: "2px dashed #999",
        position: "relative",
      }}
    >
      <motion.div
        drag
        dragConstraints={ref}
        dragElastic={0.2}
        style={{
          width: 80,
          height: 80,
          borderRadius: 16,
          background: "tomato",
        }}
      />
    </div>
  );
}
```

Use drag for:

* sliders
* kanban cards
* mobile carousels
* sortable UIs

---

# 10) AnimatePresence for mount/unmount animation

React normally removes components instantly. `AnimatePresence` lets them animate out with `exit`. The docs also mention modes like `"wait"` and note that immediate children need stable unique `key` props for exit animations to work correctly. ([Motion][3])

## Modal example

```jsx
import React, { useState } from "react";
import { motion, AnimatePresence } from "motion/react";

export default function App() {
  const [open, setOpen] = useState(false);

  return (
    <div style={{ padding: 40 }}>
      <button onClick={() => setOpen(true)}>Open modal</button>

      <AnimatePresence>
        {open && (
          <motion.div
            key="backdrop"
            initial={{ opacity: 0 }}
            animate={{ opacity: 1 }}
            exit={{ opacity: 0 }}
            style={{
              position: "fixed",
              inset: 0,
              background: "rgba(0,0,0,0.5)",
              display: "grid",
              placeItems: "center",
            }}
            onClick={() => setOpen(false)}
          >
            <motion.div
              key="modal"
              initial={{ scale: 0.8, opacity: 0, y: 30 }}
              animate={{ scale: 1, opacity: 1, y: 0 }}
              exit={{ scale: 0.8, opacity: 0, y: 30 }}
              transition={{ type: "spring", stiffness: 220, damping: 20 }}
              onClick={(e) => e.stopPropagation()}
              style={{
                width: 320,
                padding: 24,
                borderRadius: 20,
                background: "white",
              }}
            >
              <h2>Delete item?</h2>
              <p>This action cannot be undone.</p>
              <button onClick={() => setOpen(false)}>Close</button>
            </motion.div>
          </motion.div>
        )}
      </AnimatePresence>
    </div>
  );
}
```

## `mode="wait"`

```jsx
<AnimatePresence mode="wait">
  {tab === "one" ? (
    <motion.div
      key="one"
      initial={{ opacity: 0, x: 30 }}
      animate={{ opacity: 1, x: 0 }}
      exit={{ opacity: 0, x: -30 }}
    >
      Tab One
    </motion.div>
  ) : (
    <motion.div
      key="two"
      initial={{ opacity: 0, x: 30 }}
      animate={{ opacity: 1, x: 0 }}
      exit={{ opacity: 0, x: -30 }}
    >
      Tab Two
    </motion.div>
  )}
</AnimatePresence>
```

Great for:

* tabs
* onboarding steps
* wizards
* slide-based UI

---

# 11) Layout animation

Motion can animate size and position changes with a single `layout` prop, and shared-element-style transitions with `layoutId`. ([Motion][7])

## Simple layout animation

```jsx
import React, { useState } from "react";
import { motion } from "motion/react";

export default function LayoutExample() {
  const [expanded, setExpanded] = useState(false);

  return (
    <motion.div
      layout
      onClick={() => setExpanded(!expanded)}
      style={{
        width: expanded ? 400 : 240,
        height: expanded ? 220 : 120,
        borderRadius: 20,
        background: "#111",
        color: "white",
        padding: 20,
        cursor: "pointer",
      }}
    >
      <motion.h2 layout>Project Card</motion.h2>
      <motion.p layout>
        Click to expand and animate layout smoothly.
      </motion.p>
    </motion.div>
  );
}
```

## Shared element transition with `layoutId`

```jsx
import React, { useState } from "react";
import { motion, AnimatePresence } from "motion/react";

const items = [
  { id: 1, title: "Mountain" },
  { id: 2, title: "Ocean" },
  { id: 3, title: "Forest" },
];

export default function Gallery() {
  const [selected, setSelected] = useState(null);

  return (
    <div style={{ padding: 30 }}>
      <div style={{ display: "flex", gap: 16 }}>
        {items.map((item) => (
          <motion.div
            key={item.id}
            layoutId={`card-${item.id}`}
            onClick={() => setSelected(item)}
            style={{
              width: 140,
              height: 100,
              borderRadius: 18,
              background: "#ddd",
              display: "grid",
              placeItems: "center",
              cursor: "pointer",
            }}
          >
            {item.title}
          </motion.div>
        ))}
      </div>

      <AnimatePresence>
        {selected && (
          <motion.div
            initial={{ opacity: 0 }}
            animate={{ opacity: 1 }}
            exit={{ opacity: 0 }}
            onClick={() => setSelected(null)}
            style={{
              position: "fixed",
              inset: 0,
              background: "rgba(0,0,0,0.5)",
              display: "grid",
              placeItems: "center",
            }}
          >
            <motion.div
              layoutId={`card-${selected.id}`}
              style={{
                width: 420,
                height: 260,
                borderRadius: 24,
                background: "white",
                display: "grid",
                placeItems: "center",
              }}
            >
              <h2>{selected.title}</h2>
            </motion.div>
          </motion.div>
        )}
      </AnimatePresence>
    </div>
  );
}
```

Real-world uses:

* product image expanders
* card-to-detail-page transitions
* music player mini-to-full transitions
* photo gallery zoom views

---

# 12) Scroll-triggered animation

A very common use case is reveal-on-scroll. Motion supports in-view style gesture features, and the docs cover scroll animations as part of the broader API. ([Motion][8])

## Simple reveal section

```jsx
import { motion } from "motion/react";

export default function Section() {
  return (
    <motion.section
      initial={{ opacity: 0, y: 60 }}
      whileInView={{ opacity: 1, y: 0 }}
      viewport={{ once: true, amount: 0.3 }}
      transition={{ duration: 0.6 }}
      style={{
        minHeight: "100vh",
        display: "grid",
        placeItems: "center",
      }}
    >
      <h2>Scroll reveal section</h2>
    </motion.section>
  );
}
```

Useful for:

* landing pages
* portfolio sections
* feature reveal blocks
* testimonials

---

# 13) Reusable animation wrappers

In real projects, don’t repeat animation props everywhere. Create wrappers.

## FadeUp component

```jsx
import { motion } from "motion/react";

export default function FadeUp({ children, delay = 0 }) {
  return (
    <motion.div
      initial={{ opacity: 0, y: 24 }}
      whileInView={{ opacity: 1, y: 0 }}
      viewport={{ once: true, amount: 0.2 }}
      transition={{ duration: 0.5, delay }}
    >
      {children}
    </motion.div>
  );
}
```

Usage:

```jsx
<FadeUp>
  <h2>Feature title</h2>
</FadeUp>
```

This is how animation systems become scalable in larger codebases.

---

# 14) Real project 1: Animated landing page hero

This combines:

* intro animation
* staggered children
* button hover/tap
* image float effect

```jsx
import React from "react";
import { motion } from "motion/react";

const container = {
  hidden: {},
  show: {
    transition: {
      staggerChildren: 0.18,
      delayChildren: 0.2,
    },
  },
};

const item = {
  hidden: { opacity: 0, y: 30 },
  show: { opacity: 1, y: 0, transition: { duration: 0.6 } },
};

export default function Hero() {
  return (
    <section
      style={{
        minHeight: "100vh",
        display: "grid",
        gridTemplateColumns: "1fr 1fr",
        alignItems: "center",
        padding: "60px",
        gap: "40px",
      }}
    >
      <motion.div variants={container} initial="hidden" animate="show">
        <motion.p variants={item} style={{ marginBottom: 12 }}>
          New generation UI
        </motion.p>

        <motion.h1
          variants={item}
          style={{
            fontSize: "clamp(2.5rem, 6vw, 5rem)",
            lineHeight: 1,
            marginBottom: 20,
          }}
        >
          Build beautiful interfaces with motion
        </motion.h1>

        <motion.p
          variants={item}
          style={{
            fontSize: "1.1rem",
            lineHeight: 1.6,
            marginBottom: 24,
            maxWidth: 520,
          }}
        >
          Add delightful animation to landing pages, products, and dashboards.
        </motion.p>

        <motion.div variants={item} style={{ display: "flex", gap: 16 }}>
          <motion.button
            whileHover={{ scale: 1.05, y: -2 }}
            whileTap={{ scale: 0.96 }}
            style={{
              padding: "14px 20px",
              borderRadius: 12,
              border: "none",
              background: "#111",
              color: "#fff",
              cursor: "pointer",
            }}
          >
            Get Started
          </motion.button>

          <motion.button
            whileHover={{ scale: 1.05 }}
            whileTap={{ scale: 0.96 }}
            style={{
              padding: "14px 20px",
              borderRadius: 12,
              background: "transparent",
              border: "1px solid #111",
              cursor: "pointer",
            }}
          >
            Live Demo
          </motion.button>
        </motion.div>
      </motion.div>

      <motion.div
        animate={{ y: [0, -12, 0] }}
        transition={{ duration: 3, repeat: Infinity, ease: "easeInOut" }}
        style={{
          width: "100%",
          height: 420,
          borderRadius: 28,
          background:
            "linear-gradient(135deg, rgba(110,86,255,1), rgba(51,194,255,1))",
        }}
      />
    </section>
  );
}
```

Why this is realistic:

* most SaaS landing pages do staggered content reveals
* CTA buttons use hover and tap feedback
* visual area often has a floating/looping effect

---

# 15) Real project 2: Animated mobile menu

```jsx
import React, { useState } from "react";
import { motion, AnimatePresence } from "motion/react";

const menuVariants = {
  closed: { x: "100%" },
  open: {
    x: 0,
    transition: {
      type: "spring",
      stiffness: 120,
      damping: 18,
      when: "beforeChildren",
      staggerChildren: 0.08,
      delayChildren: 0.1,
    },
  },
};

const itemVariants = {
  closed: { opacity: 0, x: 20 },
  open: { opacity: 1, x: 0 },
};

export default function MobileMenu() {
  const [open, setOpen] = useState(false);
  const links = ["Home", "About", "Projects", "Pricing", "Contact"];

  return (
    <div>
      <button onClick={() => setOpen(true)}>Menu</button>

      <AnimatePresence>
        {open && (
          <>
            <motion.div
              initial={{ opacity: 0 }}
              animate={{ opacity: 1 }}
              exit={{ opacity: 0 }}
              onClick={() => setOpen(false)}
              style={{
                position: "fixed",
                inset: 0,
                background: "rgba(0,0,0,0.4)",
              }}
            />

            <motion.aside
              variants={menuVariants}
              initial="closed"
              animate="open"
              exit="closed"
              style={{
                position: "fixed",
                top: 0,
                right: 0,
                width: 320,
                height: "100vh",
                background: "#111",
                color: "white",
                padding: 24,
              }}
            >
              <button onClick={() => setOpen(false)}>Close</button>

              <div style={{ marginTop: 30, display: "grid", gap: 16 }}>
                {links.map((link) => (
                  <motion.a
                    key={link}
                    variants={itemVariants}
                    href="#"
                    whileHover={{ x: 8 }}
                    style={{
                      color: "white",
                      textDecoration: "none",
                      fontSize: "1.4rem",
                    }}
                  >
                    {link}
                  </motion.a>
                ))}
              </div>
            </motion.aside>
          </>
        )}
      </AnimatePresence>
    </div>
  );
}
```

Real use:

* e-commerce side menus
* dashboard side drawers
* mobile navigation
* filter panels

---

# 16) Real project 3: Notification / toast system

```jsx
import React, { useState } from "react";
import { motion, AnimatePresence } from "motion/react";

export default function ToastDemo() {
  const [toasts, setToasts] = useState([]);

  const addToast = () => {
    const id = Date.now();
    setToasts((prev) => [...prev, { id, text: `Toast ${prev.length + 1}` }]);

    setTimeout(() => {
      setToasts((prev) => prev.filter((t) => t.id !== id));
    }, 3000);
  };

  return (
    <div style={{ padding: 40 }}>
      <button onClick={addToast}>Add toast</button>

      <div
        style={{
          position: "fixed",
          top: 20,
          right: 20,
          display: "grid",
          gap: 12,
        }}
      >
        <AnimatePresence>
          {toasts.map((toast) => (
            <motion.div
              key={toast.id}
              layout
              initial={{ opacity: 0, x: 100, scale: 0.9 }}
              animate={{ opacity: 1, x: 0, scale: 1 }}
              exit={{ opacity: 0, x: 100, scale: 0.9 }}
              transition={{ type: "spring", stiffness: 200, damping: 20 }}
              style={{
                padding: "14px 18px",
                borderRadius: 14,
                background: "#111",
                color: "white",
                minWidth: 220,
              }}
            >
              {toast.text}
            </motion.div>
          ))}
        </AnimatePresence>
      </div>
    </div>
  );
}
```

Why `layout` here matters:
when one toast leaves, the others smoothly reposition.

---

# 17) Real project 4: Expandable FAQ accordion

```jsx
import React, { useState } from "react";
import { motion, AnimatePresence } from "motion/react";

const data = [
  {
    id: 1,
    q: "What is Framer Motion?",
    a: "A React animation library for interactive UI.",
  },
  {
    id: 2,
    q: "Can it animate layout changes?",
    a: "Yes, with layout and layoutId.",
  },
  {
    id: 3,
    q: "Can it do exit animations?",
    a: "Yes, with AnimatePresence and exit.",
  },
];

export default function FAQ() {
  const [openId, setOpenId] = useState(null);

  return (
    <div style={{ maxWidth: 700, margin: "40px auto", display: "grid", gap: 16 }}>
      {data.map((item) => {
        const open = openId === item.id;

        return (
          <motion.div
            key={item.id}
            layout
            style={{
              border: "1px solid #ddd",
              borderRadius: 16,
              padding: 18,
            }}
          >
            <button
              onClick={() => setOpenId(open ? null : item.id)}
              style={{
                width: "100%",
                textAlign: "left",
                background: "none",
                border: "none",
                fontSize: "1.1rem",
                cursor: "pointer",
              }}
            >
              {item.q}
            </button>

            <AnimatePresence initial={false}>
              {open && (
                <motion.div
                  key="content"
                  initial={{ opacity: 0, height: 0 }}
                  animate={{ opacity: 1, height: "auto" }}
                  exit={{ opacity: 0, height: 0 }}
                  transition={{ duration: 0.25 }}
                  style={{ overflow: "hidden" }}
                >
                  <p style={{ paddingTop: 12 }}>{item.a}</p>
                </motion.div>
              )}
            </AnimatePresence>
          </motion.div>
        );
      })}
    </div>
  );
}
```

This pattern is everywhere in:

* FAQ sections
* pricing details
* dashboard detail rows
* settings pages

---

# 18) Combining Framer Motion with React state

Framer Motion becomes powerful when driven by state.

```jsx
import React, { useState } from "react";
import { motion } from "motion/react";

export default function Toggle() {
  const [on, setOn] = useState(false);

  return (
    <div
      onClick={() => setOn(!on)}
      style={{
        width: 80,
        height: 40,
        borderRadius: 999,
        background: on ? "#4ade80" : "#ddd",
        padding: 4,
        cursor: "pointer",
      }}
    >
      <motion.div
        animate={{ x: on ? 40 : 0 }}
        transition={{ type: "spring", stiffness: 500, damping: 30 }}
        style={{
          width: 32,
          height: 32,
          borderRadius: "50%",
          background: "white",
        }}
      />
    </div>
  );
}
```

This is a core real-world idea:
**React state decides what should happen, Motion decides how it moves.**

---

# 19) Best practices

## Use spring for interactive UI

Springs often feel more natural for buttons, cards, toggles, and panels. The official docs explicitly distinguish spring from tween and explain the physics-based controls like `stiffness`, `damping`, and `mass`. ([Motion][5])

## Use variants for large sections

Inline props are fine for small pieces, but variants are cleaner in big projects.

## Use `AnimatePresence` only when removing elements

If a component stays mounted and only changes style, use `animate`, not `exit`.

## Use stable keys

The docs warn that exit animations can fail if keys are unstable, especially if you use array indices in reorderable lists. ([Motion][3])

## Use `layout` for natural repositioning

Great for card lists, notifications, and accordions.

## Don’t animate everything

Too much motion hurts UX. Animate meaningful actions:

* entering content
* hover feedback
* state changes
* reordering
* opening/closing panels

---

# 20) Common mistakes

## Mistake 1: Exit animation not working

Wrong:

```jsx
{show && <motion.div exit={{ opacity: 0 }} />}
```

Correct:

```jsx
<AnimatePresence>
  {show && <motion.div exit={{ opacity: 0 }} />}
</AnimatePresence>
```

## Mistake 2: Missing `key`

Wrong:

```jsx
<AnimatePresence>
  {items.map((item, index) => (
    <motion.div key={index} exit={{ opacity: 0 }} />
  ))}
</AnimatePresence>
```

Better:

```jsx
<AnimatePresence>
  {items.map((item) => (
    <motion.div key={item.id} exit={{ opacity: 0 }} />
  ))}
</AnimatePresence>
```

This matches the official guidance around stable unique keys. ([Motion][3])

## Mistake 3: Using tween where spring feels better

For UI interactions, spring usually feels better.

## Mistake 4: Putting too much logic directly in JSX

Abstract repeated patterns into reusable components.

---

# 21) When to choose Framer Motion vs CSS vs GSAP

## Use CSS when:

* animation is tiny
* simple hover/fade
* no React state-driven choreography needed

## Use Framer Motion when:

* app is React-based
* UI interactions matter
* you need mount/unmount animation
* layout animation matters
* gestures and drag matter

## Use GSAP when:

* you need heavy timeline control
* very complex sequence animation
* canvas-like or highly choreographed effects

Framer Motion is especially strong for **component-driven UI animation**.

---

# 22) A mini project structure for real apps

A scalable folder structure might look like this:

```bash
src/
  components/
    animations/
      FadeUp.jsx
      ScaleIn.jsx
      StaggerContainer.jsx
    ui/
      Button.jsx
      Modal.jsx
      Accordion.jsx
  pages/
    Home.jsx
    Pricing.jsx
  lib/
    motionVariants.js
```

## Example shared variants file

```jsx
// lib/motionVariants.js
export const fadeUp = {
  hidden: { opacity: 0, y: 24 },
  show: {
    opacity: 1,
    y: 0,
    transition: { duration: 0.5 },
  },
};

export const staggerContainer = {
  hidden: {},
  show: {
    transition: {
      staggerChildren: 0.12,
    },
  },
};
```

Then reuse anywhere:

```jsx
import { motion } from "motion/react";
import { fadeUp, staggerContainer } from "./lib/motionVariants";
```

---

# 23) A full practical demo page

This demo combines:

* stagger intro
* cards with hover
* reveal on scroll
* modal with AnimatePresence
* layout animation

```jsx
import React, { useState } from "react";
import { motion, AnimatePresence } from "motion/react";

const container = {
  hidden: {},
  show: {
    transition: {
      staggerChildren: 0.15,
      delayChildren: 0.15,
    },
  },
};

const item = {
  hidden: { opacity: 0, y: 24 },
  show: { opacity: 1, y: 0, transition: { duration: 0.5 } },
};

const cards = [
  { id: 1, title: "Design Systems", desc: "Reusable components and consistency." },
  { id: 2, title: "Fast UI", desc: "Smooth, interactive, high-quality motion." },
  { id: 3, title: "Better UX", desc: "Feedback helps users understand actions." },
];

export default function App() {
  const [selected, setSelected] = useState(null);

  return (
    <div style={{ fontFamily: "sans-serif" }}>
      <section
        style={{
          minHeight: "100vh",
          padding: "80px 24px",
          display: "grid",
          placeItems: "center",
        }}
      >
        <motion.div
          variants={container}
          initial="hidden"
          animate="show"
          style={{ maxWidth: 900, width: "100%" }}
        >
          <motion.h1
            variants={item}
            style={{
              fontSize: "clamp(2.5rem, 7vw, 5rem)",
              marginBottom: 16,
            }}
          >
            Framer Motion Complete Demo
          </motion.h1>

          <motion.p variants={item} style={{ fontSize: "1.1rem", marginBottom: 32 }}>
            Learn how modern React interfaces become interactive and smooth.
          </motion.p>

          <motion.div variants={item}>
            <motion.button
              whileHover={{ scale: 1.05, y: -2 }}
              whileTap={{ scale: 0.96 }}
              style={{
                padding: "14px 20px",
                borderRadius: 12,
                border: "none",
                background: "#111",
                color: "white",
                cursor: "pointer",
              }}
            >
              Start Exploring
            </motion.button>
          </motion.div>
        </motion.div>
      </section>

      <section style={{ padding: "80px 24px" }}>
        <motion.div
          layout
          style={{
            display: "grid",
            gridTemplateColumns: "repeat(auto-fit, minmax(220px, 1fr))",
            gap: 20,
            maxWidth: 1100,
            margin: "0 auto",
          }}
        >
          {cards.map((card) => (
            <motion.div
              key={card.id}
              layout
              whileHover={{ y: -8, scale: 1.02 }}
              whileTap={{ scale: 0.98 }}
              onClick={() => setSelected(card)}
              initial={{ opacity: 0, y: 40 }}
              whileInView={{ opacity: 1, y: 0 }}
              viewport={{ once: true, amount: 0.2 }}
              transition={{ duration: 0.45 }}
              style={{
                padding: 20,
                borderRadius: 20,
                background: "#f3f3f3",
                cursor: "pointer",
              }}
            >
              <h3 style={{ marginBottom: 12 }}>{card.title}</h3>
              <p>{card.desc}</p>
            </motion.div>
          ))}
        </motion.div>
      </section>

      <AnimatePresence>
        {selected && (
          <motion.div
            initial={{ opacity: 0 }}
            animate={{ opacity: 1 }}
            exit={{ opacity: 0 }}
            onClick={() => setSelected(null)}
            style={{
              position: "fixed",
              inset: 0,
              background: "rgba(0,0,0,0.45)",
              display: "grid",
              placeItems: "center",
              padding: 20,
            }}
          >
            <motion.div
              initial={{ scale: 0.9, y: 20, opacity: 0 }}
              animate={{ scale: 1, y: 0, opacity: 1 }}
              exit={{ scale: 0.9, y: 20, opacity: 0 }}
              transition={{ type: "spring", stiffness: 220, damping: 20 }}
              onClick={(e) => e.stopPropagation()}
              style={{
                width: "min(500px, 100%)",
                background: "white",
                borderRadius: 24,
                padding: 24,
              }}
            >
              <h2 style={{ marginBottom: 12 }}>{selected.title}</h2>
              <p style={{ marginBottom: 20 }}>{selected.desc}</p>
              <motion.button
                whileHover={{ scale: 1.04 }}
                whileTap={{ scale: 0.97 }}
                onClick={() => setSelected(null)}
                style={{
                  padding: "12px 18px",
                  borderRadius: 12,
                  border: "none",
                  background: "#111",
                  color: "white",
                  cursor: "pointer",
                }}
              >
                Close
              </motion.button>
            </motion.div>
          </motion.div>
        )}
      </AnimatePresence>
    </div>
  );
}
```

---

# 24) Learning roadmap

A practical order to learn Framer Motion:

1. `motion.div`
2. `initial`, `animate`, `transition`
3. `whileHover`, `whileTap`
4. keyframes
5. variants
6. staggered children
7. `AnimatePresence`
8. `layout`
9. `layoutId`
10. scroll/in-view animation
11. reusable wrappers
12. full-page UI projects

---

# 25) Quick cheat sheet

## Most-used props

```jsx
<motion.div
  initial={{ opacity: 0 }}
  animate={{ opacity: 1 }}
  exit={{ opacity: 0 }}
  transition={{ duration: 0.4 }}
  whileHover={{ scale: 1.05 }}
  whileTap={{ scale: 0.95 }}
  whileInView={{ y: 0, opacity: 1 }}
  viewport={{ once: true }}
  layout
  drag
/>
```

## Most-used imports

```jsx
import { motion, AnimatePresence } from "motion/react";
```

## Most-used patterns

* fade up on load
* stagger section children
* hover scale button
* modal exit animation
* accordion expand/collapse
* card layout transitions
* toast notifications
* mobile side menu

---

# 26) Final advice

If you are building **React apps**, Framer Motion is one of the best tools for **UI-driven animation** because it fits naturally with component state, supports gestures, handles exit animation with `AnimatePresence`, and supports automatic layout animation with `layout` and shared transitions with `layoutId`. Those are all core parts of the official current Motion docs. ([Motion][9])

For real projects:

* use **spring** for interactive elements
* use **variants** for sections and repeated patterns
* use **AnimatePresence** for modals, drawers, toasts, tabs
* use **layout** for accordions, cards, and reordering
* keep motion purposeful, not decorative

If you want, I can turn this into a **3-project Framer Motion practice roadmap** with:

1. landing page,
2. dashboard UI,
3. portfolio website.

[1]: https://motion.dev/docs/react?utm_source=chatgpt.com "Motion for React: Get started - React Animation Library"
[2]: https://motion.dev/docs/react-motion-component?utm_source=chatgpt.com "Motion component - React"
[3]: https://motion.dev/docs/react-animate-presence?utm_source=chatgpt.com "AnimatePresence — React exit animations - Motion.dev"
[4]: https://motion.dev/?utm_source=chatgpt.com "Motion.dev"
[5]: https://motion.dev/docs/react-transitions?utm_source=chatgpt.com "React transitions — Configure Motion animations"
[6]: https://motion.dev/docs/react-gestures?utm_source=chatgpt.com "React gesture animations — hover, drag, press - Motion.dev"
[7]: https://motion.dev/docs/react-layout-animations?utm_source=chatgpt.com "Layout Animation — React FLIP & Shared Element - Motion"
[8]: https://motion.dev/docs?utm_source=chatgpt.com "Motion Documentation (prev Framer Motion)"
[9]: https://motion.dev/docs/react-animation?utm_source=chatgpt.com "React Animation | Keyframes, Transitions & Gestures | Motion"
