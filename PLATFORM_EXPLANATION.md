# HELIOS AI — Complete Platform Explanation
### L&T Techgium 2026 | 15-Minute Presentation Guide

---

## 🔴 THE PROBLEM (2 Minutes)

### India's Solar Crisis — ₹47,000 Crore Annual Loss

India has **63 GW+** of installed solar capacity and is targeting **500 GW by 2030**. But solar farms are suffering from a silent crisis:

| Problem | Impact |
|---------|--------|
| Panel faults go undetected for weeks | ₹47,000 crore revenue loss annually |
| Manual inspection is slow | 1 technician can inspect ~50 panels/day |
| EL imaging requires panel shutdown + darkness | Entire string goes offline during testing |
| InGaAs cameras cost ₹5-10 lakhs each | Unaffordable for most O&M operators |
| Root cause analysis needs expert technicians | Shortage of skilled workforce |

### The Real Bottleneck

The gold-standard for detecting micro-cracks, PID, and cell degradation is **Electroluminescence (EL) Imaging**. But traditional EL requires:
1. Complete panel shutdown (lost revenue)
2. Total darkness (only at night)
3. Expensive InGaAs cameras (₹5-10 lakhs)
4. Trained technicians (scarce in India)

**Result**: Most solar farms never perform EL testing. Faults compound, revenue bleeds.

---

## 🟢 OUR SOLUTION — HELIOS AI (3 Minutes)

### One-Line Pitch
> **HELIOS AI is a GenAI-powered solar farm monitoring platform that replaces ₹10 lakh EL cameras with AI, detects faults 24/7 without shutdown, and tells you exactly WHY a panel is failing — in plain language.**

### Three Breakthrough Innovations

#### Innovation 1: Virtual Electroluminescence (Virtual EL)
- **What**: Takes a normal RGB daytime photo of a solar panel and generates an EL-equivalent image using Generative AI
- **How**: RGB Image → Computer Vision Pipeline (Edge Detection + Canny + Adaptive Threshold) → Generative AI (Stable Diffusion / Pix2Pix via Together.ai/Replicate) → Virtual EL Image
- **Why it matters**: Eliminates need for panel shutdown, darkness, and ₹5-10 lakh cameras
- **Technical Pipeline**:
  1. RGB image fed to CV pipeline: Grayscale conversion → Bilateral filtering → CLAHE → Adaptive threshold
  2. Edge detection via Canny algorithm extracts micro-crack patterns
  3. If available, Stable Diffusion (Together.ai) or Pix2Pix (Replicate) generates photorealistic EL
  4. Fallback: Advanced CV pipeline always works even without API keys
  5. Defect analysis identifies micro-cracks, cell damage, hotspot markers
- **Code**: `backend/app/services/virtual_el.py` — 369 lines of real implementation

#### Innovation 2: Explainable AI Diagnostics
- **What**: Instead of just saying "fault detected", HELIOS AI explains the diagnosis in natural language with evidence
- **How**: Gemini 1.5 Flash (Vision-Language Model) analyzes thermal and visual images, returns structured JSON with:
  - Specific defect identification with location
  - Health score (0-1)
  - Confidence level
  - Natural language explanation for non-expert operators
  - Specific maintenance recommendations
- **Why it matters**: Plant operators don't need PhD-level expertise to understand diagnostics
- **IEC 62446-3 Compliance**: Thermal analysis follows international standards:
  - ΔT < 10°C → Normal (Class 0)
  - 10°C ≤ ΔT < 20°C → Moderate (Class 1) — Monitor
  - 20°C ≤ ΔT < 40°C → Significant (Class 2) — Action within 1 week
  - ΔT ≥ 40°C → Critical (Class 3) — Immediate intervention
- **Code**: `backend/app/services/gemini_vision.py` — 318 lines, `backend/app/services/thermal_analysis.py` — 380 lines

#### Innovation 3: Multi-Modal Root Cause Analysis (LLM Fusion)
- **What**: Combines ALL data sources (electrical + EL + thermal + environmental) and uses LLaMA 3.3 70B to determine root cause
- **How**: 
  1. Electrical data: Voltage, current, power deviation from 369W nominal
  2. Virtual EL results: Micro-crack count, cell damage locations
  3. Thermal analysis: Hotspot severity, IEC class rating
  4. Environmental context: Location (Shirpur, Maharashtra), dust rate, days since cleaning
  5. All fed to LLaMA 3.3 70B via Groq Cloud with expert system prompt
- **The "Dr. Aditya Sharma" Expert System**: LLaMA acts as a world-renowned solar diagnostician who:
  - Cross-references electrical deviations with EL defects
  - Applies Fault Signature Database (7 fault types × 3 modalities)
  - Produces: Root cause, confidence score, reasoning chain, costed maintenance actions, differential diagnosis
- **Why it matters**: This is true multi-modal AI — not just running 3 separate models, but **fusing their outputs through an LLM that reasons across all modalities**
- **Code**: `backend/app/services/groq_client.py` — 240 lines, `backend/app/services/ai_service.py` — 199 lines

---

## 🔵 ARCHITECTURE WALKTHROUGH (3 Minutes)

### System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      USER (Browser)                          │
│                    React 18 + Vite 7.3                       │
└──────────────┬──────────────────────────────┬───────────────┘
               │ REST API                      │ Real-time
               ▼                               ▼
┌──────────────────────┐          ┌──────────────────────────┐
│   FastAPI Backend     │          │  Firebase Realtime DB     │
│   Python 3.12         │◄────────►│  (helios-d3b07)          │
│   Port 8000           │          │  Live Panel/Alert Sync   │
└──────┬──────┬────────┘          └──────────────────────────┘
       │      │
       ▼      ▼
┌──────────┐ ┌──────────────┐ ┌──────────────┐ ┌─────────────┐
│ Groq     │ │ Gemini 1.5   │ │ HuggingFace  │ │ Together.ai  │
│ Cloud    │ │ Flash        │ │ BLIP         │ │ Stable Diff. │
│ LLaMA    │ │ Vision       │ │ Captioning   │ │ Virtual EL   │
│ 3.3 70B  │ │ Analysis     │ │              │ │              │
└──────────┘ └──────────────┘ └──────────────┘ └─────────────┘
```

### Frontend (React 18 + Vite)

| Component | Function |
|-----------|----------|
| **PanelGrid** | 247-panel visual matrix with color-coded status LEDs (green/yellow/red) |
| **StatsCards** | Real-time KPIs: Total Power, Active Panels, Avg Efficiency, Alerts |
| **AlertPanel** | Live alert feed with severity, resolve button, auto-refresh |
| **DiagnosticsPanel** | AI analysis trigger — runs full Virtual EL + Thermal + Root Cause pipeline |
| **AnalyticsCharts** | Recharts-powered graphs: power trends, efficiency distribution, temperature |
| **HardwareStatus** | Real API health checks — pings backend endpoints, shows cloud service status |
| **Chatbot** | Natural language interface — ask questions about any panel, get expert answers |
| **ImageUpload** | Upload real panel images for AI analysis |
| **WorkOrders** | Maintenance tracking generated from AI recommendations |
| **SettingsPanel** | 8 real settings: refresh interval, units, compact view, alert sounds |
| **PanelDetailModal** | Deep-dive: panel metrics, EL image, thermal image, root cause, cost |

**State Management**: Zustand with `persist` middleware — settings survive browser refresh (localStorage).

**Real-time Sync**: Firebase SDK's `onValue()` listeners — panels/alerts/stats update instantly across all connected browsers.

### Backend (FastAPI + Python 3.12)

| Service | API | Purpose |
|---------|-----|---------|
| `ai_service.py` | `POST /analyze-panel/{id}` | Orchestrates full 3-phase AI pipeline |
| `virtual_el.py` | (internal) | RGB → Virtual EL via CV + GenAI |
| `thermal_analysis.py` | (internal) | Thermal image generation + IEC 62446-3 analysis |
| `gemini_vision.py` | (internal) | Google Gemini 1.5 Flash vision analysis |
| `groq_client.py` | (internal) | LLaMA 3.3 70B root cause + Fault Signature DB |
| `chatbot_service.py` | `POST /chat` | Context-aware solar farm chatbot |
| `vision_ai.py` | (internal) | Vision-Language analysis via Groq |
| `hf_client.py` | (internal) | HuggingFace BLIP image captioning |
| `sensor_simulator.py` | `GET /sensor-status` | Sensor data simulation for POC |
| `firebase.py` | (internal) | Firebase Realtime DB CRUD |
| `supabase_client.py` | (internal) | Supabase for analysis result archival |

**Key Design Pattern**: Async everywhere — `asyncio.gather()` runs Virtual EL and Thermal analysis in parallel, then feeds both to Root Cause LLM sequentially.

### Database Architecture

| Database | Purpose | Data |
|----------|---------|------|
| **Firebase Realtime DB** | Live operational data | `/panels` (247 entries), `/alerts`, `/stats` |
| **Supabase (PostgreSQL)** | Historical analysis archive | AI results, time-series data |

### Deployment

| Layer | Service | URL |
|-------|---------|-----|
| Frontend | Vercel | Auto-deployed from GitHub |
| Backend | Render | https://helios-wiro.onrender.com |
| Database | Firebase | helios-d3b07 |
| LLM | Groq Cloud | LLaMA 3.3 70B (free tier) |
| Vision | Google AI | Gemini 1.5 Flash (free tier) |

---

## 🟡 LIVE DEMO WALKTHROUGH (4 Minutes)

### Step 1: Dashboard Overview (30 sec)
Open the app → Show the main dashboard:
- **247 panels** displayed as color-coded grid (green = healthy, yellow = warning, red = critical)
- **4 stat cards** at top: Total Power Output, Active Panels, Average Efficiency, Critical Alerts
- Point out real-time numbers updating

### Step 2: Click a Critical Panel (45 sec)
Click any red panel → **PanelDetailModal** opens:
- Show: Panel ID, Zone, Row, Position
- Show: Voltage (V), Current (A), Power (W), Temperature (°C), Efficiency (%)
- Show: Current diagnosis text
- Click **"Run AI Analysis"** button

### Step 3: AI Analysis Pipeline (60 sec)
Watch the 3-phase pipeline execute:
1. **Virtual EL Image** appears — generated from panel data using CV + AI
   - Point out: "This replaces a ₹10 lakh InGaAs camera"
   - Show defect markers (red rectangles on micro-cracks)
2. **Thermal Image** appears — realistic heatmap with IEC classification
   - Point out the color scale and ΔT values
   - Show Class rating (Class 0/1/2/3)
3. **Root Cause Analysis** appears — LLaMA 3.3 70B output:
   - Root cause (e.g., "Bypass diode failure with thermal runaway")
   - Confidence: 94%
   - Reasoning chain (multi-modal evidence synthesis)
   - Maintenance steps with cost estimate in ₹
   - Differential diagnosis (what was ruled out and why)

### Step 4: Chatbot (30 sec)
Open chatbot → Type: "Which panels need immediate attention?"
- HELIOS AI Assistant fetches live farm context from Firebase
- Responds with specific panel IDs, their metrics, and recommended actions
- Show bilingual capability (Hindi/English)

### Step 5: Settings (15 sec)
Open Settings → Show real functionality:
- Toggle **Compact View** → Grid switches to dense mode
- Change **Temperature Unit** → °C ↔ °F across all views
- Change **Refresh Interval** → Data auto-refreshes at set interval
- Toggle **Alert Sounds** → Double-beep plays on new critical alerts

### Step 6: Image Upload (30 sec)
Go to Image Upload → Upload a real solar panel photo:
- Backend processes through Gemini 1.5 Flash
- Returns: Visual defect assessment, health score, recommendations
- Show that it works on ANY panel image, not just our 247 simulated panels

### Step 7: Alert Management (15 sec)
Show AlertPanel → Click **"Resolve"** on an alert:
- Alert gets cleared from Firebase
- Real-time update across all views
- Show that this represents actual O&M workflow

---

## 🟣 HARDWARE ABSTRACTION — PRODUCTION READINESS (2 Minutes)

### The Question: "Where's the hardware?"

HELIOS AI is a **software-first POC** with a **production-ready hardware abstraction layer** built in.

### Hardware Abstraction Layer (HAL) Design

```
┌───────────────────────────────────────────────┐
│              Application Layer                 │
│          (AI Service, Dashboard)               │
└──────────────┬────────────────────────────────┘
               │  Unified Interface
               ▼
┌───────────────────────────────────────────────┐
│         SensorInterface (Abstract)             │
│  - read_temperature()                          │
│  - read_voltage()                              │
│  - read_current()                              │
│  - read_irradiance()                           │
└──┬──────────┬──────────────┬──────────────┬───┘
   │          │              │              │
   ▼          ▼              ▼              ▼
┌────────┐ ┌────────┐ ┌───────────┐ ┌──────────┐
│ Mock   │ │ MQTT   │ │ Modbus    │ │ HTTP     │
│Adapter │ │Adapter │ │ Adapter   │ │ Adapter  │
│(POC)   │ │(IoT)   │ │(Industrial│ │(REST API)│
└────────┘ └────────┘ └───────────┘ └──────────┘
```

**Configuration** — single environment variable:
```bash
SENSOR_MODE=mock     # Current POC
SENSOR_MODE=mqtt     # Production with ESP32 + MQTT broker
SENSOR_MODE=modbus   # Industrial SCADA integration
SENSOR_MODE=http     # REST API from any data source
```

### The Camera Interface (same pattern):
```
CameraInterface (Abstract)
├── MockCamera      ← Current POC (generates synthetic images)
├── RTSPCamera      ← IP cameras / CCTV integration
├── USBCamera       ← Direct USB thermal cameras
└── DroneCamera     ← DJI SDK integration for aerial surveys
```

### Production Transition — ZERO Code Changes

| Step | Action | Cost | Time |
|------|--------|------|------|
| 1 | Buy ESP32 + INA219 sensors | ₹5,000 | 1 day |
| 2 | Connect to MQTT broker | ₹0 (Mosquitto free) | 2 hours |
| 3 | Change `SENSOR_MODE=mqtt` | ₹0 | 5 minutes |
| 4 | Deploy | ₹0 | Automatic |

**Total cost to go from POC → Real hardware: ₹5,000-10,000**

### Hardware Bill of Materials (for 1 panel monitoring station)

| Component | Purpose | Cost (₹) |
|-----------|---------|-----------|
| ESP32 DevKit | IoT microcontroller | ₹500 |
| INA219 (×2) | Voltage + Current sensing | ₹400 |
| DS18B20 | Temperature sensor | ₹150 |
| BH1750 | Light/Irradiance sensor | ₹200 |
| MLX90614 | IR temperature (non-contact) | ₹800 |
| PCB + Wiring | Assembly | ₹500 |
| Waterproof enclosure | IP65 housing | ₹300 |
| **Total per station** | | **₹2,850** |

For full thermal camera capability: FLIR Lepton module (₹15,000-25,000) or Seek Thermal (₹12,000).

---

## 🔶 WHY THIS APPROACH? (1 Minute)

### L&T Techgium Theme: GenAI/LLM Platform

HELIOS AI is designed as a **Generative AI + LLM platform**, not a traditional ML model:

| Traditional ML Approach | HELIOS AI (GenAI/LLM) Approach |
|------------------------|-------------------------------|
| Train custom CNN on 10,000+ labeled fault images | Use pre-trained LLaMA 3.3 70B with expert prompt engineering |
| Requires massive labeled dataset | Zero training data needed — knowledge is in the LLM |
| Black box — "fault detected" | Explainable — "Here's the multi-modal evidence and reasoning chain" |
| Single modality (visual only) | Multi-modal fusion (electrical + EL + thermal + environmental) |
| Fixed fault categories | Open-ended reasoning — discovers novel fault patterns |
| Needs retraining for new fault types | Just update the prompt — the LLM adapts |
| Months of training time | Instant deployment, zero training |

### Cost Advantage

| Item | Traditional Approach | HELIOS AI |
|------|---------------------|-----------|
| EL Camera | ₹5-10 lakhs | ₹0 (Virtual EL via AI) |
| Thermal Camera | ₹2-5 lakhs | ₹800 (MLX90614 IR sensor) |
| Expert Technician | ₹50,000/month | ₹0 (LLM-powered diagnostics) |
| Inspection time/panel | 15-20 minutes | 3 seconds (automated) |
| AI Model Training | ₹5-10 lakhs (GPU + data) | ₹0 (API-based, free tiers) |
| **Total Setup Cost** | **₹15-25 lakhs** | **₹5,000-15,000** |

---

## 📋 TECHNICAL SUMMARY

| Dimension | HELIOS AI |
|-----------|-----------|
| **Frontend** | React 18, Vite 7.3, Zustand, Firebase SDK, Recharts, Framer Motion, Tailwind CSS |
| **Backend** | FastAPI, Python 3.12, async/await, HTTPX |
| **LLM** | LLaMA 3.3 70B via Groq Cloud (free, 7,000 req/day) |
| **Vision AI** | Google Gemini 1.5 Flash (free, 60 req/min) |
| **Image Captioning** | BLIP (Salesforce) via HuggingFace Inference API |
| **Virtual EL** | OpenCV + NumPy + optional Stable Diffusion (Together.ai) |
| **Database** | Firebase Realtime DB (live) + Supabase PostgreSQL (archive) |
| **Deployment** | Vercel (frontend) + Render (backend) |
| **Panels Monitored** | 247 (POC) → designed for 10,000+ |
| **AI Response Time** | 3-8 seconds full analysis pipeline |
| **Standards** | IEC 62446-3 (Thermal), IEC 61215 (Panel Performance) |
| **Lines of Code** | ~5,000+ (Backend) + ~4,000+ (Frontend) |

---

*HELIOS AI — Harnessing Generative AI for Solar Panel Diagnostics*
*L&T Techgium 2026 | Team Submission*
