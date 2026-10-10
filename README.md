<div align="center">

# 🚨 Incident Command Dashboard

### AI-powered CCTV monitoring that **sees** an emergency, **confirms** it, and **calls for help** — in seconds.

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-backend-009688?logo=fastapi&logoColor=white)
![React](https://img.shields.io/badge/React-Vite-61DAFB?logo=react&logoColor=black)
![Mapbox](https://img.shields.io/badge/Mapbox-live%20map-000000?logo=mapbox&logoColor=white)
![YOLOv8](https://img.shields.io/badge/YOLOv8-detection-FF6F00)
![Telegram](https://img.shields.io/badge/Telegram-alerts-26A5E4?logo=telegram&logoColor=white)

🔥 Fire &nbsp;•&nbsp; 🚗 Crash &nbsp;•&nbsp; 🥊 Violence &nbsp;•&nbsp; 🧍 Fall &nbsp;•&nbsp; 👜 Snatch

</div>

> 📘 Looking for just the run commands (dashboard, phone camera, demo controls, smoke tests)? See [WomenSafety/run.md](WomenSafety/run.md).

---

## ✨ What does it do?

Think of it as a **security guard that never blinks**:

| Step | What happens | Example |
|:---:|---|---|
| 👁️ **1. Watch** | Reads live video from CCTV, IP cameras, webcams or a phone | A camera streams a street |
| 🧠 **2. Detect** | AI models (YOLOv8) look for fire, crashes and more | Flames appear in frame |
| ✅ **3. Confirm** | Needs several matching frames before raising an alarm — fewer false alarms | 4 of 6 frames agree |
| 🗺️ **4. Show** | Incident pops up live on a map with video evidence | Red pin on the dashboard |
| 📲 **5. Alert** | Telegram message + voice-call escalation | Phone rings, bot pings |
| 🚑 **6. Dispatch** | Finds the nearest hospital / police / fire station and plots a route | "Fire station 1.2 km away" |

---

## 🧭 How it works (big picture)

```mermaid
flowchart LR
    A[📹 Camera<br/>CCTV / IP / Phone / Video file] --> B[🎞️ Frame Loop<br/>camera_workers]
    B --> C{🧠 AI Detectors<br/>Fire · Crash · Violence<br/>Fall · Snatch}
    C -- nothing found --> B
    C -- possible event --> D[✅ N-of-M Confirmation<br/>event_capture]
    D -- not confirmed --> B
    D -- confirmed --> E[💾 Save Evidence<br/>thumbnail · best frame · clip]
    E --> F[(🗄️ Incident DB<br/>SQLite)]
    F --> G[⚡ WebSocket Push]
    G --> H[🗺️ Live Map Dashboard<br/>React + Mapbox]
    F --> I[📲 Notifications<br/>Telegram + Voice Call]
    F --> J[🚑 Dispatch Planner<br/>nearest hospital · police · fire]
    J --> H

    style A fill:#1e293b,stroke:#38bdf8,color:#fff
    style C fill:#7c2d12,stroke:#fb923c,color:#fff
    style D fill:#14532d,stroke:#4ade80,color:#fff
    style H fill:#1e3a8a,stroke:#60a5fa,color:#fff
    style I fill:#581c87,stroke:#c084fc,color:#fff
    style J fill:#134e4a,stroke:#2dd4bf,color:#fff
```

### 🔄 Life of one incident

```mermaid
sequenceDiagram
    autonumber
    participant Cam as 📹 Camera
    participant AI as 🧠 Detector
    participant Cap as ✅ Confirmer
    participant API as ⚙️ FastAPI
    participant Map as 🗺️ Dashboard
    participant You as 👮 Operator
    participant Tg as 📲 Telegram / Call

    Cam->>AI: video frames
    AI->>Cap: "looks like fire!" (per frame)
    Cap->>Cap: wait for N of M frames to agree
    Cap->>API: confirmed incident + evidence clip
    API-->>Map: live push (WebSocket)
    API->>Tg: alert message + voice call
    API->>API: find nearest hospital / police / fire
    Map-->>You: red pin + video + dispatch route
    You->>API: Confirm ✅ or False positive ❌
```

### 🚦 Incident status flow

```mermaid
stateDiagram-v2
    [*] --> Detected: AI sees something
    Detected --> Confirmed: N-of-M frames agree
    Detected --> Dropped: not enough evidence
    Confirmed --> Alerted: Telegram / call sent
    Alerted --> Resolved: operator confirms
    Alerted --> FalsePositive: operator rejects
    Resolved --> [*]
    FalsePositive --> [*]
    Dropped --> [*]
```

---

## 🚀 Quick Start (5 commands)

```bash
cd WomenSafety
pip install -r requirements.txt
cp .env.example .env          # then edit .env with your keys
cd frontend && npm install && cd ..
python run_dashboard.py --mode all
```

Then open 👉 **http://localhost:8000** (full stack) or **http://localhost:3000/map** (dev mode).

```mermaid
flowchart LR
    A[1️⃣ Install<br/>Python deps] --> B[2️⃣ Install<br/>frontend deps]
    B --> C[3️⃣ Fill in<br/>.env keys]
    C --> D[4️⃣ Run<br/>run_dashboard.py]
    D --> E[5️⃣ Open<br/>localhost:8000 🎉]
```

---

## 📑 Table of Contents

- [Prerequisites](#-prerequisites)
- [Installation](#-installation)
- [Environment Setup](#️-environment-setup)
- [Camera Configuration](#-camera-configuration)
- [Running the Dashboard](#️-running-the-dashboard)
- [Demo Mode](#-demo-mode)
- [API Endpoints](#-api-endpoints)
- [Phone Camera Setup](#-phone-camera-setup)
- [Project Structure](#-project-structure)
- [Troubleshooting](#-troubleshooting)
- [Safety](#️-safety)

---

## 📦 Prerequisites

| Tool | Version | Purpose |
|------|---------|---------|
| 🐍 Python | 3.10+ | Backend, detection models |
| 🟢 Node.js | 18+ | Frontend build |
| 📦 npm | 9+ | Frontend dependencies |
| 🎬 ffmpeg | any | Evidence clip encoding |
| 🔧 Git | any | Clone + CLIP install |

Check everything is installed:

```bash
python --version
node --version
npm --version
ffmpeg -version
```

---

## 🔧 Installation

```bash
# 1. Clone and enter the project
git clone https://github.com/vjn6805/Detection-Models.git
cd Detection-Models/WomenSafety

# 2. Python dependencies
pip install -r requirements.txt

# 3. Frontend dependencies
cd frontend && npm install && cd ..
```

---

## ⚙️ Environment Setup

```bash
cp .env.example .env
```

### `.env` — key variables

```env
# ============================================================
# DETECTION MODELS — 1 = ON, 0 = OFF
# ============================================================
VIOLENCE_DETECTION=0
FALL_DETECTION=0
SNATCH_DETECTION=0
FIRE_DETECTION=1          # YOLOv8 fire detector
CRASH_DETECTION=1         # YOLOv8 crash detector

API_ENABLED=1

# ── Telegram alerts ──────────────────────────────────────────
# Bot token from @BotFather, chat ID from @userinfobot
TELEGRAM_BOT_TOKEN=your_bot_token_here
TELEGRAM_CHAT_ID=your_chat_id_here

# ── Voice call alerts (Omnidim) ──────────────────────────────
OMNIDIM_API_KEY=
OMNIDIM_AGENT_ID=
OMNIDIM_FROM_NUMBER_ID=
ALERT_PHONE_NUMBER=+91XXXXXXXXXX

# ── Dispatch & nearby services ───────────────────────────────
SERPAPI_KEY=your_serpapi_key     # hospital/police/fire lookup
MAPBOX_TOKEN=your_mapbox_token   # backend routing (server-side)

# ── Demo safety ──────────────────────────────────────────────
DEMO_MODE=true                   # MUST be true for demos
DEMO_PHONE_NUMBER=+91XXXXXXXXXX  # only this number gets calls
ALERTS_ENABLED=false             # true = enable Telegram/call
LEGACY_DETECTORS_ENABLED=false   # keep false for demos
```

### `frontend/.env` — frontend-only

```env
VITE_MAPBOX_TOKEN=pk.your_mapbox_public_token_here
VITE_DEMO_TOKEN=your_demo_token_here
```

> 💡 **Get a Mapbox token:** sign up at [mapbox.com](https://account.mapbox.com/) → create a token → paste it in both `frontend/.env` and `.env`.

### 🔑 Which keys do I really need?

| Feature | Needs | Required? |
|---|---|:---:|
| Detection + dashboard | nothing extra | ✅ |
| Map tiles | `MAPBOX_TOKEN` | Recommended (fallback view exists) |
| Telegram alerts | `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID` | Optional |
| Voice calls | `OMNIDIM_*` keys | Optional |
| Nearest hospital/police/fire | `SERPAPI_KEY` | Optional |

---

## 📷 Camera Configuration

```mermaid
flowchart TD
    S{What camera<br/>do you have?} -->|Laptop webcam| W["CAMERA_SOURCE=0"]
    S -->|CCTV / IP cam| R["CAMERA_SOURCE=rtsp://user:pass@ip:554/stream1"]
    S -->|Phone app| P["CAMERA_SOURCE=http://ip:8080/video"]
    S -->|Recorded clip| V["CAMERA_SOURCE=data/crash.mp4"]
```

Cameras are registered in `config/cameras.json`.

| Source | Example |
|---|---|
| 💻 Webcam | `CAMERA_SOURCE=0` (use `1`, `2`… for more cameras) |
| 📡 RTSP (most CCTV) | `CAMERA_SOURCE=rtsp://username:password@192.168.1.100:554/stream1` |
| 🌐 HTTP / MJPEG | `CAMERA_SOURCE=http://192.168.1.100:8080/video` |
| 🎞️ Video file | `CAMERA_SOURCE=data/crash.mp4` |

### Adding a new camera

Edit `config/cameras.json`:

```json
{
  "camera_id": "CAM-NEW-001",
  "name": "My Camera Name",
  "place_text": "Location description",
  "latitude": 26.9124,
  "longitude": 75.7873,
  "stream_source": "rtsp://192.168.1.100:554/stream1",
  "camera_type": "cctv",
  "location_basis": "real_installation",
  "enabled": true,
  "sample": false
}
```

> **Camera types:** `cctv` (fixed installation) or `phone` (demo phone camera).
> **Location basis:** `real_installation` (actual GPS) or `simulated_placement` (demo position).

---

## ▶️ Running the Dashboard

```mermaid
flowchart TD
    Q{What do you want?} -->|Full demo| A["--mode all<br/>API + detection + UI"]
    Q -->|Only the server| B["--mode api"]
    Q -->|Work on the UI| C["--mode api --no-build<br/>+ npm run dev"]
    Q -->|Only build UI| D["--mode build"]
    Q -->|Only detection| E["--mode detection"]
```

| Mode | Command | Use when |
|---|---|---|
| 🎯 **Full stack** (recommended) | `python run_dashboard.py --mode all` | Demos |
| 🔌 API only | `python run_dashboard.py --mode api` | No camera processing |
| 🛠️ Dev (hot reload) | Terminal 1: `python run_dashboard.py --mode api --no-build`<br/>Terminal 2: `cd frontend && npm run dev` | Frontend work |
| 🏗️ Build frontend | `python run_dashboard.py --mode build` | Production UI |
| 🧠 Detection only | `python run_dashboard.py --mode detection` | Headless detection |

- Dashboard: **http://localhost:8000**
- API docs: **http://localhost:8000/api/docs**
- Dev frontend: **http://localhost:3000** (proxies API to :8000), map at **/map**

---

## 🎮 Demo Mode

### ✅ Pre-demo checklist

| # | Check | Command / Action |
|:-:|-------|-----------------|
| 1 | Mapbox token set | `frontend/.env` has `VITE_MAPBOX_TOKEN=pk.…` |
| 2 | `DEMO_MODE=true` | Check `.env` |
| 3 | Backend running | `python run_dashboard.py --mode all` |
| 4 | Frontend loads | http://localhost:8000 or http://localhost:3000/map |
| 5 | Hotspot joined | Laptop + phone on same Wi-Fi |
| 6 | Phone camera online | `python scripts/check_camera.py CAM-001` |
| 7 | Cache warmed | Visit `/api/v1/cameras/CAM-SAMPLE-001/nearby-services` |

### 🕹️ Hidden demo controls

Press **`Ctrl + Shift + D`** on the map page:

- **Load showcase** — restores `demo/showcase.db` (pre-recorded incidents)
- **Reset view** — refreshes the map data
- **Trigger test incident** — creates a fire or crash incident from a stored sample clip

### Trigger a test incident via API

```bash
# 🔥 Fire on CAM-SAMPLE-002
curl -X POST http://localhost:8000/api/v1/demo/trigger \
  -H "Content-Type: application/json" \
  -d "{\"camera_id\": \"CAM-SAMPLE-002\", \"category\": \"fire\"}"

# 🚗 Crash on CAM-SAMPLE-001
curl -X POST http://localhost:8000/api/v1/demo/trigger \
  -H "Content-Type: application/json" \
  -d "{\"camera_id\": \"CAM-SAMPLE-001\", \"category\": \"road_accident\"}"
```

<details>
<summary>🪟 PowerShell (Windows) version</summary>

```powershell
# Fire incident
Invoke-WebRequest -Uri "http://localhost:8000/api/v1/demo/trigger" `
  -Method POST -ContentType "application/json" `
  -Body '{"camera_id":"CAM-SAMPLE-002","category":"fire"}'

# Crash incident
Invoke-WebRequest -Uri "http://localhost:8000/api/v1/demo/trigger" `
  -Method POST -ContentType "application/json" `
  -Body '{"camera_id":"CAM-SAMPLE-001","category":"road_accident"}'
```

</details>

---

## 🌐 API Endpoints

| Method | Endpoint | Description |
|:---:|----------|-------------|
| GET | `/api/v1/incidents` | List all incidents (paginated) |
| GET | `/api/v1/incidents/{id}` | Incident detail |
| PATCH | `/api/v1/incidents/{id}/status` | Update status (confirm / false_positive) |
| WS | `/api/v1/incidents/ws` | Real-time incident push (WebSocket) |
| GET | `/api/v1/map/groups` | Camera-grouped incidents for the map |
| GET | `/api/v1/cameras/status` | All camera statuses + FPS |
| GET | `/api/v1/cameras/{id}/live` | Live MJPEG stream |
| GET | `/api/v1/cameras/{id}/nearby-services` | Nearest hospital / police / fire |
| GET | `/api/v1/evidence/v2/{cam}/{date}/{id}/{file}` | Evidence files (thumbnail, best_frame, clip) |
| POST | `/api/v1/demo/trigger` | Trigger a test incident |
| GET | `/api/docs` | Interactive Swagger docs |

---

## 📱 Phone Camera Setup

Turn any phone into a live demo camera in 5 steps:

```mermaid
flowchart LR
    A[📲 Install<br/>camera app] --> B[▶️ Start server<br/>note the URL]
    B --> C[📝 Put URL<br/>in .env]
    C --> D[🔍 Run<br/>check_camera.py]
    D --> E[🎉 CAM-001<br/>online]
```

1. **Install an app** — Android: [IP Webcam](https://play.google.com/store/apps/details?id=com.pas.webcam) · iOS: [IP Camera Lite](https://apps.apple.com/app/ip-camera-lite/id1013455241)
2. **Start the stream** and note the URL (e.g. `http://192.168.1.42:8080/video`)
3. **Configure `.env`:**
   ```env
   PHONE_CAM001_URL=http://192.168.1.42:8080/video
   PHONE_CAM001_LAT=26.9124
   PHONE_CAM001_LNG=75.7873
   ```
4. **Verify:** `python scripts/check_camera.py CAM-001` → expect `CAM-001: online`
5. **Add more phones:**
   ```bash
   python scripts/add_phone_camera.py \
     --camera-id CAM-PHONE-002 \
     --name "Entrance Camera" \
     --place "Building entrance" \
     --url "http://192.168.1.43:8080/video" \
     --lat 26.9150 --lng 75.7920
   ```

> ⚠️ Laptop and phone must be on the **same Wi-Fi network** or hotspot.

---

## 🏗️ Architecture

```mermaid
flowchart TB
    subgraph Input["📥 Input"]
        C1[CCTV / RTSP]
        C2[Phone Camera]
        C3[Webcam / Video file]
    end

    subgraph Backend["⚙️ Backend — FastAPI"]
        W[camera_workers<br/>frame loop]
        D[detection_service<br/>YOLOv8 fire / crash]
        E[event_capture<br/>N-of-M + clip encoding]
        N[notification_service<br/>Telegram · call · escalation]
        R[dispatch_routing<br/>route plan + Mapbox cache]
        G[safety_guard<br/>phone-number blocks]
        DB[(incidents.db)]
    end

    subgraph Frontend["🖥️ Frontend — React + Mapbox GL"]
        M[MapView dashboard]
    end

    subgraph External["☁️ External"]
        TG[Telegram]
        OM[Omnidim voice]
        SP[SerpApi]
        MB[Mapbox]
    end

    C1 & C2 & C3 --> W --> D --> E --> DB
    E --> N --> G
    G --> TG & OM
    E --> R --> SP & MB
    DB <--> M
```

More detail: [WomenSafety/docs/ARCHITECTURE.md](WomenSafety/docs/ARCHITECTURE.md)

---

## 📁 Project Structure

```
Detection-Models/
├── WomenSafety/                    # 🚨 Main dashboard app
│   ├── api/                        # FastAPI backend
│   │   ├── core/config.py          # All settings (env-loaded)
│   │   ├── models/                 # camera.py, incident_v2.py
│   │   ├── routes/                 # cameras, map, incidents_v2, evidence, nearby_services
│   │   └── services/               # camera_workers, detection_service, event_capture,
│   │                               # notification_service, dispatch_routing, safety_guard
│   ├── config/cameras.json         # Camera registry
│   ├── frontend/                   # React + Vite + Mapbox GL
│   │   └── src/pages/MapView.jsx   # Main dashboard page
│   ├── fire_detection/             # 🔥 Fire model code
│   ├── crash_detection/            # 🚗 Crash model code
│   ├── fall_detection/             # 🧍 Fall model code
│   ├── snatch_detection/           # 👜 Snatch model code
│   ├── data/                       # Sample videos (crash.mp4, fire.mp4)
│   ├── evidence/                   # Generated evidence files
│   ├── models/                     # YOLOv8 weights
│   ├── scripts/                    # check_camera.py, add_phone_camera.py, camera_replay.py
│   ├── docs/                       # ARCHITECTURE, DEMO_SCRIPT, LIMITATIONS, ...
│   ├── tests/                      # Test suite
│   ├── run_dashboard.py            # 🚀 Main launcher
│   ├── .env.example                # Template for .env
│   └── requirements.txt            # Python dependencies
├── snatch_detection_system/        # 👜 Standalone snatch-detection research pipeline
├── PROJECT_STATE_REPORT.md         # Current project status
└── run.md                          # Run notes
```

---

## 🔧 Troubleshooting

```mermaid
flowchart TD
    P{Problem?} -->|Port 8000 busy| A["netstat -ano | findstr :8000<br/>taskkill /PID &lt;pid&gt; /F"]
    P -->|Map is blank| B[Check VITE_MAPBOX_TOKEN<br/>fallback dots view is normal]
    P -->|Phone cam offline| C[Same Wi-Fi? Open URL in browser<br/>run check_camera.py]
    P -->|Clips fail| D[Install ffmpeg]
    P -->|No incidents| E[Ctrl+Shift+D → Trigger test incident]
```

<details>
<summary>🔌 Port already in use</summary>

```bash
# Windows — find and kill the process
netstat -ano | findstr :8000
taskkill /PID <pid> /F

# Restart
python run_dashboard.py --mode all
```

</details>

<details>
<summary>🗺️ Map shows blank / no tiles</summary>

- Check `frontend/.env` has a valid `VITE_MAPBOX_TOKEN`
- The dashboard has a built-in fallback (dots on dark background) if Mapbox is unreachable

</details>

<details>
<summary>📱 Phone camera not detected</summary>

- Both devices must be on the **same Wi-Fi / hotspot**
- Open the stream URL directly in a browser: `http://<phone-ip>:8080/video`
- Run `python scripts/check_camera.py CAM-001`

</details>

<details>
<summary>🎬 ffmpeg not found</summary>

Evidence clip encoding requires ffmpeg:
- **Windows:** `winget install ffmpeg` or download from [ffmpeg.org](https://ffmpeg.org/download.html)
- **Linux:** `sudo apt install ffmpeg`
- **Mac:** `brew install ffmpeg`

</details>

<details>
<summary>📭 No incidents appear on the map</summary>

1. Check the backend: `curl http://localhost:8000/api/v1/map/groups`
2. Trigger a test: `Ctrl+Shift+D` → "Trigger test incident"
3. Or via API: `POST /api/v1/demo/trigger` with `{"camera_id":"CAM-SAMPLE-001","category":"fire"}`

</details>

<details>
<summary>🧠 Turning detection models on/off</summary>

Edit `.env`:

```env
VIOLENCE_DETECTION=0    # 0 = OFF, 1 = ON
FALL_DETECTION=0
SNATCH_DETECTION=0
FIRE_DETECTION=1        # ✅ ON
CRASH_DETECTION=1       # ✅ ON
```

> **For demos:** keep only `FIRE_DETECTION=1` and `CRASH_DETECTION=1`, and set `LEGACY_DETECTORS_ENABLED=false`.

</details>

---

## 🛡️ Safety

- 🔒 **`DEMO_MODE=true`** (default) restricts all outgoing calls/messages to explicitly configured demo recipients.
- 📵 Discovered service phone numbers (hospitals, police, fire stations) are **display only** — never auto-dialled.
- 🚫 Known emergency numbers (100, 101, 102, 108, 112) are **hard-blocked** in code.
- 📄 See [WomenSafety/docs/LIMITATIONS.md](WomenSafety/docs/LIMITATIONS.md) for full scope disclosure.

---

## 📄 License

Developed for a hackathon demo. See [WomenSafety/docs/LIMITATIONS.md](WomenSafety/docs/LIMITATIONS.md) for scope and limitations.
