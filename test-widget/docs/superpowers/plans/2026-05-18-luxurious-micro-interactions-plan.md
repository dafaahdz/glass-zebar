# Glass-Pack Luxurious Micro-Interactions Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Implement 5 ultra-premium micro-interactions (Islands Halo, Jelly Workspace, Clock Gear-Ticker, Weather Particle Canvas, Concentric Click Ripples) using Anime.js and optimized CSS/HTML to elevate the visual aesthetic to a luxurious standard.

**Architecture:** Utilize hardware-accelerated CSS pseudo-classes, dynamic Canvas requestAnimationFrame timers, React DOM refs, and coordinated Anime.js physics.

**Tech Stack:** React 18, HTML5 Canvas, Anime.js, CSS, HTML

---

### Task 1: Ethereal Island Halo Glow (Opsi B)

**Files:**
- [test-widget/styles.css](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/styles.css)

- [ ] **Step 1: Append pseudo-elements and transitions for all islands**

Add custom `::before` glowing layers to floating islands with dynamic scaling, blur, and opacity.

```css
.island-left,
.island-center,
.island-right,
.island-arch,
.island-album {
  position: relative;
}

.island-left::before,
.island-center::before,
.island-right::before,
.island-arch::before,
.island-album::before {
  content: '';
  position: absolute;
  inset: -12px;
  background: radial-gradient(circle, var(--island-glow-color, rgba(255, 255, 255, 0.08)) 0%, transparent 70%);
  opacity: 0;
  filter: blur(20px);
  z-index: -1;
  pointer-events: none;
  border-radius: inherit;
  transition: opacity 0.6s cubic-bezier(0.16, 1, 0.3, 1), transform 0.6s cubic-bezier(0.16, 1, 0.3, 1);
  transform: scale(0.92);
}

.island-left:hover::before,
.island-center:hover::before,
.island-right:hover::before,
.island-arch:hover::before,
.island-album:hover::before {
  opacity: 0.85;
  transform: scale(1.06);
}
```

- [ ] **Step 2: Assign custom ambient theme colors to islands**

In [test-widget/index.html](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/index.html), add local ambient theme variables (e.g. `--island-glow-color: var(--kawaii-pink)`) on islands in JSX return to give them rich, glowing visual personalities.

---

### Task 2: Elastic Jelly Workspace Indicators (Opsi D)

**Files:**
- [test-widget/index.html](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/index.html)

- [ ] **Step 1: Calculate direction and apply advanced spring squish/stretch**

Update the `useEffect` inside `WorkspaceWidget` to calculate the travel direction and apply advanced non-linear scaling `scaleX` and `scaleY` with matching skew shifts.

Replace the current Anime.js timeline with:
```javascript
            const prevLeft = parseFloat(indicatorRef.current.style.left) || 0
            const direction = leftOffset > prevLeft ? 1 : -1

            anime.remove(indicatorRef.current)
            anime({
              targets: indicatorRef.current,
              left: `${leftOffset}px`,
              width: `${width}px`,
              scaleX: [1, 1.45, 0.9, 1],
              scaleY: [1, 0.78, 1.1, 1],
              skewX: [0, direction * 12, 0],
              duration: 850,
              easing: 'spring(1, 80, 12, 0)',
            })
```

---

### Task 3: Kinetic Gear-Shift Clock Ticker (Opsi G)

**Files:**
- [test-widget/index.html](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/index.html)
- [test-widget/styles.css](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/styles.css)

- [ ] **Step 1: Create `TickerDigit` sub-component**

Add a fully memoized `TickerDigit` sub-component in `index.html` to slide digits vertically upon time updates.

- [ ] **Step 2: Update `ClockWidget` to render split digits**

Refactor `ClockWidget` to split `date.formatted` and feed characters into `TickerDigit`.

---

### Task 4: Frosted Weather Precipitation Canvas (Opsi M)

**Files:**
- [test-widget/index.html](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/index.html)
- [test-widget/styles.css](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/styles.css)

- [ ] **Step 1: Add Canvas and particle drawer loop to WeatherWidget**

Inject a background `<canvas>` inside `WeatherWidget` and run a highly optimized canvas particle loop on hover to draw falling rain/snow/sunny beams with linear fade-out cleanups.

---

### Task 5: Concentric Click Ripples (Opsi N)

**Files:**
- [test-widget/index.html](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/index.html)

- [ ] **Step 1: Upgrade `window.triggerRipple` to render concentric double rings**

Refactor `window.triggerRipple` to create dual-concentric expanding rings with a small stagger delay, creating a beautiful water ripple sensation.

---

### Task 6: Verification & Git Commit

- [ ] **Step 1: Verify code and commit the changes**

Stash changes and commit with:
```bash
git add test-widget/styles.css test-widget/index.html
git commit -m "feat: deploy luxury micro-interactions suite (B, D, G, M, N)"
```
