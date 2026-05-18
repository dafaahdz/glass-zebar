# Implementation Plan: Anime.js Luxury Animation Suite

This document outlines the step-by-step refactoring stages to deploy the unified `useAnime` custom hook and its corresponding physics, states, and diagnostics across the entire Glass-Pack Status Bar.

---

## 📈 Refactoring Sequence

```
+-------------------------------------------------------------+
|                      REFACTORING TASKS                      |
+-------------------------------------------------------------+
| [ ] Phase 0 & 1: Core Infrastructure Setup                   |
|     - Inject EASINGS, AnimationManager, and useAnime hook   |
| [ ] Phase 2: Media Suite Migration                          |
|     - Port MediaAlbumWidget (Vinyl rot + SVG Progress ring) |
|     - Port MediaWidget Marquee (dimension measurer + transl) |
| [ ] Phase 3: Physics & Springs Migration                     |
|     - Morph SVG path wave inside BatteryWidget              |
|     - Attach elasticClickPop with Reduced Motion to pills   |
| [ ] Phase 4: State-Driven Mapping & Easter Eggs              |
|     - Bind Pomodoro FSM mode to breathing scale pulse       |
|     - Attach outward stagger delay to WorkspaceWidget       |
|     - Link ToastItem linear progress transitions            |
|     - Integrate diagnostics & Konami Sequence               |
+-------------------------------------------------------------+
```

---

## 🛠️ Phase-by-Phase Technical Details

Refer to the master [implementation_plan.md](../../../../../../../.gemini/antigravity/brain/2039c836-0d81-4f0a-84c1-d486bd42676d/implementation_plan.md) for full blueprints of:

1.  **Core Infrastructure:** `useAnime` hook and `AnimationManager`.
2.  **Media Suite:** `MediaAlbumWidget` vinyl spin and `MediaMarquee` constant velocity translator.
3.  **Physics & Springs:** `BatteryWidget` morphing SVG liquid waves and `StatusPill` click pops with accessibility checks.
4.  **States & Easter Eggs:** State-driven Pomodoro pulse, stagger Workspace ripple, linear Toast timers, and the 3D Konami jump-spin sequence.
