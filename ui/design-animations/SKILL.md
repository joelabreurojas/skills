---
name: design-animations
description: "Animation decision framework, spring physics, clip-path techniques, stagger effects, and asymmetric timing for purposeful UI animation. Use when designing or reviewing animations, choosing easings, setting durations, or planning motion for a component."
license: MIT
trigger: "/design-animations, animation, motion design, UI animation, spring, easing, clip-path, stagger, transition timing, animate"
metadata:
  author: Emil Kowalski
  version: "1.0.0"
  derived_from: "emilkowalski/skill — emil-design-eng"
  porter_note: "Companion to ui/build-components (component implementation) and standards/polish-ui (performance, CSS transforms, debugging, accessibility). The three skills together fully cover Emil Kowalski's design engineering philosophy."
---

# Design Animations

## Philosophy

### Every animation needs a reason

Animation is not decoration. It is communication. A button scales on press to confirm the interface heard the user. A toast enters from the right to establish spatial consistency. A drawer drags with momentum to feel physically grounded.

If you cannot answer "why does this animate?" with a user-facing purpose, remove the animation. The best interface often has zero animation when the user is being productive.

### Speed is a feature

A 180ms dropdown feels more responsive than a 400ms one — not because it finishes faster, but because `ease-out` shows movement immediately. The perception of speed matters as much as actual speed. Users judge your app's performance by how quickly it *feels* to respond, not by how fast the network is.

A fast-spinning spinner makes loading feel faster even when load time is identical. The motion itself communicates tempo.

### Easing is personality

Built-in CSS easings are too weak. Custom `cubic-bezier` curves create the punch that makes animations feel intentional. The easing defines the emotional tone of the interface — crisp and responsive for tools, smooth and elegant for content, bouncy and playful for celebrations.

Match the motion to the mood. A professional dashboard should be fast and crisp. A creative tool can have playful bounce. A banking app should be nearly invisible.

---

## Anti-Patterns

### 1. Animating Keyboard-Initiated Actions

Command palettes, keyboard shortcuts, and hotkey-triggered interfaces must never animate. Users trigger these hundreds of times per day — animation makes them feel slow and disconnected from the keystroke.

**Fix**: Zero animation on keyboard-initiated actions. Raycast has no open/close animation. That is the optimal experience.

### 2. Using `ease-in` for UI Elements

`ease-in` starts slow, delaying the initial movement — the exact moment the user watches most closely. A dropdown with `ease-in` at 300ms *feels* slower than `ease-out` at the same 300ms.

**Fix**: Use `ease-out` for entering elements, `ease-in-out` for on-screen movement, `linear` for constant motion. Never `ease-in` for UI.

### 3. Same Enter and Exit Timing

If entering and exiting take the same duration, the interface feels mechanical. The user is deciding on entry (slow, deliberate) and the system should respond on exit (fast, snappy).

**Fix**: Make exit faster than enter. Enter at 200-300ms with `ease-out`. Exit at 100-150ms. Release is always faster than press.

### 4. All Elements Appearing at Once

When multiple items enter simultaneously, the visual flood overwhelms the user. Everything fighting for attention means nothing gets it.

**Fix**: Stagger with 30-80ms delays between items. The cascade guides the eye naturally, giving each element its moment.

---

## Workflow

### Phase 1 — Apply the Animation Decision Framework

Before writing any animation code, answer these four questions in order:

- [ ] **Should it animate?** How often will users see this?
  - 100+ times/day: No animation. Ever.
  - Tens of times/day: Remove or drastically reduce.
  - Occasional (modals, toasts): Standard animation.
  - Rare/first-time: Can add delight.
- [ ] **What is the purpose?** Answer "why does this animate?"
  - Valid: spatial consistency, state indication, explanation, feedback, preventing jarring changes.
  - Invalid: "looks cool" for frequently-seen elements.
- [ ] **What easing should it use?** Follow the easing decision tree:
  - Entering/exiting → `ease-out` (starts fast, responsive)
  - Moving/morphing on screen → `ease-in-out` (natural accel/decel)
  - Hover/color change → `ease`
  - Constant motion (marquee, progress) → `linear`
- [ ] **How fast should it be?** Match duration to element:
  - Button press: 100-160ms
  - Tooltips, small popovers: 125-200ms
  - Dropdowns, selects: 150-250ms
  - Modals, drawers: 200-500ms
  - Marketing/explanatory: Can be longer
  - **Rule**: UI animations under 300ms.

```css
/* Custom easing curves — built-in CSS easings are too weak */
--ease-out: cubic-bezier(0.23, 1, 0.32, 1);
--ease-in-out: cubic-bezier(0.77, 0, 0.175, 1);
--ease-drawer: cubic-bezier(0.32, 0.72, 0, 1);
```

### Phase 2 — Choose Spring or Duration-Based

Springs feel more natural than duration-based animations because they simulate real physics. They have no fixed duration — they settle based on physical parameters. CSS transitions and keyframes have fixed durations.

Use springs for:
- [ ] Drag interactions with momentum (dismiss gestures, drawer pull)
- [ ] Elements that should feel "alive" (mouse tracking, decorative)
- [ ] Gestures that can be interrupted mid-animation
- [ ] Decorative interactions tied to cursor position

Use CSS transitions/keyframes for:
- [ ] Determined enter/exit states (tooltips, modals, dropdowns)
- [ ] Performance-critical paths (CSS runs off main thread)
- [ ] Any animation that does not need interruptibility

**Spring configuration**:

```js
// Apple-style (recommended — easier to reason about)
{ type: "spring", duration: 0.5, bounce: 0.2 }

// Traditional physics (more control)
{ type: "spring", mass: 1, stiffness: 100, damping: 10 }
```

- [ ] Keep bounce subtle (0.1-0.3) when used
- [ ] Avoid bounce in most UI contexts — use for drag-to-dismiss and playful interactions only
- [ ] Springs maintain velocity when interrupted — ideal for gestures users might cancel mid-motion

### Phase 3 — Apply clip-path Animation Techniques

`clip-path` is one of the most powerful animation tools in CSS. Use it for reveal effects, tab transitions, and overlay interactions.

- [ ] **Inset reveals**: `clip-path: inset(top right bottom left)` defines a rectangular clipping region. Each value "eats" into the element from that side.

```css
.hidden { clip-path: inset(0 100% 0 0); }     /* Hidden from right */
.visible { clip-path: inset(0 0 0 0); }        /* Fully visible */
```

- [ ] **Tabs with perfect color transitions**: Duplicate the tab list, style the copy as "active," and clip the copy so only the active tab is visible. Animate the clip on tab change. This creates seamless color transitions that individual timing can never achieve.

- [ ] **Hold-to-delete overlay**: Use `clip-path: inset(0 100% 0 0)` on a colored overlay. On `:active`, transition to `inset(0 0 0 0)` over 2s linear. On release, snap back with 200ms `ease-out`. Add `scale(0.97)` on the button for press feedback.

```css
.overlay {
  clip-path: inset(0 100% 0 0);
  transition: clip-path 200ms ease-out;
}
.button:active .overlay {
  clip-path: inset(0 0 0 0);
  transition: clip-path 2s linear;
}
```

- [ ] **Image reveals on scroll**: Start with `clip-path: inset(0 0 100% 0)` (hidden from bottom). Animate to `inset(0 0 0 0)` when the element enters the viewport.

- [ ] **Comparison sliders**: Overlay two images. Clip the top one with `clip-path: inset(0 50% 0 0)`. Adjust the right inset based on drag position. No extra DOM, fully hardware-accelerated.

### Phase 4 — Add Stagger Animations

When multiple elements enter together, stagger their appearance with small delays. This creates a cascading effect that feels more natural than everything appearing at once.

- [ ] Keep stagger delays short: **30-80ms** between items
- [ ] Long delays make the interface feel slow — never block interaction while stagger animations play
- [ ] Stagger is decorative — it should not delay functionality

```css
.item {
  opacity: 0;
  transform: translateY(8px);
  animation: fadeIn 300ms ease-out forwards;
}

.item:nth-child(1) { animation-delay: 0ms; }
.item:nth-child(2) { animation-delay: 50ms; }
.item:nth-child(3) { animation-delay: 100ms; }
.item:nth-child(4) { animation-delay: 150ms; }

@keyframes fadeIn {
  to { opacity: 1; transform: translateY(0); }
}
```

### Phase 5 — Fine-Tune Timing and Cohesion

- [ ] **Opacity + height combination**: When items enter and exit a list, the opacity change must work well with the height animation. There is no formula — adjust until it feels right. Test at different speeds.

- [ ] **Asymmetric enter/exit timing**: Pressing should be slow when it needs to be deliberate, but release should always be fast.

| Action | Press (Decide) | Release (Respond) |
|--------|---------------|-------------------|
| Hold-to-delete | 2s `linear` | 200ms `ease-out` |
| Button click | 160ms `ease-out` | N/A |
| Modal | 300ms `ease-out` | 150ms `ease-out` |

- [ ] **Cohesion check**: Does the easing and duration match the component's personality? Sonner uses slightly slower `ease` for elegance. A dashboard should be faster and crisper. Match motion to mood.

- [ ] **Review your work the next day**: Play animations in slow motion or frame by frame to spot timing issues invisible at full speed.

---

## Glossary

| Term | Definition | Source |
|------|-----------|--------|
| `ease-out` | Easing that starts fast and decelerates — for entering elements, feels responsive | Animation |
| `ease-in-out` | Easing that accelerates then decelerates — for on-screen movement | Animation |
| Spring | Physics-based animation with no fixed duration; settles based on stiffness, damping, mass | Animation |
| `clip-path: inset()` | CSS clipping that reveals/hides by eating into element edges — fully hardware-accelerated | CSS |
| Stagger | Cascading animation with small delays (30-80ms) between items entering together | Animation |
| Interruptibility | Ability to retarget an animation mid-flight; springs and CSS transitions are interruptible, keyframes are not | Animation |

---

## Chaining

**Upstream**:
- `standards/polish-ui` — Performance rules, CSS transform mastery, and accessibility checks ground every animation decision

**Downstream**:
- `ui/build-components` — Apply animation decisions to component implementation (buttons, popovers, toasts, drawers)
- `standards/apply-principles` — Simplicity and JIT engineering principles prevent over-animation

**Cross-cutting**: Animation choices affect accessibility (`prefers-reduced-motion`), performance (GPU compositing), and review criteria. Always check the Review Checklist in `standards/polish-ui` before finalizing animation code.
