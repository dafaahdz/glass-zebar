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

---

## 📚 6. Zebar API Reference (TypeScript Typings)

Use these type signatures to construct provider configs and use outputs directly without extra research:

### Global Methods
- `zebar.createProviderGroup(configMap: ProviderGroupConfig): ProviderGroup`
- `zebar.createProvider(config: ProviderConfig): Provider`
- `zebar.currentWidget(): Widget`
- `zebar.startWidget(widgetName: string, placement: WidgetPlacement, args: StartWidgetArgs): Promise<void>`
- `zebar.startWidgetPreset(widgetName: string, presetName: string, args?: StartWidgetPresetArgs): Promise<void>`
- `zebar.shellExec(program: string, args?: string | string[], options?: ShellCommandOptions): Promise<ShellExecOutput>`
- `zebar.shellSpawn(program: string, args?: string | string[], options?: ShellCommandOptions): Promise<ShellProcess>`

### Providers Config & Output Map

#### 1. GlazeWM (`type: 'glazewm'`)
Exposes workspace and monitor info from the GlazeWM tiling window manager.
- **Output:**
  ```typescript
  displayedWorkspace: Workspace;
  focusedWorkspace: Workspace;
  currentWorkspaces: Workspace[];
  allWorkspaces: Workspace[];
  allMonitors: Monitor[];
  allWindows: Window[];
  focusedMonitor: Monitor;
  currentMonitor: Monitor;
  focusedContainer: Container;
  tilingDirection: TilingDirection; // 'horizontal' | 'vertical'
  bindingModes: BindingModeConfig[];
  isPaused: boolean;
  runCommand(command: string, subjectContainerId?: string): Promise<RunCommandResponse>;
  ```

#### 2. Media (`type: 'media'`)
Exposes playback controls and metadata for currently active media players.
- **Output:**
  ```typescript
  currentSession: MediaSession | null;
  allSessions: MediaSession[];
  play(options?: MediaControlOptions): void;
  pause(options?: MediaControlOptions): void;
  togglePlayPause(options?: MediaControlOptions): void;
  next(options?: MediaControlOptions): void;
  previous(options?: MediaControlOptions): void;
  ```
- **`MediaSession` Object:**
  ```typescript
  sessionId: string;
  title: string | null;
  artist: string | null;
  albumTitle: string | null;
  albumArtist: string | null;
  trackNumber: number;
  startTime: number; // in seconds
  endTime: number;   // in seconds (total duration)
  position: number;  // in seconds (updates every 5s from GSMTC timeline refresh)
  isPlaying: boolean;
  isCurrentSession: boolean;
  ```

#### 3. Audio (`type: 'audio'`)
Controls system audio devices and master volume.
- **Output:**
  ```typescript
  defaultPlaybackDevice: AudioDevice | null;
  defaultRecordingDevice: AudioDevice | null;
  playbackDevices: AudioDevice[];
  recordingDevices: AudioDevice[];
  setVolume(volume: number, options?: SetVolumeOptions): Promise<void>;
  setMute(mute: boolean, options?: SetMuteOptions): Promise<void>;
  ```
- **`AudioDevice` Object:**
  ```typescript
  deviceId: string;
  name: string;
  volume: number; // 0-100
  type: AudioDeviceType; // 'playback' | 'recording'
  isDefaultPlayback: boolean;
  isDefaultRecording: boolean;
  isMuted: boolean;
  ```

#### 4. Battery (`type: 'battery'`)
Exposes battery charge percentage and power state.
- **Output:**
  ```typescript
  chargePercent: number; // 0-100
  cycleCount: number;
  healthPercent: number;
  powerConsumption: number; // in Watts
  state: 'discharging' | 'charging' | 'full' | 'empty' | 'unknown';
  isCharging: boolean;
  timeTillEmpty: number | null; // in minutes
  timeTillFull: number | null;
  voltage: number | null;
  ```

#### 5. Network (`type: 'network'`)
Exposes network interfaces and internet speed traffic.
- **Output:**
  ```typescript
  defaultInterface: NetworkInterface | null;
  defaultGateway: NetworkGateway | null;
  interfaces: NetworkInterface[];
  traffic: NetworkTraffic | null;
  ```
- **`NetworkInterface` Object:**
  ```typescript
  name: string;
  friendlyName: string | null;
  description: string | null;
  type: InterfaceType; // 'wifi' | 'ethernet' | 'loopback' | etc.
  ipv4Addresses: string[];
  ipv6Addresses: string[];
  macAddress: string | null;
  isDefault: boolean;
  ```
- **`NetworkGateway` Object:**
  ```typescript
  ssid: string | null; // WiFi SSID
  signalStrength: number | null; // 0-100
  ```
- **`NetworkTraffic` Object:**
  ```typescript
  received: DataSizeMeasure; // bytes, siValue, siUnit, iecValue, iecUnit
  transmitted: DataSizeMeasure;
  ```

#### 6. CPU (`type: 'cpu'`)
Exposes system processor statistics.
- **Output:**
  ```typescript
  frequency: number; // in MHz
  usage: number; // 0-100
  logicalCoreCount: number;
  physicalCoreCount: number;
  vendor: string;
  ```

#### 7. Memory (`type: 'memory'`)
Exposes RAM and swap utilization statistics.
- **Output:**
  ```typescript
  usage: number; // 0-100
  freeMemory: number; // in bytes
  usedMemory: number;
  totalMemory: number;
  ```

#### 8. Weather (`type: 'weather'`)
Exposes current weather conditions.
- **Output:**
  ```typescript
  isDaytime: boolean;
  status: WeatherStatus; // 'clear_day' | 'clear_night' | 'cloudy_day' | etc.
  celsiusTemp: number;
  fahrenheitTemp: number;
  windSpeed: number;
  ```

#### 9. Date (`type: 'date'`)
Exposes current system date/time.
- **Output:**
  ```typescript
  formatted: string; // Formatted date string based on `formatting` option
  new: Date;
  now: number; // epoch ms
  iso: string;
  ```

#### 10. Systray (`type: 'systray'`)
Exposes active system tray icons and provides mouse action handlers.
- **Output:**
  ```typescript
  icons: SystrayIcon[];
  onHoverEnter(iconId: string): Promise<void>;
  onHoverLeave(iconId: string): Promise<void>;
  onHoverMove(iconId: string): Promise<void>;
  onRightClick(iconId: string): Promise<void>;
  onLeftClick(iconId: string): Promise<void>;
  onMiddleClick(iconId: string): Promise<void>;
  ```
- **`SystrayIcon` Object:**
  ```typescript
  id: string;
  iconUrl: string; // Data URL for rendering icon in <img>
  tooltip: string;
  ```
