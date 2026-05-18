# Arch Quick-Actions Liquid Glass Drawer Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Refactor the Arch quick-actions drawer so that every button behaves as a miniature circular liquid glass capsule that expands horizontally to reveal its name and pushes neighbors to the right when hovered.

**Architecture:** Combine clean flexbox layout styling in CSS with hardware-accelerated transitions. Every button starts as a 24x24px frosted glass circle. Hovering over a button expands its `max-width` to 120px to reveal its title inline, automatically pushing neighboring buttons to the right, and adding a custom themed neon glow.

**Tech Stack:** React 18, Vanilla CSS, Anime.js

---

### Task 1: HTML Refactor for Inline Text Elements

**Files:**
- Modify: [index.html](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/index.html)

- [ ] **Step 1: Convert all floating tooltips into inline button label text spans**
  
  Locate the `.arch-quick-actions` container (around line 3161) in `test-widget/index.html` and change all `<span className="action-tooltip">` inside the action buttons to `<span className="action-btn-text">` to support inline flow and expansion.

  Target HTML block:
  ```html
  <div className="arch-quick-actions">
    <button
      className={`action-btn love-btn ${isLoved ? 'active' : ''}`}
      onClick={toggleLove}
    >
      <i className={`nf ${isLoved ? 'nf-md-heart' : 'nf-md-heart_outline'}`}></i>
      <span className="action-btn-text">Love Track</span>
    </button>

    <button className="action-btn skip-btn" onClick={skipPomodoro}>
      <i className="nf nf-md-skip_forward"></i>
      <span className="action-btn-text">Skip Pomodoro</span>
    </button>

    <button className="action-btn sync-btn" onClick={forceSyncWeather}>
      <i className="nf nf-md-autorenew"></i>
      <span className="action-btn-text">Sync Weather</span>
    </button>

    <button
      className={`action-btn vignette-btn ${isVignetteActive ? 'active' : ''}`}
      onClick={toggleVignette}
    >
      <i className="nf nf-md-vignette"></i>
      <span className="action-btn-text">Toggle Vignette</span>
    </button>

    <button className="action-btn palette-btn" onClick={cyclePalette}>
      <i className="nf nf-md-palette"></i>
      <span className="action-btn-text">Cycle Palette</span>
    </button>
  </div>
  ```

- [ ] **Step 2: Commit HTML structure changes**
  
  Stage and commit this change:
  ```bash
  git add test-widget/index.html
  git commit -m "feat: refactor arch quick actions JSX to support inline label spans"
  ```

---

### Task 2: CSS Base Styles for Glass Capsule Buttons

**Files:**
- Modify: [styles.css](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/styles.css)

- [ ] **Step 1: Define liquid glass pill styles for action buttons and collapsed state for label text**

  Locate and replace the `.arch-quick-actions .action-btn` and `.action-tooltip` rules in `test-widget/styles.css` (lines 1848 onwards) with the base capsule styling:

  ```css
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
  ```

- [ ] **Step 2: Commit CSS base capsule styles**

  Stage and commit this change:
  ```bash
  git add test-widget/styles.css
  git commit -m "style: define base liquid glass styles for quick-action buttons"
  ```

---

### Task 3: CSS Hover States & Kinetic Pushing

**Files:**
- Modify: [styles.css](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/styles.css)

- [ ] **Step 1: Set up hover expansion triggers and inline text sliding transitions**

  Append the hover state CSS rules to support `max-width` widening and label slide-in:

  ```css
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
  ```

- [ ] **Step 2: Commit CSS kinetic hover rules**

  Stage and commit this change:
  ```bash
  git add test-widget/styles.css
  git commit -m "style: implement CSS kinetic hover expansion and text slide-in transitions"
  ```

---

### Task 4: Custom Themed Neon Glowing Border Profiles

**Files:**
- Modify: [styles.css](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/styles.css)

- [ ] **Step 1: Add custom glowing shadows and active states to each button class**

  Append the specific neon color configurations for hover and active button states:

  ```css
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

- [ ] **Step 2: Commit glowing profiles**

  Stage and commit this change:
  ```bash
  git add test-widget/styles.css
  git commit -m "style: add custom ambient neon glowing borders and active text-shadows"
  ```
