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

## Tool 1 — Delivery Tracker App (Android)

### Download

**[⬇ Download APK](app/delivery-tracker.apk)**

> Requires Android 8.0+. Allow installation from unknown sources in Settings → Security.

### Features

- **One-tap state machine** — idle → staking → notified → picking → delivered; each tap timestamps the transition
- **GPS recording** — captures coordinates at each state change via browser Geolocation API
- **Live weather** — temperature, precipitation, wind speed from Open-Meteo (no API key required)
- **Route segment classification** — waiting segment, dispatch segment, pickup-to-delivery segment, intercept segment
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

A desktop web tool that ingests the Excel file exported by the App and produces performance analytics.

### Features

- **Import** — drag-and-drop the App's Excel export
- **Route efficiency regression** — distance vs. time scatter with trend line
- **Empty-run rate** — percentage of distance traveled without an active order
- **Time distribution** — breakdown of working hours by segment type
- **Revenue burn rate** — earnings per minute across different time windows
- **NTD/min efficiency** — real effective hourly rate excluding wait time
- **Geographic heatmap** — pickup/delivery location density

### Tech Stack

Pure HTML + JavaScript, no build step required. Runs entirely in the browser.

| Library | Purpose |
|---|---|
| SheetJS | Excel file parsing |
| Chart.js | Statistical charts |
| Leaflet | Geographic visualization |

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

## Data Flow Detail

The App exports an Excel file with one row per delivery segment:

| Column | Description |
|---|---|
| 路段語意 | Segment type (waiting / dispatch / pickup / intercept) |
| 累計距離_公尺 | Cumulative distance (meters) |
| 時間戳記 | Timestamp of state transition |
| GPS座標 | Latitude / longitude at transition |
| 天氣_降雨量 | Precipitation at time of recording |
| 收入 | Declared earnings for this order |

The Analyzer reads this schema directly — no transformation needed.

---

## Related

- [3D Container Packing System](https://github.com/wutesta0101-hue/container-packing) — BLB bin-packing with forklift aisle constraints
