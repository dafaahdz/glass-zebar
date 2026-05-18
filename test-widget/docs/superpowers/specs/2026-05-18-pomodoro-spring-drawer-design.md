# Design Specification: Pomodoro Snappy Elastic Spring-Flex Expansion

This document outlines the design and architectural implementation of **Opsi A: Snappy Elastic Spring-Flex Expansion** for the Pomodoro settings drawer inside Zebar.

---

## 🎯 1. Core Problem & Objective
Right-clicking the Pomodoro widget to toggle settings currently displays a jarring visual transition due to:
1. Competing CSS transitions (`margin-left` 0.85s ease) colliding with Anime.js `maxWidth`/`scale` animations.
2. A visual layout freeze (dead-zone) caused by an over-shot maximum animated width (`maxWidth: 480` vs. actual form width of `~310px`).

**Objective:**
Re-engineer the toggle animation to behave as a single, fluid, hardware-accelerated horizontal spring-drawer that slides out and retracts with premium, tactile macOS-style spring physics.

---

## 🎨 2. Visual Interaction & Physics Blueprint

### Keyframe & Spring Parameters:
- **Easing:** `spring(1, 95, 15, 0)` (highly stable, snappy elastomeric spring)
- **Target Form Max-Width:** `310px` (ideal scale containing all form inputs snugly)
- **Scale Bounds:** `[0.96, 1]` on open, `[1, 0.96]` on close for dynamic depth.

### CSS Adjustments:
- Remove the competing inline transition `transition: margin-left 0.85s` from `.inline-pomodoro-settings` in `styles.css`.
- Ensure flex items align cleanly during container resize.

---

## 🧩 3. Proposed Component Changes

### [styles.css](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/styles.css)
- Modify `.inline-pomodoro-settings` to remove competitive `transition` property.
- Adjust margin rules to render without jitter.

### [index.html](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/index.html)
- Update `useEffect` inside `PomodoroWidget` tracking `isDialogOpen`.
- Bind `maxWidth`, `opacity`, `scale` to snappy spring equations.
