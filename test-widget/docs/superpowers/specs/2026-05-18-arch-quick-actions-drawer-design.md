# Design Specification: Arch Quick-Actions Spring Drawer

This document establishes the architecture, layout system, styling properties, and dynamic spring-loaded animation routines to integrate a multi-functional quick-actions command center directly inside the expanding shell of the Arch Linux status bar capsule.

---

## 🎯 1. Core Problem & Objective
Rather than occupying unnecessary vertical or horizontal space with floating popup menus, this feature morphs the existing branding element (`island-arch` / Arch Linux capsule) into an expandable container. Right-clicking the capsule triggers a horizontal spring-loaded expansion to the right, revealing 5 minimalist quick-actions.

To make the drawer feel premium and consistent with the status pills in the right island, **each button behaves as a miniature liquid glass capsule (status-pill)**. Normally, each button shows only its centered icon. When hovered, the button dynamically expands to the right to reveal its title text, **pushing neighboring buttons to the right with a smooth, physical kinetic sliding motion**.

---

## 🎨 2. Component Structure (HTML/JSX Refactor)

### Target File: [test-widget/index.html](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/index.html)

The existing `island-arch` element will be refactored to house both the branding icon and the nested action drawer. To achieve the horizontal text slide-in, the hover tooltips are refactored into inline label spans inside each button:

```html
<div
  className={`island-arch ${isTrayOpen ? 'expanded' : ''}`}
  onContextMenu={handleRightClick}
  onClick={handleLeftClick}
>
  <div className="arch-glass-capsule">
    <i className="nf nf-md-arch"></i>
  </div>

  <div className="arch-quick-actions">
    {/* 1. Last.fm Love Track Toggle */}
    <button className={`action-btn love-btn ${isLoved ? 'active' : ''}`} onClick={toggleLove}>
      <i className={`nf ${isLoved ? 'nf-md-heart' : 'nf-md-heart_outline'}`}></i>
      <span className="action-btn-text">Love Track</span>
    </button>

    {/* 2. Pomodoro FSM State Skipper */}
    <button className="action-btn skip-btn" onClick={skipPomodoro}>
      <i className="nf nf-md-skip_forward"></i>
      <span className="action-btn-text">Skip Pomodoro</span>
    </button>

    {/* 3. Force Weather Sync */}
    <button className="action-btn sync-btn" onClick={forceSyncWeather}>
      <i className="nf nf-md-autorenew"></i>
      <span className="action-btn-text">Sync Weather</span>
    </button>

    {/* 4. Frosted Glass Vignette Switcher */}
    <button className={`action-btn vignette-btn ${isVignetteActive ? 'active' : ''}`} onClick={toggleVignette}>
      <i className="nf nf-md-vignette"></i>
      <span className="action-btn-text">Toggle Vignette</span>
    </button>

    {/* 5. Palette Shifter */}
    <button className="action-btn palette-btn" onClick={cyclePalette}>
      <i className="nf nf-md-palette"></i>
      <span className="action-btn-text">Cycle Palette</span>
    </button>
  </div>
</div>
```

---

## 🎨 3. Styling & Micro-Interactions

### Target File: [test-widget/styles.css](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/styles.css)

Add clean glassmorphism rules for the expanding container and the quick action buttons:

```css
/* Island Arch Container - Flex row with hidden overflow */
.island-arch {
  display: inline-flex;
  flex-direction: row;
  align-items: center;
  justify-content: flex-start;
  background: #0a0a0c !important;
  border: 1px solid rgba(255, 255, 255, 0.05);
  box-shadow: 0 16px 32px -12px rgba(0, 0, 0, 0.8);
  border-radius: 9999px; /* Pill-shaped so it stretches beautifully */
  min-width: 32px;
  width: auto;
  max-width: 32px; /* Collapsed width matches 32px circle */
  height: 32px;
  padding: 4px;
  box-sizing: border-box;
  transition:
    border-color 0.5s ease,
    box-shadow 0.5s ease,
    transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  margin-right: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden; /* Contain ripples */
  will-change: max-width, padding;
}

/* Hover glow & scaling when collapsed */
.island-arch:not(.expanded):hover {
  border-color: rgba(23, 147, 209, 0.3);
  box-shadow: 0 0 15px rgba(23, 147, 209, 0.15);
  transform: translateY(-0.5px) scale(1.08);
}

/* Expanded state overrides */
.island-arch.expanded {
  cursor: default !important;
  max-width: 450px; /* Ample max-width to host dynamic button expansions */
  padding-right: 14px !important;
  border-color: rgba(23, 147, 209, 0.35) !important;
  box-shadow: 0 0 16px rgba(23, 147, 209, 0.2) !important;
}

.island-arch.expanded:hover {
  transform: none !important;
  background: #0a0a0c !important;
}

/* Inner Nested Circular Glass Capsule */
.arch-glass-capsule {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.03) !important;
  backdrop-filter: blur(16px);
  border: 1px solid rgba(255, 255, 255, 0.02) !important;
  /* Premium 3D Edge Highlights */
  box-shadow:
    inset 0 1.5px 0 rgba(255, 255, 255, 0.16),
    inset 0 -1px 0 rgba(0, 0, 0, 0.5);
  box-sizing: border-box;
  position: relative;
  overflow: hidden;
  transition: background 0.4s ease;
  flex-shrink: 0; /* Never shrink or flatten the logo capsule! */
}

.island-arch:not(.expanded):hover .arch-glass-capsule {
  background: rgba(255, 255, 255, 0.08) !important;
}

/* Centered Arch Icon styling */
.arch-glass-capsule i {
  font-size: 11px;
  color: #1793d1;
  text-shadow: 0 0 8px rgba(23, 147, 209, 0.4);
  transition: transform 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

/* 360-degree rotation transition on hover when collapsed */
.island-arch:not(.expanded):hover .arch-glass-capsule i {
  transform: rotate(360deg);
}

/* Quick Actions Inner Drawer Wrapper */
.arch-quick-actions {
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 8px; /* Snappy, tight gaps */
  max-width: 0;
  opacity: 0;
  margin-left: 0;
  overflow: hidden;
}

.island-arch.expanded .arch-quick-actions {
  max-width: 400px;
  opacity: 1;
}

/* ==========================================
   Liquid Glass Action Capsule Pill Styles
   ========================================== */
.arch-quick-actions .action-btn {
  display: inline-flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  background: rgba(255, 255, 255, 0.03) !important;
  backdrop-filter: blur(16px);
  border: 1px solid rgba(255, 255, 255, 0.02) !important;
  box-shadow:
    inset 0 1.5px 0 rgba(255, 255, 255, 0.16),
    inset 0 -1px 0 rgba(0, 0, 0, 0.5);
  border-radius: 9999px;
  padding: 0 !important;
  min-width: 24px;
  max-width: 24px; /* Circular by default */
  height: 24px !important;
  color: var(--ethereal-text-dim);
  cursor: pointer;
  outline: none;
  box-sizing: border-box;
  overflow: hidden;
  transition:
    max-width 0.45s cubic-bezier(0.25, 1, 0.4, 1),
    padding 0.45s cubic-bezier(0.25, 1, 0.4, 1),
    border-color 0.4s ease,
    box-shadow 0.4s ease,
    color 0.25s ease;
  flex-shrink: 0;
}

/* Icon centering adjustment */
.arch-quick-actions .action-btn i {
  font-size: 11px;
  transition: transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  line-height: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 12px;
  height: 12px;
  margin: 0 auto;
}

/* Text label styling - Collapsed by default */
.arch-quick-actions .action-btn .action-btn-text {
  font-family: var(--font-monospace);
  font-size: 8px;
  font-weight: 800;
  color: rgba(255, 255, 255, 0.9);
  opacity: 0;
  max-width: 0;
  transform: translateX(6px);
  transition:
    opacity 0.2s ease,
    max-width 0.25s ease,
    transform 0.2s ease,
    margin-left 0.25s ease;
  white-space: nowrap;
  overflow: hidden;
  pointer-events: none;
  margin-left: 0;
  flex-shrink: 0;
}

/* ==========================================
   Kinetic Hover Expansion (Flex Push)
   ========================================== */
.arch-quick-actions .action-btn:hover {
  max-width: 120px; /* Expand to pill shape */
  padding: 0 10px !important;
  color: var(--ethereal-text);
  justify-content: flex-start;
}

.arch-quick-actions .action-btn:hover i {
  transform: scale(1.1);
  margin: 0; /* Align left alongside text */
}

.arch-quick-actions .action-btn:hover .action-btn-text {
  opacity: 0.95;
  max-width: 80px;
  transform: translateX(0);
  margin-left: 6px;
  transition:
    opacity 0.35s cubic-bezier(0.25, 1, 0.4, 1) 0.08s,
    max-width 0.4s cubic-bezier(0.25, 1, 0.4, 1),
    transform 0.35s cubic-bezier(0.25, 1, 0.4, 1) 0.08s,
    margin-left 0.4s ease;
}

/* ==========================================
   Themed Kawaii Glow Profiles on Hover
   ========================================== */
/* 1. Love Track: Red-Pink Glow */
.arch-quick-actions .action-btn.love-btn:hover {
  border-color: rgba(243, 139, 168, 0.35) !important;
  box-shadow:
    inset 0 1.5px 0 rgba(255, 255, 255, 0.16),
    inset 0 -1px 0 rgba(0, 0, 0, 0.5),
    0 0 8px rgba(243, 139, 168, 0.4) !important;
}
.arch-quick-actions .action-btn.love-btn.active {
  color: #f38ba8 !important;
  text-shadow: 0 0 6px #f38ba8;
}

/* 2. Skip Pomodoro: Green Glow */
.arch-quick-actions .action-btn.skip-btn:hover {
  border-color: rgba(166, 227, 161, 0.35) !important;
  box-shadow:
    inset 0 1.5px 0 rgba(255, 255, 255, 0.16),
    inset 0 -1px 0 rgba(0, 0, 0, 0.5),
    0 0 8px rgba(166, 227, 161, 0.4) !important;
}

/* 3. Sync Weather: Teal-Blue Glow */
.arch-quick-actions .action-btn.sync-btn:hover {
  border-color: rgba(137, 180, 250, 0.35) !important;
  box-shadow:
    inset 0 1.5px 0 rgba(255, 255, 255, 0.16),
    inset 0 -1px 0 rgba(0, 0, 0, 0.5),
    0 0 8px rgba(137, 180, 250, 0.4) !important;
}

/* 4. Frosted Vignette: Lavender Glow */
.arch-quick-actions .action-btn.vignette-btn:hover {
  border-color: rgba(203, 166, 247, 0.35) !important;
  box-shadow:
    inset 0 1.5px 0 rgba(255, 255, 255, 0.16),
    inset 0 -1px 0 rgba(0, 0, 0, 0.5),
    0 0 8px rgba(203, 166, 247, 0.4) !important;
}
.arch-quick-actions .action-btn.vignette-btn.active {
  color: #cba6f7 !important;
  text-shadow: 0 0 6px #cba6f7;
}

/* 5. Palette Shifter: Yellow Glow */
.arch-quick-actions .action-btn.palette-btn:hover {
  border-color: rgba(249, 226, 175, 0.35) !important;
  box-shadow:
    inset 0 1.5px 0 rgba(255, 255, 255, 0.16),
    inset 0 -1px 0 rgba(0, 0, 0, 0.5),
    0 0 8px rgba(249, 226, 175, 0.4) !important;
}
```

---

## ⚡ 4. Dynamic Animation Mechanics (Anime.js)

### Expand Transition (Right Click)
- When `isTrayOpen` shifts to `true`:
  - Reset any active Anime.js instances on `.island-arch`, `.arch-quick-actions`, and `.action-btn`.
  - Animate `.island-arch` to `maxWidth: 450` (ample limit to hold expanded children) with spring physics `spring(1, 95, 15, 0)`.
  - Animate `.arch-quick-actions` to `opacity: 1` and `marginLeft: 12` using a swift `easeOutQuad` over 400ms.
  - Apply an entry stagger delay to nested `.action-btn` buttons: `scale: [0.75, 1]`, `opacity: [0, 1]`, duration: 450ms, delay: `anime.stagger(45, { start: 150 })`.

### Collapse Transition (Outside Click / Right Click toggle)
- When `isTrayOpen` shifts to `false`:
  - Reset active Anime.js instances on `.island-arch`, `.arch-quick-actions`, and `.action-btn`.
  - Animate `.island-arch` back to `maxWidth: 32` using `cubic-bezier(0.16, 1, 0.3, 1)` over 450ms.
  - Animate `.arch-quick-actions` back to `opacity: 0` and `marginLeft: 0` using `cubic-bezier(0.16, 1, 0.3, 1)` over 300ms.

---

## 🧩 5. Feature Action Handlers

### 1. Last.fm Love Track Toggle
- Updates React state `isLoved`. Spawns 5 floating red micro heart particles that float upward and fade out with elastic scale dynamics.

### 2. Pomodoro FSM State Skipper
- Calls the internal transition callback of `PomodoroWidget` to cycle mode immediately, rotating the skip icon by 360 degrees using spring physics.

### 3. Force Weather Sync
- Clears weather cached states, spins the weather icon using Anime.js, and triggers the weather sync event.

### 4. Frosted Glass Vignette Switcher
- Toggles class `.cyber-vignette-overlay` with a dark radial glass gradient.

### 5. Palette Shifter (Color Wave Cycle)
- Cycles through 4 predefined sets of CSS primary colors and transitions all capsules with a fluid background wave.

---

## ♿ Accessibility & Performance Safeguards
1. **Motion Preferences:** Uses `prefers-reduced-motion` to bypass all slide/spring transformations, resolving immediate states with `anime.set()`.
2. **Memory Cleanup:** Registers all Anime.js instances in the global `AnimationManager` to ensure clean unmounting.
