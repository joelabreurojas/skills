---
name: build-components
description: "Component building principles, gesture interactions, and the Sonner approach to developer experience. Use when implementing UI components, designing interaction patterns, adding drag gestures, or reviewing component code."
license: MIT
trigger: "/build-components, component, UI component, gesture, drag, swipe, toast, popover, tooltip, drawer, button, interaction design, component review, Sonner"
metadata:
  author: Emil Kowalski
  version: "1.0.0"
  derived_from: "emilkowalski/skill — emil-design-eng"
  porter_note: "Companion to ui/design-animations (animation decisions) and standards/polish-ui (performance, CSS transforms, debugging, accessibility). The three skills together fully cover Emil Kowalski's design engineering philosophy."
---

# Build Components

## Philosophy

### Every pixel is an argument

A button is never "just a button." It communicates affordance, states (rest, hover, active, disabled), and feedback. A popover's origin tells the user where it came from. A toast's entry direction establishes spatial memory.

Components are the vocabulary of your design system. Every detail — the ease curve, the scale factor, the delay before a tooltip appears — makes an argument about what this component is and how it behaves. Be deliberate about each one.

### Developer experience IS user experience

If a component is hard to adopt, developers will use it wrong or not at all. The best component disappears: insert `<Toaster />` once, call `toast()` from anywhere, and never think about it again.

No hooks, no context providers wrapping the tree, no complex setup. The API surface should be minimal because the implementation handles everything internally. Good defaults mean most users never need to configure anything.

### Invisible correctness compounds

Users never notice that toast timers pause when the tab is hidden. They never notice that drag continues even when the pointer leaves the element. They never notice that stacking toasts fill gaps with pseudo-elements to preserve hover state.

That is the point. These details are invisible when they work correctly and glaring when they do not. The aggregate of invisible correctness creates interfaces people trust.

---

## Anti-Patterns

### 1. `transform: scale(0)` Entry Animations

Nothing in the real world disappears and reappears completely. Elements animating from `scale(0)` look like they come out of nowhere. It breaks the user's spatial understanding.

**Fix**: Start from `scale(0.95)` or higher combined with `opacity: 0`. A barely-visible initial scale makes the entrance feel natural, like a balloon that has a visible shape even when deflated.

### 2. `transform-origin: center` on All Popovers

Popovers must scale in from their trigger, not from the center of the viewport. Default `transform-origin: center` is wrong for almost every anchored component.

**Fix**: Use the framework's origin variable: `var(--radix-popover-content-transform-origin)` for Radix UI, `var(--transform-origin)` for Base UI. **Exception**: Modals stay centered — they are not anchored to a trigger.

### 3. Keyframes for Rapidly-Triggered Components

Using CSS `@keyframes` for toasts, notifications, or tooltips that can enter/exit rapidly. Keyframes restart from zero on interruption, creating visual glitches and canceling in-progress motion.

**Fix**: Use CSS transitions instead. Transitions retarget smoothly mid-animation, preserving the current interpolated state.

### 4. Same Animation for Every Enter/Exit

Applying a one-size-fits-all animation to every tooltip, dropdown, and popover. A tooltip needs a 125ms subtle scale-and-fade. A dropdown needs a 200ms stronger reveal. A modal needs a distinct 300ms transition. Uniformity kills character.

**Fix**: Match the animation personality to the component's role and size.

---

## Workflow

### Phase 1 — Apply Component Building Principles

Check each component against these principles:

- [ ] **Buttons feel responsive**: Add `transform: scale(0.97)` on `:active`. This gives instant press feedback. Any pressable element benefits from this. Keep the scale subtle (0.95-0.98).

```css
.button {
  transition: transform 160ms ease-out;
}
.button:active {
  transform: scale(0.97);
}
```

- [ ] **Popovers are origin-aware**: Set `transform-origin` to the trigger location, not center. This makes the popover feel physically connected to what opened it.

```css
.popover {
  transform-origin: var(--radix-popover-content-transform-origin);
}
```

- [ ] **Tooltips skip delay on subsequent hovers**: Delay the first tooltip to prevent accidental activation. Once a tooltip is open, hover over adjacent tools should be instant with no animation.

```css
.tooltip[data-starting-style],
.tooltip[data-ending-style] {
  opacity: 0;
  transform: scale(0.97);
}
.tooltip[data-instant] {
  transition-duration: 0ms;
}
```

- [ ] **Use CSS transitions over keyframes for dynamic UI**: Keyframes restart from zero. Transitions retarget smoothly. For toasts, notifications, and any component triggered rapidly, transitions win.

- [ ] **Use blur to mask imperfect transitions**: When a crossfade between two states feels off despite trying different easings, add subtle `filter: blur(2px)` during the transition. The blur bridges two distinct objects into one smooth transformation — tricking the eye. Keep blur under 20px (heavy blur is expensive, especially in Safari).

```css
.button-content.transitioning {
  filter: blur(2px);
  opacity: 0.7;
}
```

- [ ] **Use `@starting-style` for enter animations**: The modern CSS way to animate element entry without JavaScript. Replaces the `useEffect` + `mounted` pattern.

```css
.toast {
  opacity: 1;
  transform: translateY(0);
  transition: opacity 400ms ease, transform 400ms ease;

  @starting-style {
    opacity: 0;
    transform: translateY(100%);
  }
}
```

### Phase 2 — Apply the Sonner Principles

These principles come from building Sonner (13M+ weekly npm downloads) and apply to any component:

- [ ] **Developer experience is key**: No hooks, no context, no complex setup. The API surface should be minimal. `<Component />` once, call from anywhere.

- [ ] **Good defaults matter more than options**: Ship beautiful out of the box. Most users never customize. The default easing, timing, and visual design must be excellent.

- [ ] **Naming creates identity**: "Sonner" (French for "to ring") feels more elegant than "react-toast." Sacrifice discoverability for memorability when appropriate.

- [ ] **Handle edge cases invisibly**:
  - Pause timers when the tab is hidden (`document.hidden` / `visibilitychange`)
  - Fill gaps between stacked items with pseudo-elements to maintain hover state
  - Capture pointer events during drag
  - Users never notice these — that is exactly right

- [ ] **Use transitions, not keyframes, for dynamic UI**: Toasts are added rapidly. Keyframes restart from zero on interruption. Transitions retarget smoothly.

- [ ] **Build a great documentation site**: Let people touch the product, play with it, and understand it before they use it. Interactive examples with ready-to-use code snippets lower the adoption barrier.

### Phase 3 — Implement Gesture and Drag Interactions

For drawers, swipe-to-dismiss, and drag interactions:

- [ ] **Momentum-based dismissal**: Don't require dragging past a fixed threshold. Calculate velocity and dismiss on quick flick regardless of distance.

```js
const timeTaken = new Date().getTime() - dragStartTime.current;
const velocity = Math.abs(swipeAmount) / timeTaken;

if (Math.abs(swipeAmount) >= SWIPE_THRESHOLD || velocity > 0.11) {
  dismiss();
}
```

- [ ] **Damping at boundaries**: When dragging past the natural boundary, apply increasing resistance. Things in real life do not suddenly stop — they slow down first. The more the user drags past the limit, the less the element moves.

- [ ] **Pointer capture**: Once dragging starts, set the element to capture all pointer events. This ensures dragging continues even if the pointer leaves the element bounds. Use `element.setPointerCapture()`.

- [ ] **Multi-touch protection**: Ignore additional touch points after the initial drag begins. Without this, switching fingers mid-drag causes the element to jump.

```js
function onPointerDown() {
  if (isDragging) return;
  isDragging = true;
  // Start drag...
}
```

- [ ] **Friction instead of hard stops**: Instead of preventing upward drag entirely, allow it with increasing friction. It feels more natural than hitting an invisible wall.

### Phase 4 — Review Component Code

Use the Review Format table for every component review:

| Before | After | Why |
| --- | --- | --- |
| `transition: all 300ms` | `transition: transform 200ms ease-out` | Specify exact properties; avoid `all` |
| `transform: scale(0)` | `transform: scale(0.95); opacity: 0` | Nothing in the real world appears from nothing |
| `ease-in` on dropdown | `ease-out` with custom curve | `ease-in` feels sluggish; `ease-out` gives instant feedback |
| No `:active` on button | `transform: scale(0.97)` on `:active` | Buttons must feel responsive |
| `transform-origin: center` on popover | `var(--radix-popover-content-transform-origin)` | Popovers scale from trigger, not center |

- [ ] Does the component have all four states? (rest, hover/focus, active/pressed, disabled)
- [ ] Does the exit animation feel faster than the enter animation?
- [ ] Are touch interactions tested on real devices?
- [ ] Does `prefers-reduced-motion` degrade gracefully?

---

## Glossary

| Term | Definition | Source |
|------|-----------|--------|
| `@starting-style` | CSS at-rule for animating element entry without JavaScript | Component |
| Momentum-based dismissal | Dismiss gesture triggered by velocity (quick flick) regardless of distance | Gesture |
| Pointer capture | API (`setPointerCapture`) that routes all pointer events to an element, even outside its bounds | Gesture |
| Damping | Increasing resistance at drag boundaries — things slow, they don't stop abruptly | Gesture |
| Origin-aware | `transform-origin` set to match where the trigger lives, not center | Component |
| Sonner Principles | Six guidelines for building loved components: DX, defaults, naming, edge cases, transitions, docs | Component |

---

## Chaining

**Upstream**:
- `ui/design-animations` — Animation decisions (easing, spring, clip-path) feed directly into component implementation
- `standards/polish-ui` — Performance rules and CSS transform mastery apply to every component

**Downstream**:
- `standards/apply-principles` — Simplicity and JIT engineering prevent over-engineering component APIs
- `standards/write-tests` — Components need interaction tests for gesture, animation, and state transitions

**Cross-cutting**: Component quality affects accessibility, internationalization, and design system consistency. Every component must be reviewed against the checklist in Phase 4 before shipping. Chain to `ui/design-animations` for any animation decision during component implementation.
