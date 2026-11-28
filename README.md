# AeroPulse — Urban Air Quality & Health Assistant 🌬️

AeroPulse is an urban environmental intelligence platform and AI health assistant built with **React 19**, **TypeScript (strict mode)**, **Vite**, **Tailwind CSS v4**, and **Leaflet**. It translates raw atmospheric pollutant feeds into personalized clinical advisories, predictive smog forecasts, multi-city comparisons, and conversational guidance.

---

## 🎯 Problem Statement

Urban residents, commuters, and clinically vulnerable groups (asthma patients, children, older adults, and cardiovascular patients) lack accessible tools that bridge **raw atmospheric sensor telemetry** with **actionable daily health decisions**:
- Raw pollutant readings ($\text{PM}_{2.5}$, $\text{PM}_{10}$, $\text{NO}_2$, $\text{O}_3$, $\text{CO}$, $\text{SO}_2$) are difficult to interpret against World Health Organization (WHO) safety thresholds.
- Static AQI numbers rarely translate into profile-specific actions (e.g., whether it is safe to run, when to open windows, what mask rating filters fine particulates, or what HEPA purifier speed is needed).
- Existing dashboards frequently require paid API keys or fail when external LLM/tile providers are unavailable.

## 💡 Solution Overview

AeroPulse solves this end-to-end with zero-friction setup:
1. **Real-Time Dual Indexing & Meteorology:** Fetches live global air quality and weather telemetry via **Open-Meteo** (no API key required) with in-memory TTL caching and request deduplication, presenting both **US EPA AQI (0–500)** and **European AQI** alongside temperature (°C/°F), humidity, and wind dispersion vectors.
2. **WHO 2021 Criteria Pollutant Breakdown:** Calculates exceedance ratios across all 6 criteria pollutants with expandable emission-source and health-impact panels.
3. **Personalized Health Advisory & Automated Threshold Alerts:** Tailors exercise, mask, ventilation, and HEPA guidance across 6 health profiles and triggers automated alerts whenever AQI exceeds your configured safety threshold.
4. **Multi-City Watchlist & 7-Day Smog Trajectory:** Compares pinned cities side-by-side and visualizes 24-hour / 7-day pollutant curves highlighting peak smog hours and cleanest outdoor windows.
5. **Zero-Key OpenStreetMap Dispersion Map:** Renders city dispersion radii, hotspots, and wind vectors over **OpenStreetMap** and **Esri** basemaps using safe DOM APIs.
6. **Dual-Mode AI Assistant:** Integrates **Google Gemini** (`x-goog-api-key` header, live telemetry context injection) with an automatic offline **WHO/EPA Expert Rule Engine** fallback.

---

## 🏗️ Architecture

```text
src/
├── types/                  # Strict TypeScript domain models (airQuality.ts, chat.ts)
├── utils/
│   ├── aqiStandards.ts     # US EPA AQI categories & WHO 2021 exceedance ratios
│   ├── healthAdviser.ts    # Profile-aware clinical, mask, exercise & HEPA guidance
│   ├── security.ts         # Input/CSV sanitization, coordinate clamping, token redaction
│   ├── formatters.ts       # °C/°F conversion & 8-point wind bearing formatting
│   ├── markdownParser.tsx  # XSS-safe React AST markdown renderer (no dangerouslySetInnerHTML)
│   └── mapConfigs.ts       # OpenStreetMap/Esri basemaps & safe DOM pin/popup builders
├── services/
│   ├── openMeteoApi.ts     # TTL-cached Open-Meteo AQI, weather, geocoding & watchlist APIs
│   ├── offlineExpertEngine.ts # Deterministic WHO/EPA environmental intent engine
│   └── aiAssistant.ts      # Dual-mode Gemini + offline expert orchestrator
├── components/
│   ├── Navbar.tsx          # WCAG AA combobox city search, quick pills, GPS & actions
│   ├── AqiAlertBanner.tsx  # Threshold alert banner with protective action trigger
│   ├── AqiSpeedometer.tsx  # Accessible SVG AQI gauge & dispersion weather panel
│   ├── HealthAdviceCards.tsx # 6-profile tablist & 4 actionable advisory cards
│   ├── PollutantGrid.tsx   # WHO exceedance progress bars & expandable source info
│   ├── ForecastChart.tsx   # Memoized 24h/7d interactive SVG trajectory chart
│   ├── CityWatchlist.tsx   # Multi-city comparison & pinned watchlist
│   ├── UrbanPollutionMap.tsx # Lazy-loaded Leaflet dispersion map (OSM / Esri)
│   ├── AiChatDrawer.tsx    # Accessible dialog drawer with live context AI chat
│   ├── AuditReportModal.tsx# Copyable report & formula-safe CSV audit export
│   ├── SettingsModal.tsx   # Gemini API key, model, °C/°F, profile & threshold settings
│   ├── ToastBanner.tsx     # Non-blocking accessible status/alert notifications
│   └── ErrorBoundary.tsx   # Top-level graceful UI error recovery
└── test/                   # Vitest + React Testing Library setup & fixtures
```

---

## 🎬 Product Demo Video & Walkthrough

![AeroPulse Demo Walkthrough](./public/demo_walkthrough.gif)

> 📹 **Interactive Video Studio:** Open [demo_video.html](./public/demo_video.html) in your browser to watch the 75-second simulated walkthrough with closed captions and 1-click **Export MP4/WebM**.

---

## 🚀 Setup, Run & Verification

### Prerequisites
- Node.js (v18+)
- npm

### 1. Install Dependencies
```bash
npm install
```

### 2. Configure Environment (Optional)
AeroPulse works out of the box without any keys. To enable Google Gemini generative reasoning, copy `.env.example` to `.env` and add your key from [Google AI Studio](https://aistudio.google.com/app/apikey):
```env
VITE_GEMINI_API_KEY=your_gemini_api_key_here
```

### 3. Run Development Server
```bash
npm run dev
```
Open **[http://localhost:5173/](http://localhost:5173/)**.

### 4. Lint, Test, Coverage & Production Build
```bash
npm run lint           # Oxlint static analysis (0 warnings, 0 errors)
npm test               # Vitest unit & integration test suite
npm run test:coverage  # V8 code coverage report
npm run build          # Strict TypeScript check (tsc -b) + Vite production build
```

---

## 📊 Data Sources & Standards
- **Atmospheric & Weather Data:** [Open-Meteo Air Quality API](https://open-meteo.com/en/docs/air-quality-api) & Forecast API (Copernicus CAMS & NOAA GFS).
- **Mapping:** [OpenStreetMap](https://www.openstreetmap.org/) & Esri World Topographic Map.
- **Health Benchmarks:** World Health Organization (WHO) 2021 Global Air Quality Guidelines & US EPA AQI Standards.
