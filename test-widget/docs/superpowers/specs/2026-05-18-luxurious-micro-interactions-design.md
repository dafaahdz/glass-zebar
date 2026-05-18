# Spec: Glass-Pack Luxurious Micro-Interactions Suite

This specification outlines the technical design, layout refactorings, and spring-physics parameters to implement a suite of premium visual micro-interactions across the Glass-Pack Status Bar.

---

## 🎯 Master Philosophy & Architecture

The ultimate objective of the Glass-Pack Status Bar is to look completely organic, responsive, and tactually alive. By combining cohesive **Anime.js** spring timelines with React hooks and isolated style variables, we can implement 5 high-end interactions:

1.  **Opsi B (Ethereal Island Halo):** Soft ambient background halos behind islands that expand and breathe on mouse hovers.
2.  **Opsi D (Elastic Jelly Workspace):** Jelly capsule indicators that stretch and squish dynamically along the motion axis when navigating workspaces.
3.  **Opsi G (Kinetic Gear-Shift Clock Ticker):** A watchmaker mechanical-style flip clock digit rotation with spring pops on time changes.
4.  **Opsi M (Precipitation Weather Card):** High-performance rain, snow, or sun rays floating on an offscreen canvas behind the frosted glass of the Weather widget.
5.  **Opsi N (Concentric Click Ripples):** Absolute dynamic SVG concentric ripple waves expanding outward from the exact cursor coordinate on every pill click.

---

## 🏗️ Detailed Subsystem Design

### 1. Ethereal Island Halo Glow (Opsi B)
- **Target File:** [test-widget/styles.css](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/styles.css)
- **CSS Layout:** Add a `::before` pseudo-element to `.island-left`, `.island-center`, and `.island-right` positioned absolutely behind the islands.
  ```css
  .island-left::before, .island-center::before, .island-right::before {
    content: '';
    position: absolute;
    inset: -10px;
    background: radial-gradient(circle, rgba(255, 255, 255, 0.08) 0%, transparent 70%);
    opacity: 0;
    filter: blur(24px);
    z-index: -1;
    pointer-events: none;
    will-change: opacity, transform;
  }
  ```
- **React Interaction:** On hover (`onMouseEnter`/`onMouseLeave` in React), trigger Anime.js to scale the pseudo-element `scale: [0.95, 1.1]` and animate `opacity: [0, 0.8]` with a gentle `spring(1, 100, 15, 0)`.

### 2. Elastic Jelly Workspace Indicator (Opsi D)
- **Target File:** [test-widget/index.html](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/index.html)
- **React Interaction:** Modify `WorkspaceWidget` sliding indicator logic:
  - Track current and target coordinates to establish direction and travel speed.
  - Apply `transform: scaleX(1.35) scaleY(0.85)` during the translation using Anime.js spring timelines.
  - On arrival, run an elastic snap-back `anime({ scaleX: 1, scaleY: 1, easing: 'spring(1, 80, 12, 0)' })` to trigger the satisfying "jelly wobble" landing bounce.

### 3. Kinetic Gear-Shift Clock Ticker (Opsi G)
- **Target File:** [test-widget/index.html](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/index.html)
- **HTML Refactor:** Split Clock time digits into individual wrappers:
  ```html
  <span class="ticker-digit-wrapper">
    <span class="ticker-digit-current">0</span>
    <span class="ticker-digit-next">1</span>
  </span>
  ```
- **React Animation:** Whenever minutes or hours change, animate the wrapper's `translateY` from `0%` to `-100%` to slide the old digit up and out, then instantly swap and reset with mechanical spring easing `'spring(1, 120, 15, 0)'` (snappy flip).

### 4. Frosted Weather Precipitation Canvas (Opsi M)
- **Target File:** [test-widget/index.html](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/index.html)
- **HTML Refactor:** Append a lightweight `<canvas>` absolutely positioned behind the weather status pill wrapper:
  ```html
  <div class="weather status-pill">
    <canvas class="weather-canvas" style="position: absolute; inset: 0; pointer-events: none; z-index: 0;"></canvas>
    <!-- Weather Icons and Text -->
  </div>
  ```
- **Particle Loop:** On hover, hook a high-performance requestAnimationFrame loop to render mini floating precipitation lines (rain / snow / sunshine rays) fading dynamically on leave.

### 5. Concentric Click Ripples (Opsi N)
- **Target File:** [test-widget/index.html](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/index.html)
- **Global Click Helper:** Bind a touch/click coordinator to all `StatusPill` components:
  - Calculate `x = clientX - boundingClientRect.left` and `y = clientY - boundingClientRect.top`.
  - Create a temporary `.click-ripple` element absolutely positioned at `(x, y)`.
  - Trigger Anime.js: `scale: [0, 2.5]`, `opacity: [0.6, 0]`, duration: `450ms`, easing: `'easeOutQuad'`, then clean up/remove the element from the DOM.

---

## ♿ Accessibility & Performance Safeguards

1.  **Strict prefers-reduced-motion Matching:**
    - Any active system-level reduced motion preference will skip all sliding, stretching, flipping, and particle cycles, applying static coordinates instantly using `anime.set()`.
2.  **Thread Cleanups:**
    - All Anime.js instances will register in `AnimationManager` map and clean up on React component unmount to prevent leaks in long-running persistent windows.
