# Spec: Spring-Physics Expansion Drawer for Network Widget

This specification outlines the architecture, layout changes, and Anime.js transition parameters to refactor the Network Widget into a premium spring-physics powered expansion drawer, replacing the legacy CSS hover transition.

---

## 🎯 Central Philosophy & Design Goal

The Glass-Pack Status Bar thrives on physically realistic, fluid micro-interactions. The Network Widget current CSS transition (`transition: max-width 0.85s`) is functional but feels linear and non-interactive. 

By porting this expansion behavior to **Anime.js**, we can leverage our global mass-stiffness-damping spring parameters to slide and bounce the network drawer open on hover, aligning it with the beautiful macOS-drawer physics used in the Battery Widget.

```
[Hover In] (onMouseEnter)
  .network (maxWidth: 145px -> 400px)   ====> springExpand physics (spring(1, 80, 12, 0))
  .network-traffic (opacity & slide)    ====> easeOutElastic slide-in stagger bounce

[Hover Out] (onMouseLeave)
  .network (maxWidth: 400px -> 145px)   ====> liquid deceleration curve (cubicBezier(0.16, 1, 0.3, 1))
  .network-traffic (opacity & collapse) ====> Snappy linear ease-out fade out
```

---

## 🏗️ Technical Architecture & Proposed Changes

### 1. Style Isolation & CSS Cleanup
To prevent browser layout thrashing and animation stutter, we must strip all active transition behaviors for `max-width`, `margin-left`, `opacity`, and `transform` from the CSS. Anime.js will be given exclusive operational control.

- **Target File:** [test-widget/styles.css](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/styles.css)
- **Modifications:**
  - Remove CSS transition rules under the `.network` class that manage `max-width` and `transition`.
  - Remove legacy static expansion rules `.network.expanded { max-width: 400px; }`.
  - Remove all CSS transition transitions under `.network-traffic` and `.network-traffic.visible`.

### 2. Component Refactoring
We will bind container refs directly to the `.network` container and `.network-traffic` speed indicators, and orchestrate their timelines inside a React `useEffect` hooked to the `isHovered` state.

- **Target File:** [test-widget/index.html](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/index.html)
- **Anime.js Transitions Configuration:**
  - **Expand Event:**
    - Container Target: `containerRef` (`max-width: 400px`, duration: `850ms`, easing: `'spring(1, 80, 12, 0)'`).
    - Speed Text Target: `trafficRef` (`opacity: [0, 0.8]`, `marginLeft: ['0px', '8px']`, `translateX: [12, 0]`, duration: `600ms`, easing: `'easeOutElastic(1, 0.8)'`, delay: `80ms`).
  - **Collapse Event:**
    - Container Target: `containerRef` (`max-width: 145px`, duration: `400ms`, easing: `'cubicBezier(0.16, 1, 0.3, 1)'`).
    - Speed Text Target: `trafficRef` (`opacity: 0`, `marginLeft: '0px'`, `translateX: 0`, duration: `200ms`, easing: `'easeOutQuad'`).

---

## ♿ Accessibility & Performance Safeguards

1. **Reduced Motion Queries:**
   - The component will actively query `window.matchMedia('(prefers-reduced-motion: reduce)').matches`.
   - If active, the drawer will skip all physics steps and apply instant `anime.set()` positioning values to avoid layout disorientation.
2. **Animation Overlap Prevention:**
   - Any new hover enter/leave event will immediately trigger `anime.remove(containerRef.current)` and `anime.remove(trafficRef.current)` to purge existing animation threads and prevent thread stacking.
