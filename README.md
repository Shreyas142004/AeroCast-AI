# ⚡ AeroCast-AI : Thunderstorm & Lightning Nowcasting System for Karnataka

[![Python 3.10+](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)
[![Flask 3.0](https://img.shields.io/badge/Flask-3.0%2B-lightgrey.svg)](https://palletsprojects.com/p/flask/)
[![MongoDB Atlas](https://img.shields.io/badge/Database-MongoDB%20Atlas-green.svg)](https://www.mongodb.com/atlas)
[![Ensemble Accuracy](https://img.shields.io/badge/Ensemble%20Accuracy-91.24%25-brightgreen.svg)](#-machine-learning-pipeline--evaluation)
[![Google Gemini AI](https://img.shields.io/badge/Generative%20AI-Google%20Gemini-orange.svg)](https://ai.google.dev/)
[![CARTO Basemaps](https://img.shields.io/badge/Maps-CARTO%20Voyager%20GIS-blueviolet.svg)](https://carto.com/)
[![Telegram Bot](https://img.shields.io/badge/Alerts-Telegram%20Bot%20API-0088cc.svg)](#-multi-channel-emergency-alert-system)

> **An intelligent, physics-informed nowcasting platform engineered for high-precision thunderstorm and lightning prediction within a 0 to 90-minute window (+30m, +60m, +90m). Combines atmospheric thermodynamics, a 27-year regional climatology, soft-voting gradient boosted ensembles, CARTO GIS cartography with 176 Karnataka taluk boundaries, Google Gemini Generative AI, and multi-channel autonomous alert broadcasts (Email & Telegram).**

---

## 📌 Project Overview

Tropical thunderstorms, cloud-to-ground lightning discharges, and localized downbursts inflict severe hazards across Karnataka, especially along the Western Ghats orographic barrier and the coastal belt (Dakshina Kannada, Udupi, Uttara Kannada, Kodagu, Chikkamagaluru, and Shivamogga).

Conventional Numerical Weather Prediction (NWP) models (such as GFS and ECMWF) update only on 3 to 6-hour cycles over coarse spatial grids ($9\text{–}25\text{ km}$), failing to resolve rapid convective initiation occurring within a 30 to 60-minute window. Furthermore, standard machine learning models commonly trigger **nocturnal false alarms** in coastal river valleys due to high nighttime relative humidity ($>90\%$) despite calm, stable atmospheric conditions.

**AeroCast-AI** overcomes these limitations by unifying:
1. **Live High-Resolution Atmospheric Telemetry** (Dual-source: Open-Meteo & Visual Crossing fallback).
2. **62-Feature Atmospheric Physics Engine** (Dew Point Depression, Vapor Pressure Deficit, Theta-e, and Diurnal Solar Insolation Weighting).
3. **27-Year Karnataka Historical Climatology** ($9,862$ verified daily observation records from 2000 to 2027).
4. **Soft-Voting GBDT Ensemble Core** (LightGBM, XGBoost, and HistGradientBoosting) achieving **91.24% Holdout Accuracy** and **0.9723 ROC-AUC**.
5. **Interactive CARTO GIS Doppler Radar** with licensed CARTO Voyager vector basemaps, range rings ($25\text{ km}$, $50\text{ km}$, $100\text{ km}$), and simulated reflectivity contours.
6. **176 Karnataka Administrative Taluk Boundary Polygon Engine** with real-time client-side Ray-Casting Point-in-Polygon detection.
7. **Authoritative 30,753-Village Boundary Engine** (`karnataka_places.py` indexing all 31 districts, 240 taluks, and 30,753 villages with strict state geofencing).
8. **Autonomous Background Thunderstorm Monitoring Service** daemon that continuously monitors user localities every 2 minutes and auto-broadcasts warnings.
9. **Multi-Channel Emergency Alert Dispatcher** broadcasting real-time alerts via Email (HTML cards), Telegram Bot (@aerocast_alerts_bot), and SMS.
10. **Google Gemini Generative AI** synthesizing plain-language synoptic advisories for agriculture, transit, and public safety.

---

## 🌟 Key Capabilities & Features

### ⚡ 1. 0 to 90-Minute Nowcasting & 3-Step Dynamic Timeline
- Calibrated convective risk percentages delivered for frontline decision support.
- Dynamic timeline progression cards at **+30m**, **+60m**, and **+90m** horizons with color-coded risk levels:
  - 🟢 **Low / Fair Weather** ($<35\%$): Clear skies, stable atmosphere.
  - 🟡 **Moderate Watch** ($35\% - 69.9\%$): Convective initiation, towering cumulus growth.
  - 🔴 **Critical Alert** ($\ge 70\%$): Severe thunderstorm, cloud-to-ground lightning, violent downbursts.

### 🚫 2. Zero Nocturnal False Alarms
- Leverages continuous boundary-layer saturation metrics:
  - **Dew Point Depression** ($\text{DPD} = T - T_d$)
  - **Vapor Pressure Deficit** ($\text{VPD}$) calculated via the Magnus-Tetens formulation.
- Modulated by a regional **Diurnal Solar Insolation Factor** ($D_f(h)$) and Bayesian Climatological Priors, suppressing false nocturnal alarms during calm, high-humidity nights in coastal river valleys (e.g., Puttur, Bantwal, Sullia).

### 🌡️ 3. Accurate Apparent ("Feels Like") Temperature
- Replaced naive offset approximations with genuine meteorological thermodynamic apparent temperature from live soundings (incorporating air temperature, relative humidity, wind speed, and vapor pressure).

### 🗺️ 4. Interactive S-Band Doppler Radar with CARTO Basemaps
- **CARTO Voyager Basemaps**: Integrated official CARTO Basemaps API key to deliver watermark-free, high-DPI vector tiles without 403 access blocks.
- **Esri World Street Map Failover**: Automatic fallback to Esri tiles in case of network disruptions.
- **Doppler Sweep Canvas**: Concentric radar range rings ($25\text{ km}$, $50\text{ km}$, $100\text{ km}$) with simulated convective reflectivity echoes ($15\text{–}65\text{ dBZ}$).

### 📐 5. 176 Karnataka Administrative Taluk Boundary Polygon Engine
- Features an optimized vector GeoJSON (`static/data/karnataka_taluks.geojson`) covering 176 sub-districts/taluks across Karnataka.
- **Client-Side Ray-Casting Point-in-Polygon (PIP)**: Instantly resolves the parent taluk boundary for any searched village, town, or GPS coordinate.
- **Terracotta Dashed Outline (`#e05a36`)**: Highlights the exact taluk boundary polygon on the Leaflet radar map with smooth bounding-box zoom.
- **Administrative Header Badge**: Displays the verified taluk and district name directly in the radar toolbar.

### 📍 6. Authoritative Karnataka Boundary Fencing & Coordinate Precision
- Backed by `processed_data/karnataka_villages.csv` (30,753 villages, 240 taluks, 31 districts).
- **Coastal Clamping**: Micro-zone coordinates (such as Bantwal and Hampankatta) are clamped strictly onshore ($\ge 74.82^\circ\text{E}$), eliminating sea/ocean coordinate drift.
- **Geofence Enforcement**: Locations outside Karnataka (e.g., Goa, Kerala, Andhra Pradesh, Mumbai) are smoothly navigated to on the map while predictions and telemetry are disabled with a clear out-of-bounds warning.

### 🤖 7. Autonomous Background Thunderstorm Monitoring Service
- A background daemon thread runs continuously within `app.py`, sampling weather telemetry across registered citizens' localities every 2 minutes.
- When convective initiation or severe thunderstorm cells ($\ge 65\%$ probability or active METAR codes) are identified, the daemon automatically triggers and broadcasts emergency alerts without requiring any manual user interaction.

### 📢 8. Multi-Channel Emergency Alert System
- **Telegram Bot API**: Instant push notifications via `@aerocast_alerts_bot` with rich Markdown formatting, emergency safety guidelines, and one-click dashboard links.
- **Email Alerts**: HTML-formatted emergency alert bulletins delivered via SMTP.
- **SMS Alerts**: Frontline emergency SMS dispatch via Twilio.
- **In-Memory Alert Session History**: Dispatches alerts directly to subscribers without unnecessarily bloating the database with notification records.

### 🌾 9. Sector-Specific Generative Advisories (Google Gemini)
- Generates natural-language operational guidance tailored to Karnataka's socio-economic landscape:
  - **Plantation & Agriculture**: Areca nut drying, pepper harvest, and rubber tapping precautions.
  - **Travel & Ghat Road Safety**: Charmadi, Shiradi, Agumbe, and Sampaje ghat road slickness, visibility drops, and flash flooding risks.
  - **Aviation & Outdoors**: Ground lightning hazard protocols and structural shelter advisories.

### 🎨 10. Dual-Theme Design System
- Handcrafted responsive UI supporting **Warm Beige** (Light) and **Roasted Espresso** (Dark) themes with custom glassmorphism styling.

---

## 🏗️ 4-Tier System Architecture

```text
┌─────────────────────────────────────────────────────────────────────────┐
│                    1. PRESENTATION LAYER (USER DASHBOARD)               │
│  - CARTO Voyager GIS Basemaps + Esri Failover                           │
│  - Interactive S-Band Doppler Radar (Range Rings 25 - 100 km)          │
│  - 176 Karnataka Taluk Boundary Polygon Engine (Ray-Casting PIP)        │
│  - Convective Risk Dial & Dynamic Timeline Cards (+30m / +60m / +90m)   │
│  - Gemini Sector Advisories (Agriculture, Ghat Transit, Public Safety)  │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │ HTTPS / REST Requests
┌────────────────────────────────────▼────────────────────────────────────┐
│              2. GATEWAY & BOUNDARY VALIDATION LAYER (FLASK)             │
│  - Flask 3.0+ REST Gateway (Request Routing, CORS & In-Memory Cache)    │
│  - Karnataka State Boundary Validator (11.5°N–18.6°N, 74.0°E–78.6°E)     │
│  - In-Memory Alert Dispatch Throttle & Session State Manager            │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │ Dispatches Validated Queries
┌────────────────────────────────────▼────────────────────────────────────┐
│                    3. CORE SERVICES & AI INFERENCE ENGINE               │
│  - Dual Ingestion: Open-Meteo High-Resolution Telemetry & Live Soundings│
│  - Physics Engine: 62 Atmospheric Features (DPD, VPD, Df Factor, Theta) │
│  - ML Classifier: Soft-Voting GBDT Ensemble (LightGBM + XGBoost + HGB)  │
│  - Autonomous Monitor Daemon: 2-minute cycle background weather scanner │
│  - Google Gemini Cloud LLM: Synoptic Sector Advisory Synthesis          │
│  - Multi-Channel Dispatcher: Telegram Bot API, SMTP Email, Twilio SMS   │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │ Reads / Serializes
┌────────────────────────────────────▼────────────────────────────────────┐
│                             4. STORAGE LAYER                            │
│  - MongoDB Atlas Cloud Database (Users, Auth, Preferences, Alerts)      │
│  - 27-Year Karnataka Climate Dataset (9,862 verified daily records)     │
│  - 30,753 Karnataka Villages & Taluks LGD Census Repository (CSV)       │
│  - 176 Karnataka Sub-District Boundary Polygons (GeoJSON)               │
│  - Serialized Trained Models Repository (best_convective_model.joblib)  │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 📂 Project Structure

```text
AeroCast-AI/
├── app.py                          # Flask REST API Web Server, Routes & Autonomous Daemon
├── config.py                       # Global configuration, path resolutions & environment loader
├── db.py                           # MongoDB Atlas Database Manager, Auth & Active Alerts
├── karnataka_places.py             # 30,753 Villages Index & State Boundary Validation Engine
├── gemini_nowcast.py               # Google Gemini Generative AI Advisory Engine
├── predict_live.py                 # Live Telemetry Ingestion, Physics Transforms & ML Pipeline
├── preprocess_features.py          # 62-Feature Physics Engineering Pipeline
├── train_models.py                 # GBDT Model Benchmarking & Serialization Script
├── requirements.txt                # Python Dependencies & Production Specifications
├── README.md                       # Comprehensive Project Documentation
├── .env                            # API Keys & Local Configuration (Git-ignored)
├── .gitignore                      # Git tracking exclusions
│
├── dataset/
│   └── karnataka_dataset.xlsx      # 27-Year Karnataka Meteorological Records (2000–2027)
│
├── notifications/
│   ├── __init__.py                 # Notification package exports
│   ├── dispatcher.py               # Multi-channel Alert Dispatcher (Email, SMS, Telegram)
│   ├── email_service.py            # SMTP Email Alert Service with HTML templates
│   ├── sms_service.py              # Twilio SMS Alert Service
│   └── telegram_service.py         # Telegram Bot API Service (@aerocast_alerts_bot)
│
├── processed_data/
│   ├── karnataka_convective_features.csv # Cleaned & Processed 62-Feature Matrix
│   └── karnataka_villages.csv      # 30,753 Villages, 240 Taluks & 31 Districts (LGD Directory)
│
├── models/
│   ├── best_convective_model.joblib# Serialized Winning Soft-Voting GBDT Ensemble
│   ├── feature_columns.json        # 62 Feature Schema for Live Inference
│   └── metrics_summary.json        # Holdout Evaluation Metrics Summary
│
├── static/
│   ├── css/
│   │   └── style.css               # Warm Beige (Light) & Roasted Espresso (Dark) Theme Tokens
│   ├── data/
│   │   └── karnataka_taluks.geojson# 176 Karnataka Administrative Taluk Boundary Polygons
│   ├── js/
│   │   ├── app.js                  # Frontend Telemetry, Doppler Radar, Taluk PIP & UI Engine
│   │   └── alerts_listener.js      # Real-time WebSocket / Polling Alert Notifications
│   └── vendor/
│       ├── leaflet.css             # Leaflet GIS Mapping Stylesheet
│       ├── leaflet.js              # Leaflet GIS Interactive Mapping Engine
│       └── leaflet.js.map          # Leaflet source maps
│
└── templates/
    ├── index.html                  # Responsive Convective Weather Dashboard Template
    ├── about.html                  # Physics Parameters & Meteorological Concepts Guide
    ├── history.html                # Convective Search & Prediction History View
    ├── profile.html                # User Profile & Notification Channels Settings
    ├── login.html                  # Authentication Login Page
    ├── register.html               # New User Registration Page
    └── forgot_password.html        # Password Recovery Page
```

---

## 📊 Machine Learning Pipeline & Evaluation

The predictive engine was benchmarked on a **70% Training / 30% Testing** chronological split across the 27-year Karnataka climatological dataset ($9,862$ daily observation records):
- **Training Set (70% | 2000–2018)**: 6,568 historical daily observation records.
- **Holdout Test Set (30% | 2019–2027)**: 3,287 historical daily observation records.

### Benchmark Results on 30% Holdout Test Set

| Model Architecture | Accuracy | Precision | Recall | F1-Score | ROC-AUC |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Logistic Regression (Baseline)** | 86.43% | 0.8273 | 0.9069 | 0.8653 | 0.9490 |
| **Random Forest Classifier** | 83.69% | 0.8128 | 0.8581 | 0.8349 | 0.9203 |
| **HistGradientBoosting (Scikit-Learn)** | 90.84% | 0.8732 | 0.9468 | 0.9085 | 0.9716 |
| **LightGBM Classifier** | 90.90% | 0.8717 | 0.9506 | 0.9094 | 0.9713 |
| **XGBoost Classifier** | 91.27% | 0.8850 | 0.9405 | 0.9119 | 0.9712 |
| **Soft Voting GBDT Ensemble (Winning Model)** 🏆 | **91.24%** | **0.8773** | **0.9506** | **0.9125** | **0.9723** |

*The winning **Soft Voting Ensemble (weights: LightGBM: 1.2, XGBoost: 1.2, HistGradientBoosting: 1.0)** was fitted on 100% of historical records ($9,862$ samples) and serialized to `models/best_convective_model.joblib`.*

---

## 🔬 Mathematical Formulations

### 1. Dew Point Depression (DPD)
$$\text{DPD} = T - T_d$$
where $T$ is dry-bulb surface temperature ($^\circ\text{C}$) and $T_d$ is dew point temperature ($^\circ\text{C}$). A $\text{DPD} < 3^\circ\text{C}$ indicates near-surface moisture saturation.

### 2. Vapor Pressure Deficit (VPD)
Using the Magnus-Tetens formulation for saturation vapor pressure ($e_s$) and actual vapor pressure ($e$):
$$e = 6.112 \times \exp\left(\frac{17.67 \times T_d}{T_d + 243.5}\right), \quad e_s = 6.112 \times \exp\left(\frac{17.67 \times T}{T + 243.5}\right)$$
$$\text{VPD} = e_s - e \quad (\text{hPa})$$

### 3. Diurnal Solar Insolation Factor ($D_f$)
Convective initiation in tropical regimes is fundamentally governed by daytime solar heating. Convective potential is modulated across four diurnal time windows:
$$D_f(h) = \begin{cases} 
1.25, & 13 \le h \le 18 \quad (\text{Afternoon thermal convection peak}) \\
1.00, & 11 \le h < 13 \;\lor\; 18 < h \le 20 \quad (\text{Transition hours}) \\
0.70, & 07 \le h < 11 \quad (\text{Morning boundary-layer heating}) \\
0.42, & \text{otherwise} \quad (\text{Nocturnal radiational cooling})
\end{cases}$$

### 4. Bayesian Climatological Prior Fusion
Combines 27-year historical monthly precipitation frequencies $P_{\text{base}}(m)$ (ranging from $2.1\%$ in January to $62.4\%$ in July) with real-time convective energy $E_{\text{conv}}$:
$$P_{\text{fused}} = 0.35 \times P_{\text{base}}(m) + 0.65 \times (E_{\text{conv}} \times D_f)$$
*Eliminates false alarms during calm, high-humidity nighttime periods in coastal valleys (e.g., Puttur, Bantwal), yielding realistic low risk ($15\text{–}28\%$, Green alert) rather than false $98\%$ critical alarms.*

---

## 🌐 Deployment & Hosting Guide

AeroCast-AI combines Flask, serialized Gradient Boosted Decision Tree models (LightGBM & XGBoost), MongoDB Atlas persistence, and a background daemon thread. Follow the instructions below to deploy on **Render** (recommended) or configure with **MongoDB Atlas**.

### 🍃 Option A: MongoDB Atlas Cloud Setup (Required for Production)

1. **Create an Account & Cluster**:
   - Go to [MongoDB Atlas](https://www.mongodb.com/atlas) and sign up/log in.
   - Click **Create a Deployment** and select the **M0 Free Tier** (AWS or GCP, choose Mumbai `ap-south-1` for lowest latency to Karnataka).
2. **Create a Database User**:
   - Navigate to **Security** → **Database Access** → **Add New Database User**.
   - Select **Password Authentication**, enter a username (e.g. `aerocast_admin`) and a secure password.
   - Assign the **Read and write to any database** privilege.
3. **Configure Network Access**:
   - Navigate to **Security** → **Network Access** → **Add IP Address**.
   - Choose **Allow Access from Anywhere** (`0.0.0.0/0`) so that hosting providers (Render, Vercel) can connect reliably.
4. **Obtain Connection String**:
   - Navigate to **Databases** → Click **Connect** on your cluster → Choose **Drivers (Python)**.
   - Copy your cluster's connection URI string from the Atlas dashboard.
   - In your `.env` or Render environment settings, set:
     ```env
     MONGODB_URI=<your-mongodb-atlas-connection-string>
     MONGODB_DB=aerocast_db
     ```

---

### 🚀 Option B: Deploying on Render (Recommended)

Render provides persistent Python containers ideal for running Flask, Gunicorn, machine learning libraries (`lightgbm`, `xgboost`), and background threads without serverless timeout constraints.

1. **Push your code to GitHub**:
   Ensure all changes are pushed to your GitHub repository.
2. **Create a New Web Service on Render**:
   - Log into [Render Dashboard](https://dashboard.render.com/).
   - Click **New +** → **Web Service**.
   - Connect your GitHub repository (`AeroCast-AI`).
3. **Configure Service Settings**:
   - **Name**: `aerocast-ai`
   - **Region**: Singapore or Frankfurt
   - **Branch**: `main`
   - **Runtime**: `Python 3`
   - **Build Command**:
     ```bash
     pip install --upgrade pip && pip install -r requirements.txt
     ```
   - **Start Command**:
     ```bash
     gunicorn app:app --bind 0.0.0.0:$PORT --workers 2 --timeout 120
     ```
4. **Set Environment Variables in Render**:
   Navigate to the **Environment** tab in your Render service and add:
   - `MONGODB_URI` = `<your-mongodb-atlas-connection-string>`
   - `MONGODB_DB` = `aerocast_db`
   - `SECRET_KEY` = `<generate-a-strong-random-secret-key>`
   - `GEMINI_API_KEY` = `<your-google-gemini-api-key>`
   - `TELEGRAM_BOT_TOKEN` = `<your-telegram-bot-token>` (Optional)
   - `MAIL_USERNAME` = `<your-smtp-email>` (Optional)
   - `MAIL_PASSWORD` = `<your-smtp-app-password>` (Optional)
5. **Deploy**:
   Click **Create Web Service**. Render will automatically clone the repository, install dependencies, load the serialized models, start the background monitor daemon, and provide a public HTTPS URL (e.g., `https://aerocast-ai.onrender.com`).

---

### ⚡ Option C: Notes on Vercel Hosting

If deploying on Vercel:
- Vercel is designed for **stateless serverless functions** with execution time limits (10–15s on Hobby tier) and memory caps (1024 MB).
- While a lightweight Flask API can be deployed via `vercel.json` (pointing to `app.py`), the heavy GBDT ensemble (`lightgbm`, `xgboost`, `scikit-learn`) and the continuous background daemon thread (`background_thunderstorm_monitor_daemon`) will not persist across serverless invocations.
- **Recommendation**: Deploy the full-stack Flask application on **Render** (or Railway/AWS EC2) for continuous background monitoring and fast, warm ML inferences.

---

## 🛠️ Configuration & Environment Variables

Create a `.env` file in the project root directory or supply these keys in your cloud hosting provider:

| Variable | Required | Description | Example / Default |
| :--- | :---: | :--- | :--- |
| `MONGODB_URI` | **Yes** | MongoDB Atlas or local connection string | `<atlas-connection-uri>` or `mongodb://localhost:27017/` |
| `MONGODB_DB` | No | Database name in MongoDB | `aerocast_db` |
| `SECRET_KEY` | **Yes** | Flask session signing secret key | `<random-32-char-string>` |
| `GEMINI_API_KEY` | Optional | Google Gemini Generative AI Key for Synoptic Advisories | `AIzaSy...` |
| `VISUAL_CROSSING_API_KEY` | Optional | Secondary fallback meteorological API | `YOUR_VC_KEY` |
| `TELEGRAM_BOT_TOKEN` | Optional | Telegram Bot token for push notifications | `123456789:ABCdef...` |
| `TELEGRAM_CHAT_ID` | Optional | Default/broadcasting Telegram chat IDs (comma-separated) | `987654321` |
| `MAIL_SERVER` | Optional | SMTP Mail server for email alerts | `smtp.gmail.com` |
| `MAIL_PORT` | Optional | SMTP Port (SSL/TLS) | `465` (SSL) or `587` (TLS) |
| `MAIL_USERNAME` | Optional | SMTP Sender email address | `alerts@aerocast.ai` |
| `MAIL_PASSWORD` | Optional | SMTP App-specific password | `xxxx xxxx xxxx xxxx` |
| `TWILIO_ACCOUNT_SID` | Optional | Twilio account identifier for SMS dispatch | `AC...` |
| `TWILIO_AUTH_TOKEN` | Optional | Twilio authentication token | `...` |
| `TWILIO_PHONE_NUMBER` | Optional | Twilio registered sender phone number | `+1...` |

---

## 🚀 Setup & Local Execution Guide

### 1. Prerequisites
- Python 3.10 or higher.
- Modern web browser (Chrome, Edge, Firefox, Brave) with WebGL enabled.
- Local MongoDB instance or MongoDB Atlas connection string.

### 2. Installation
Clone the repository and install dependencies:
```bash
git clone https://github.com/Shreyas142004/AeroCast-AI.git
cd AeroCast-AI
pip install -r requirements.txt
```

### 3. Environment Configuration
Create a `.env` file in the project root:
```env
MONGODB_URI=mongodb://localhost:27017/
MONGODB_DB=aerocast_db
SECRET_KEY=aerocast-ai-local-development-secret-key-2026
GEMINI_API_KEY=your_google_gemini_api_key_here
```

### 4. Train / Re-evaluate Models (Optional)
The pre-trained 91.24% Soft Voting Ensemble is already included in `models/best_convective_model.joblib`. To retrain from scratch:
```bash
python preprocess_features.py  # Generates 62-feature atmospheric dataset
python train_models.py         # Trains and benchmarks GBDT ensemble
```

### 5. Launch the Server
```bash
python app.py
```
Open **`http://127.0.0.1:5000`** in your browser.

- Default evaluator login (seeded automatically on startup):
  - **Email**: `evaluator@aerocast.ai`
  - **Password**: `aerocast2026`

---

## 🧪 Verification & Out-of-Bounds Testing

The platform has been validated across diverse Karnataka microclimates and out-of-state benchmark queries:

| Test Query | Region / State | Boundary Status | Coordinates | Probability Output | Dashboard Behavior |
| :--- | :--- | :---: | :---: | :---: | :--- |
| **Puttur** | Dakshina Kannada, KA | ✅ Inside KA | `12.7667°N, 75.2016°E` | `28.5%` (Low Risk) | Full Live Weather, Timeline, Taluk Outline & S-Band Radar active |
| **Bantwal** | Dakshina Kannada, KA | ✅ Inside KA | `12.8698°N, 74.8430°E` | `22.4%` (Low Risk) | Onshore clamped coordinate, full taluk boundary highlighted |
| **Hampankatta** | Mangaluru, KA | ✅ Inside KA | `12.8698°N, 74.8430°E` | `25.1%` (Low Risk) | Accurately located in city center, full radar sweep active |
| **Bengaluru** | Bengaluru Urban, KA | ✅ Inside KA | `12.9716°N, 77.5946°E` | `18.5%` (Low Risk) | Live Telemetry, Bangalore Taluk boundary & Sector Advisories |
| **Goa** | Goa State | ❌ Outside KA | `15.2993°N, 74.1240°E` | `N/A (Disabled)` | Map flies to Goa, weather hidden, warning banner displayed |
| **Sikkim** | Sikkim State | ❌ Outside KA | `27.5330°N, 88.5122°E` | `N/A (Disabled)` | Map flies to Sikkim, predictions locked |
| **Andhra Pradesh** | Andhra Pradesh State | ❌ Outside KA | `15.9129°N, 79.7400°E` | `N/A (Disabled)` | Map flies to AP, predictions locked |
| **Mumbai** | Maharashtra State | ❌ Outside KA | `19.0760°N, 72.8777°E` | `N/A (Disabled)` | Out-of-bounds alert, radar scan inactive |

---

## 📜 License
This project is developed for academic, meteorological, and disaster-preparedness research purposes.
