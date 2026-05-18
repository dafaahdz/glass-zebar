# Spec: Agent Guidelines & Safeguards for Glass-Pack

This specification defines the strict guidelines, buildless architecture constraints, premium glassmorphism aesthetic guidelines, Webview2 performance optimizations, and step-by-step checklists to be codified in `agents.md`. This file will serve as a master reference to ensure all future AI agents maintain codebase integrity, prevent memory leaks, and preserve high-end visuals.

---

## 🎯 Goal Description
The purpose of `agents.md` is to establish clear rules of engagement and modular engineering patterns for AI agents developing the Glass-Pack status bar. This protects the codebase from architectural drift (e.g. introducing external bundlers or TypeScript), ensures smooth animations (spring physics, reduced-motion controls), prevents memory leaks in MS Webview2 (lifecycle management via `AnimationManager`), and streamlines new widget development.

---

## Proposed Changes

### [Component Name: Root Documentation]

#### [NEW] [agents.md](file:///c:/Users/aipus/.glzr/zebar/glass-pack/agents.md)
Create a markdown ruleset file directly in the project root outlining all constraints, design patterns, and checklists.

---

## Technical Details & Policies to Codify

### 1. Pure Buildless Architecture Rules
- **No Bundlers/TS:** Strict prohibition of Node bundlers (Vite, Webpack, etc.) or TypeScript. Runtimes must remain purely buildless, compiled client-side using `@babel/standalone`.
- **ESM Imports:** All React, ReactDOM, Anime.js, and Zebar dependency imports must be loaded directly as ES Modules via `esm.sh` CDN.
- **Single-File Component Script:** Keep React widget markup and main loop logic encapsulated in the single `test-widget/index.html` file within a script block (`type="text/babel"` and `data-type="module"`).

### 2. Performance & Webview2 Memory Leak Policies
- **Animation Tracking:** Every Anime.js instance must register with `AnimationManager.register(id, instance)` and run `cleanup` upon React unmounts to prevent memory footprint inflation in background Webview2 processes.
- **Throttling & Batching:** System state updates must be batched using `requestAnimationFrame` within the custom `useSystemData()` hook.
- **Debounced Updates:** Heavy system data provider reads or network bandwidth polling must be debounced to at least 500ms intervals.

### 3. Glassmorphic Visuals & Easing Presets
- **Glassmorphism Spec:** Frosted transparent backgrounds, light refractive top border highlights, and dark bottom shadow effects.
- **Spring Physics:** Use physics-based mass-stiffness-damping curves (`EASINGS.springMicro`, `EASINGS.springExpand`, `EASINGS.elasticPop`) to create organic micro-animations.
- **Reduced Motion:** If `prefers-reduced-motion: reduce` is active, immediately bypass animations and set absolute element positions.

### 4. Asynchronous Persistent Storage & Caching
- **Async IndexedDB:** All persistent configuration settings must use the non-blocking `IndexedDBStore` class (`dbStore`) rather than synchronous `localStorage`.
- **Computation Caching:** Math transforms must be cached using `useMemo` to keep CPU layout thrashing close to zero.

### 5. Implementation Checklist
- Code a rigid step-by-step checklist to guide agents through updating `zpack.json`, adding CSS, importing icons, writing React widget logic, registering animations, and testing.

---

## Verification Plan

### Automated/Manual Verification
1. Verify `agents.md` is present in the workspace root.
2. Confirm the content exactly matches the approved design parameters (without the Catppuccin color palette section).
3. Validate that standard markdown link formatting is used correctly.
