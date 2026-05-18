# Design Specification: Magnetic Hover Reduction

This document outlines the changes to reduce the hover magnetic offset displacement across all status pills.

---

## 🎯 1. Core Problem & Objective
The JS-based magnetic hover effect calculated inside `<StatusPill />` applied a displacement multiplier of `0.1` (X) and `0.2` (Y) to follow the mouse cursor. This resulted in an overly slippery, high-displacement movement that felt unstable.

**Objective:**
Reduce the magnetic translation multipliers by 60% to achieve an ultra-subtle, expensive, and premium micro-interaction that provides feedback without layout instability.

---

## 🎨 2. Blueprint & Multiplier Variables
- **Old Multiplier X:** `0.1`
- **Old Multiplier Y:** `0.2`
- **New Multiplier X:** `0.04`
- **New Multiplier Y:** `0.08`

---

## 🧩 3. Proposed Component Changes

### [index.html](file:///c:/Users/aipus/.glzr/zebar/glass-pack/test-widget/index.html)
- Locate `handleMouseMove` inside `StatusPill`.
- Modify X and Y multiplier operations.
