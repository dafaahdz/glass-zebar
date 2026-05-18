# Developer & Agent Guidelines (agents.md)

This document establishes strict rules, architecture constraints, performance policies, design systems, and checklists that **every AI agent or developer must follow** when maintaining or expanding this codebase.

---

## 🎯 1. Core Philosophy & Buildless Architecture
To preserve simplicity, ease of customization, and lightweight footprints, follow these strict development rules:

1. **No External Bundlers or TypeScript:**
   - Strict ban on adding bundlers (Vite, Webpack, Rollup, Turbo, esbuild) or compiling via TypeScript.
   - The runtime must remain purely buildless, compiled client-side by the browser using the preloaded `@babel/standalone` compiler.
2. **ES Module Imports via esm.sh:**
   - All external dependency libraries (React 18, canvas-confetti, Anime.js, Zebar API, etc.) must be imported as native ES Modules using `esm.sh` CDN URLs:
     - React: `import React, { useState, useEffect, useRef, useMemo } from 'https://esm.sh/react@18?dev'`
     - Anime.js: `import anime from 'https://esm.sh/animejs@3.2.1'`
     - Zebar API: `import * as zebar from 'https://esm.sh/zebar@3.0'`
3. **Single-File React Component Script:**
   - Keep all React widget definitions, helper stores, custom hooks, and layout state encapsulated inside `test-widget/index.html` within the `<script type="text/babel" data-type="module">` tag.
4. **Nerd Font v3 Icons:**
   - Standardize all widget icons using classes from **Nerd Font v3** (e.g. `nf-md-wifi`, `nf-fae-chip`). Do not introduce other icon packages or images unless explicitly requested.

---

## 🧬 2. Webview2 Performance & Leak Prevention
Since Zebar widgets operate as persistent desktop windows powered by Microsoft Webview2/Chromium, memory and CPU optimization are absolute priorities.

1. **Mandatory Animation Lifecycle Management (Anime.js):**
   - **Never** spin up raw or untracked CSS/JS event loops.
   - All Anime.js instances must be registered with `AnimationManager` to ensure they are cleaned up and killed upon unmount.
   - Prefer using the custom `useAnime(config, deps, id)` hook which encapsulates these cleanup policies dynamically.
2. **Batching State Updates via requestAnimationFrame:**
   - To avoid layout thrashing and high frame-time spikes, use `useSystemData()` to listen to and batch the Zebar providers data stream inside `requestAnimationFrame`.
3. **Debouncing High-Frequency Streams:**
   - Throttling is required for heavy computations. Network traffic polling, CPU usage queries, or disk operations must be debounced to at least 500ms intervals.

---

## 🎨 3. Design System & Premium Aesthetics
Keep the status bar premium, frosted, and visually alive using these design standards:

1. **Frosted Ethereal Glassmorphism:**
   - Apply frosted translucent glass variables, refractive top edge highlights, and dark bottom shadow offsets:
     ```css
     background: rgba(255, 255, 255, 0.03);
     backdrop-filter: blur(16px);
     border: 1px solid rgba(255, 255, 255, 0.02);
     box-shadow: inset 0 1.5px 0 rgba(255, 255, 255, 0.16),
                 inset 0 -1px 0 rgba(0, 0, 0, 0.5);
     ```
2. **Spring Physics Animation dictionary (`EASINGS`):**
   - Leverage physical easing parameters instead of linear/standard cubic beziers:
     - `springMicro` (`spring(1, 120, 15, 0)`): Snappy bounce.
     - `springExpand` (`spring(1, 80, 12, 0)`): MacOS-style drawer expansion.
     - `elasticPop` (`easeOutElastic(1, .8)`): Satisfying pop for interactive clicks.
3. **Accessibility Guard (Reduced Motion):**
   - Check if `prefers-reduced-motion` is active. If active, skip all heavy Anime.js animations or clip-path cycles immediately and render static final positions directly.

---

## ⚡ 4. Asynchronous Persistent Storage & Math Optimizations
1. **Async Storage via IndexedDBStore:**
   - Do not use blocking, synchronous `localStorage` which freezes Webview2's single rendering thread. Always use `dbStore` (`IndexedDBStore`) for setting/getting persistent states.
2. **Efficient Math Operators & Caching:**
   - Cache math conversions using `useMemo`.
   - Use bitwise-aligned byte converters with fast binary log2 arithmetic:
     ```javascript
     const i = Math.floor(Math.log2(bytes) / 10);
     ```

---

## 📋 5. Rigid Widget Implementation Checklist
When adding or updating a widget, agents must complete each step in order:

* [ ] **Step 1: Manifest Registration**
  Register the widget name, anchor presets (`top_left`, `top_right`), inclusion paths (`test-widget/**`), dimensions, monitor options, and caching rules inside `zpack.json`.
* [ ] **Step 2: Component Implementation**
  Code the React widget inside `test-widget/index.html`. Connect it to the providers data stream using `useSystemData()`.
* [ ] **Step 3: Glassmorphism Styling**
  Add class styles to `test-widget/styles.css` using frosted glass standards and responsive pill layouts.
* [ ] **Step 4: Animation Lifecycle Cleanups**
  Use `useAnime` or track Anime.js instances in the global `AnimationManager` to guarantee zero memory leaks upon unmounting.
* [ ] **Step 5: Motion Accessibility Check**
  Verify the widget skips animations gracefully if the system's `prefers-reduced-motion: reduce` media query is active.
