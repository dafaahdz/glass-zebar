# Spring-Physics Network Drawer Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Upgrade the legacy CSS hover transition in the Network Widget into a premium, physics-realistic expansion drawer powered by Anime.js.

**Architecture:** Bind DOM refs, disable conflict CSS transitions, and run parallel, staggered Anime.js timelines on mouse enter/leave with reduced-motion compliance.

**Tech Stack:** React 18, Anime.js, CSS, HTML

---

### Task 1: Clean Up CSS Transitions

**Files:**
- [test-widget/styles.css](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/styles.css)

- [ ] **Step 1: Strip max-width and margin transitions from styling**

Remove legacy static maximum widths and CSS properties under `.network` and `.network-traffic`.

Replace:
```css
.network {
  width: auto !important;
  max-width: 145px;
  display: inline-flex;
  align-items: center;
  overflow: hidden;
  white-space: nowrap;
  position: relative;
  padding-right: 16px !important;
  letter-spacing: -0.01em;
  transition:
    max-width 0.85s cubic-bezier(0.16, 1, 0.3, 1),
    background 0.4s ease,
    border-color 0.4s ease,
    box-shadow 0.4s ease !important;
}

.network.expanded {
  max-width: 400px;
  border-color: var(--network-signal-color) !important;
}
```

With:
```css
.network {
  width: auto !important;
  max-width: 145px;
  display: inline-flex;
  align-items: center;
  overflow: hidden;
  white-space: nowrap;
  position: relative;
  padding-right: 16px !important;
  letter-spacing: -0.01em;
  transition:
    background 0.4s ease,
    border-color 0.4s ease,
    box-shadow 0.4s ease !important;
}

.network.expanded {
  border-color: var(--network-signal-color) !important;
}
```

And Replace:
```css
.network-traffic {
  margin-left: 0;
  opacity: 0;
  max-width: 0;
  overflow: hidden;
  display: inline-block;
  transition:
    margin-left 0.85s cubic-bezier(0.16, 1, 0.3, 1) 0.15s,
    opacity 0.6s ease 0.15s,
    max-width 0.85s cubic-bezier(0.16, 1, 0.3, 1) 0.15s;
  font-family: 'Outfit', 'Geist Mono', monospace;
  font-size: 11px;
  white-space: nowrap;
  flex-shrink: 0;
  will-change: margin-left, opacity, max-width;
}
```

With:
```css
.network-traffic {
  margin-left: 0;
  opacity: 0;
  overflow: hidden;
  display: inline-block;
  transition: none !important;
  font-family: 'Outfit', 'Geist Mono', monospace;
  font-size: 11px;
  white-space: nowrap;
  flex-shrink: 0;
}
```

---

### Task 2: Implement Anime.js Expansion Timelines

**Files:**
- [test-widget/index.html](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/index.html)

- [ ] **Step 1: Integrate DOM refs and useEffect inside NetworkWidget**

Update `NetworkWidget` component definition to track `containerRef` and `trafficRef`, and trigger Anime.js spring/liquid timelines inside `useEffect` bound to `isHovered`.

---

### Task 3: Verification & Validation

- [ ] **Step 1: Run syntactical lint check or browser validation**

Confirm that no HTML/CSS syntax errors exist and variables are cleanly mapped.

- [ ] **Step 2: Commit the changes**

Commit changes with Conventional Commits description:
```bash
git add test-widget/styles.css test-widget/index.html
git commit -m "feat: implement spring-physics expansion drawer for network widget"
```
