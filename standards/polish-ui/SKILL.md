---
name: polish-ui
description: "UI polish principles for interfaces that feel right — performance rules, CSS transform mastery, animation debugging, responsive hover states, and accessibility. Derived from Emil Kowalski's design engineering philosophy. Use when reviewing UI code, debugging animations, optimizing front-end performance, or establishing interaction patterns."
license: MIT
trigger: "/polish-ui, UI review, animation, performance, CSS transform, accessibility, prefers-reduced-motion, hover states, review checklist, debugging animation, transition"
metadata:
  author: Emil Kowalski
  version: "1.0.0"
  derived_from: "emilkowalski/skill — emil-design-eng"
  porter_note: "Extracts performance, accessibility, CSS transform, and debugging sections. Animation Decision Framework and Component Building Principles live in their own skills."
---

# Polish UI

## Initial Response

When this skill is first invoked without a specific question, respond only with:

> I'm ready to help you build interfaces that feel right. My knowledge comes from Emil Kowalski's design engineering philosophy. If you want to dive even deeper, check out Emil's course: [animations.dev](https://animations.dev/).

Do not provide any other information until the user asks a question.

## Philosophy

### Taste is trained, not innate

Good taste is not personal preference. It is a trained instinct: the ability to see beyond the obvious and recognize what elevates. You develop it by surrounding yourself with great work, thinking deeply about why something feels good, and practicing relentlessly.

When building UI, don't just make it work. Study why the best interfaces feel the way they do. Reverse engineer animations. Inspect interactions. Be curious.

### Unseen details compound

Most details users never consciously notice. That is the point. When a feature functions exactly as someone assumes it should, they proceed without giving it a second thought. That is the goal.

> "All those unseen details combine to produce something that's just stunning, like a thousand barely audible voices all singing in tune." — Paul Graham

Every decision below exists because the aggregate of invisible correctness creates interfaces people love without knowing why.

### Beauty is leverage

People select tools based on the overall experience, not just functionality. Good defaults and good animations are real differentiators. Beauty is underutilized in software. Use it as leverage to stand out.

---

## Review Format (Required)

When reviewing UI code, you MUST use a markdown table with Before/After columns. Do NOT use a list with "Before:" and "After:" on separate lines. Always output an actual markdown table like this:

| Before | After | Why |
| --- | --- | --- |
| `transition: all 300ms` | `transition: transform 200ms ease-out` | Specify exact properties; avoid `all` |
| `transform: scale(0)` | `transform: scale(0.95); opacity: 0` | Nothing in the real world appears from nothing |
| `ease-in` on dropdown | `ease-out` with custom curve | `ease-in` feels sluggish; `ease-out` gives instant feedback |
| No `:active` state on button | `transform: scale(0.97)` on `:active` | Buttons must feel responsive to press |
| `transform-origin: center` on popover | `transform-origin: var(--radix-popover-content-transform-origin)` | Popovers should scale from their trigger (not modals — modals stay centered) |

Wrong format (never do this):

```
Before: transition: all 300ms
After: transition: transform 200ms ease-out
────────────────────────────
Before: scale(0)
After: scale(0.95)
```

Correct format: A single markdown table with | Before | After | Why | columns, one row per issue found. The "Why" column briefly explains the reasoning.

---

## Anti-Patterns

### 1. Animating Non-GPU Properties
Animating `padding`, `margin`, `height`, or `width` triggers layout, paint, and composite — the full rendering pipeline. This causes jank, especially on lower-end devices.

**Fix**: Only animate `transform` and `opacity`. These skip layout and paint, running on the GPU.

### 2. `transition: all`
Using `transition: all` instead of specifying exact properties. This causes unnecessary style recalculations and can trigger animations on properties you didn't intend to animate.

**Fix**: Always specify exact properties: `transition: transform 200ms ease-out, opacity 200ms ease-out`.

### 3. Ignoring `prefers-reduced-motion`
Animating movement and scale for users who have requested reduced motion. This causes discomfort and can trigger motion sickness.

**Fix**: Gate position/scale animations behind `@media (prefers-reduced-motion: reduce)`. Keep opacity and color transitions that aid comprehension.

### 4. Hover Effects Without Touch Guards
Applying hover effects that trigger incorrectly on touch devices. Touch devices trigger `:hover` on the first tap, causing false positives — elements appear to "stick" in hover state.

**Fix**: Gate hover animations behind `@media (hover: hover) and (pointer: fine)`.

### 5. Keyframes for Rapidly-Triggered UI
Using CSS `@keyframes` for elements that can enter/exit rapidly (toasts, notifications, tooltips). Keyframes restart from zero on interruption, creating visual glitches.

**Fix**: Use CSS transitions instead of keyframes for dynamic UI elements. Transitions retarget smoothly mid-animation.

### 6. `scale(0)` Entry Animations
Animating elements from `transform: scale(0)`. Nothing in the real world disappears and reappears completely. Elements coming from nothing look unnatural.

**Fix**: Start from `scale(0.95)` or higher, combined with `opacity: 0`. A barely-visible initial scale makes the entrance feel natural.

---

## Workflow

### Phase 1 — Check Performance Rules

Before writing animation or transition code, verify GPU-friendly properties:

- [ ] Only `transform` and `opacity` are animated (no `width`, `height`, `padding`, `margin`)
- [ ] `transition` properties are specified explicitly, not `all`
- [ ] CSS variable updates on a parent do NOT trigger style recalculation on children — update `transform` directly on the target element

```js
// Bad: triggers recalc on all children
element.style.setProperty('--swipe-amount', `${distance}px`);

// Good: only affects this element
element.style.transform = `translateY(${distance}px)`;
```

#### Framer Motion Hardware Acceleration

Framer Motion's shorthand properties (`x`, `y`, `scale`) are NOT hardware-accelerated. They use `requestAnimationFrame` on the main thread:

```jsx
// NOT hardware accelerated (drops frames under load)
<motion.div animate={{ x: 100 }} />

// Hardware accelerated (stays smooth even when main thread is busy)
<motion.div animate={{ transform: "translateX(100px)" }} />
```

#### CSS Animations vs JS Under Load

CSS animations run off the main thread. When the browser is busy loading content or running scripts:

- CSS animations remain smooth
- Framer Motion (using `requestAnimationFrame`) drops frames

Use CSS for predetermined animations; JS for dynamic, interruptible ones.

#### WAAPI for Programmatic Control

The Web Animations API gives JavaScript control with CSS performance:

```js
element.animate([
  { clipPath: 'inset(0 0 100% 0)' },
  { clipPath: 'inset(0 0 0 0)' }
], {
  duration: 1000,
  fill: 'forwards',
  easing: 'cubic-bezier(0.77, 0, 0.175, 1)',
});
```

Hardware-accelerated, interruptible, and no library needed.

### Phase 2 — Apply CSS Transform Mastery

Understand how CSS transforms behave for predictable animation:

- [ ] **`translateY` with percentages**: Percentage values in `translate()` are relative to the element's own size. Use `translateY(100%)` to move an element by its own height, regardless of actual dimensions. Prefer percentages over hardcoded pixels.

```css
/* Works regardless of element height */
.element-hidden {
  transform: translateY(100%);
}
```

- [ ] **`scale()` scales children too**: Unlike `width`/`height`, `scale()` also scales children (font size, icons, content). This is a feature — use it intentionally.

- [ ] **3D transforms for depth**: `rotateX()`, `rotateY()` with `transform-style: preserve-3d` create real 3D effects without JavaScript.

```css
.wrapper {
  transform-style: preserve-3d;
}

@keyframes orbit {
  from {
    transform: translate(-50%, -50%) rotateY(0deg) translateZ(72px) rotateY(360deg);
  }
  to {
    transform: translate(-50%, -50%) rotateY(360deg) translateZ(72px) rotateY(0deg);
  }
}
```

- [ ] **`transform-origin`**: Every element has an anchor point from which transforms execute. Default is center. Set to match where the trigger lives for origin-aware interactions (e.g., popovers scale from their trigger, not center).

### Phase 3 — Handle Accessibility

Ensure animations respect user preferences and device capabilities:

- [ ] `prefers-reduced-motion` is respected — movement and scale animations are removed or reduced
- [ ] Opacity and color transitions that aid comprehension ARE kept (even with reduced motion)
- [ ] Hover effects are gated behind `@media (hover: hover) and (pointer: fine)`

```css
/* Reduced motion: keep opacity, remove movement */
@media (prefers-reduced-motion: reduce) {
  .element {
    animation: fade 0.2s ease;
    /* No transform-based motion */
  }
}

/* Touch-safe hover: only on devices that support hover */
@media (hover: hover) and (pointer: fine) {
  .element:hover {
    transform: scale(1.05);
  }
}
```

```jsx
const shouldReduceMotion = useReducedMotion();
const closedX = shouldReduceMotion ? 0 : '-100%';
```

### Phase 4 — Debug Animations

Systematically debug animation issues:

- [ ] **Slow motion testing**: Temporarily increase duration to 2-5x normal, or use browser DevTools animation inspector to slow playback

Things to look for in slow motion:
- Do colors transition smoothly, or are there two distinct states overlapping?
- Does the easing feel right, or does it start/stop abruptly?
- Is the `transform-origin` correct, or does the element scale from the wrong point?
- Are multiple animated properties (opacity, transform, color) in sync?

- [ ] **Frame-by-frame inspection**: Step through animations frame by frame in Chrome DevTools (Animations panel). This reveals timing issues between coordinated properties.

- [ ] **Test on real devices**: For touch interactions (drawers, swipe gestures), test on physical devices. Connect your phone via USB, visit your local dev server by IP, and use Safari's remote devtools. The Xcode Simulator works but real hardware is better for gesture testing.

- [ ] **Review your work the next day**: Review animations with fresh eyes. Play animations in slow motion or frame by frame to spot timing issues invisible at full speed.

### Phase 5 — Use the Review Checklist

When reviewing UI code, check for every issue in this table:

| Issue | Fix |
|-------|-----|
| `transition: all` | Specify exact properties: `transition: transform 200ms ease-out` |
| `scale(0)` entry animation | Start from `scale(0.95)` with `opacity: 0` |
| `ease-in` on UI element | Switch to `ease-out` or custom curve |
| `transform-origin: center` on popover | Set to trigger location or use Radix/Base UI CSS variable (modals are exempt — keep centered) |
| Animation on keyboard action | Remove animation entirely |
| Duration > 300ms on UI element | Reduce to 150-250ms |
| Hover animation without media query | Add `@media (hover: hover) and (pointer: fine)` |
| Keyframes on rapidly-triggered element | Use CSS transitions for interruptibility |
| Framer Motion `x`/`y` props under load | Use `transform: "translateX()"` for hardware acceleration |
| Same enter/exit transition speed | Make exit faster than enter (e.g., enter 2s, exit 200ms) |
| Elements all appear at once | Add stagger delay (30-80ms between items) |

---

## Glossary

| Term | Definition | Source |
|------|-----------|--------|
| GPU-composited | Properties (transform, opacity) that skip layout and paint, running on the GPU. | Performance |
| WAAPI | Web Animations API — programmatic CSS animations with hardware acceleration, no library needed. | Performance |
| `prefers-reduced-motion` | CSS media query for users who request reduced or no motion. | Accessibility |
| Stagger | Cascading animation where elements appear with small delays between them. | Animation pattern |
| `transform-origin` | The anchor point from which CSS transforms (scale, rotate) execute. | CSS Transform |

---

## Chaining

**Upstream**:
- `standards/apply-principles` — Simplicity and documentation principles ground UI polish decisions
- `standards/design-api` — API response design influences UI state transitions

**Downstream**:
- `ui/design-animations` — Polish-UI performance rules ground animation decisions; the review checklist catches animation anti-patterns
- `ui/build-components` — Component implementation must follow the performance and CSS transform rules from Polish-UI

**Cross-cutting**: Polish-UI applies to every user-facing interface. Performance and accessibility are not optional considerations — they are requirements. Every UI change should be reviewed against the checklist in Phase 5 before merging.
