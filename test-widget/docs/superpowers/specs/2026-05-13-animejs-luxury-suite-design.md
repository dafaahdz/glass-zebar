# Spec: Anime.js Luxury Animation Suite for Glass-Pack Status Bar

This specification outlines the architecture, easing equations, and phased refactoring plans to transform the Glass-Pack Status Bar into a high-performance, unified, hardware-accelerated animation suite powered by **Anime.js**.

---

## 🎯 Central Philosophy & Architecture

Instead of disconnected, browser-timed CSS animations and raw event-loop triggers, this suite introduces a central custom hook (`useAnime`) and lifecycle manager (`AnimationManager`) to enforce clean mounting, pause/resume persistence, reduced-motion accessibility standards, and zero layout-thrashing.

```
+------------------------------------------------------------+
|                       Status Bar DOM                       |
+------------------------------------------------------------+
                              |
                     [ elementRef Ref ]
                              |
                              v
                   +---------------------+
                   |    useAnime Hook    | <--- EASINGS Presets
                   +---------------------+
                    /                   \
                   /                     \
                  v                       v
      [ AnimationManager ]         [ controlRef Controller ]
   (Leak Prevention & Cleanup)     (play / pause / seek / restart)
```

### 1. Unified Easing Dictionary (`EASINGS`)

Centralizes physics-based Mass-Stiffness-Damping curves to deliver natural organic motion.

- **`springMicro`** (`spring(1, 120, 15, 0)`): Snappy bounce for button click pops and active states.
- **`springExpand`** (`spring(1, 80, 12, 0)`): Slow macOS-style organic drag for collapsible drawers.
- **`liquid`** (`cubicBezier(0.16, 1, 0.3, 1)`): Smooth decelerating fluid movement.
- **`elasticPop`** (`easeOutElastic(1, .8)`): Satisfying haptic feedback on clicks.

---

## 🏗️ Phase 0 & 1: Core Infrastructure Setup

### 1. Global Registry (`AnimationManager`)

Tracks and terminates animation threads upon component unmounts to guarantee zero memory leaks in Webview2.

### 2. Controllable Hook (`useAnime`)

Returns `elementRef` and an imperative `controlRef` containing:

- `play()` / `pause()`: Immediate playback control.
- `seek(progress)`: Jumps precisely to specific frames (normalized `0.0` - `1.0`).
- `restart(newerConfig)`: Overrides and rebuilds configurations on the fly.

---

## 🎵 Phase 2: High-Performance Media Suite

### 1. Vinyl Album Spin (`MediaAlbumWidget`)

- Continuous rotation (`rotate: '360deg'`, `loop: true`) controlled via `isPlaying`.
- True pause/resume angles; seeks directly on manual scrub events when paused:
  $$\text{angle} = \left( \frac{\text{position mod } 10000}{10000} \right) \times 360^\circ$$

### 2. Smooth Marquee (`MediaWidget`)

- Measures text boundaries dynamically in a separate `useEffect`.
- Calculates scroll duration on constant px/ms ratios to eliminate extreme speed variations:
  $$\text{duration} = \frac{\text{scrollWidth} + \text{gap}}{\text{speed}}$$
- Fades and stops on hover/settings drawer expansion without DOM reflows.

---

## ⚡ Phase 3: Physics & Spring Micro-Interactions

### 1. Organic Fluid Wave (`BatteryWidget`)

- Morphs vector SVG path wavefront contours continuously to simulate moving liquid inside the battery icon.
- Translates height on the Y-axis using high-performance CSS transitions.

### 2. Haptic-Like Click Feedback (`StatusPill`)

- Triggers snappy scale bounces (`scale: [1, 1.06, 1]`) on widget clicks.
- Includes strict checks to immediately skip animations if `prefers-reduced-motion` is active.

---

## 🎨 Phase 4: State-Driven Mapping, Diagnostics & Easter Eggs

### 1. State-Driven Breathing Pulse (`PomodoroWidget`)

- Ties active timer heartbeats to finite Pomodoro FSM modes, driving color and subtle looping pulses.

### 2. Outward Stagger Ripple (`WorkspaceWidget`)

- Propagates scale ripples starting directly from the newly active workspace index outward.

### 3. Diagnostics & Cheat Codes

- **`DebugOverlay`** (`Ctrl + Shift + D`): Reports true active Anime.js instances and frame rates.
- **Konami Code** (`↑↑↓↓←→←→BA`): Triggers a spectacular stagger rotation with full accessibility filters.
