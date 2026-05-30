# Delivery Tracker

A two-tool chain for gig economy delivery workers — record routes on mobile, analyze performance on desktop.

**Analyzer Live Demo → [wutesta0101-hue.github.io/delivery-tracker](https://wutesta0101-hue.github.io/delivery-tracker)**

---

## The Problem

Gig delivery workers have no visibility into their own performance. Platform apps show earnings but hide the data that matters: which routes are profitable, how much time is spent waiting vs. riding, and what the real hourly rate is after factoring in empty runs.

This toolchain fills that gap.

---

## Two Tools, One Data Flow

```
📱 Delivery Tracker App          📊 Delivery Analyzer (Web)
─────────────────────────        ──────────────────────────
Record in real time    ──────►  Import Excel → Visualize
GPS + weather + status          Route efficiency regression
One-tap state machine           Empty-run rate analysis
Export → Excel (.xlsx)          Time distribution charts
```

The App produces a structured Excel file. The Analyzer ingests it. Same data, two different use contexts.

---

## What the Analyzer Shows

### 地理軌跡圖 — Where did you actually ride?

![Geographic Route Map](https://github.com/user-attachments/assets/985aeac3-08d3-4401-83c9-21d7fbca5cf8)

Each color represents one order group. Node shapes distinguish dispatch notifications (circle), pickup (square), and delivery (triangle). Reveals geographic patterns: which zones cluster, how far each order pulls you from base.

---

### 時間效率分析 — What is your real NTD/min?

![Time Efficiency Analysis](https://github.com/user-attachments/assets/b8c1bb93-812c-4991-a7e7-d3c355fa22ff)

Cumulative NTD/min (green line) vs. instantaneous NTD/min per delivery (orange diamonds). The dashed line shows session average. Early high-value orders pull the average up; long-distance late orders drag it down.

---

### 空車率分析 — How much distance are you riding empty?

![Empty-Run Rate Analysis](https://github.com/user-attachments/assets/b6affed3-ed63-4fa4-bb03-d1ba93219c40)

Blue = distance with active cargo. Gray = empty distance. Orange dashed line = empty-run rate per hour. High empty-run rate in the first hour is normal (cold start); sustained high rate signals poor dispatch zone positioning.

---

### Time Efficiency — Revenue vs. Trip Time

![Time Efficiency Scatter](https://github.com/user-attachments/assets/49ccd7a7-ee13-4889-9e79-2dc1bee9fa7b)

Scatter plot of revenue (NTD) vs. trip time (minutes) per order group. Pink regression = all orders (~180 NTD/hr). Green regression = top 25% efficient orders (~300 NTD/hr). Outliers above the green line are the orders worth chasing.

---

### 工作時段分布 — Which hour has the most waiting?

![Work Period Distribution](https://github.com/user-attachments/assets/4581e199-7643-4494-8806-a94d34b349a3)

Stacked bar chart per hour: waiting (purple), pickup (orange), delivery (green), intercept (pink), multi-pickup (yellow). Thick green = efficient hour. Thick purple = you're sitting still. Identifies the dead hours worth avoiding.

---

### 每組工時結構 — Effective vs. empty time per order

![Per-Order Time Structure](https://github.com/user-attachments/assets/5ac71bc9-14b9-4c76-91a0-c7f065c46e3b)

Horizontal bar per order group: green = effective riding time, blue = empty riding time, gray = inter-order wait. Long blue bars = dispatch sent you too far from the next pickup. Long gray bars = platform slow to assign next order.

---

## Tool 1 — Delivery Tracker App (Android)

![App Screenshot](https://github.com/user-attachments/assets/2596aebd-73c9-4036-8cc7-6effcc073760)

### Download

**[⬇ Download APK](app/delivery-tracker.apk)**

> Requires Android 8.0+. Allow installation from unknown sources in Settings → Security.

### Features

- **One-tap state machine** — idle → staking → notified → picking → delivered; each tap timestamps the transition
- **GPS recording** — captures coordinates at each state change via browser Geolocation API
- **Live weather** — temperature, precipitation, wind speed from Open-Meteo (no API key required)
- **Route segment classification** — waiting, dispatch, pickup-to-delivery, intercept, multi-pickup segments
- **Excel export** — one-tap export via Capacitor Filesystem; structured for direct import into the Analyzer
- **Taiwan holiday detection** — flags national holidays for comparative analysis

### Tech Stack

| Layer | Technology |
|---|---|
| UI | React 18 (CDN UMD), Babel standalone |
| Mobile packaging | Capacitor 8 (Android) |
| Storage | Capacitor Filesystem (app) / localStorage (web) |
| Weather API | Open-Meteo (free, no key) |
| Geocoding | Nominatim / OpenStreetMap (free, no key) |
| Route distance | OSRM (free, no key) |
| Export | SheetJS (xlsx) |

### Environment Detection

The same codebase runs in both environments. Storage backend switches automatically:

```javascript
const isNative = window.Capacitor?.isNativePlatform?.();
// Native (Android) → Capacitor Filesystem
// Browser         → localStorage
```

---

## Tool 2 — Delivery Analyzer (Web)

**Live Demo → [wutesta0101-hue.github.io/delivery-tracker](https://wutesta0101-hue.github.io/delivery-tracker)**

A desktop web tool that ingests the Excel file exported by the App and produces performance analytics. Pure HTML + JavaScript, no build step, runs entirely in the browser.

| Library | Purpose |
|---|---|
| SheetJS | Excel file parsing |
| Chart.js | Statistical charts |
| D3.js | Geographic route map |

---

## Project Structure

```
delivery-tracker/
├── app/
│   └── delivery-tracker.apk     ← Android APK (direct install)
├── analyzer/
│   └── index.html               ← Web analyzer (GitHub Pages)
└── README.md
```

---

## Related

- [3D Container Packing System](https://github.com/wutesta0101-hue/container-packing) — BLB bin-packing with forklift aisle constraints
