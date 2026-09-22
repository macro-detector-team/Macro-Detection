# Ticket Booking Bot Detection Project

A comprehensive project featuring a ticket booking web service combined with an anomaly-detection-based macro detection system.  
It collects browser behavior logs, computes risk scores using an active model (One-Class SVM), and integrates them into an `allow / challenge / block` policy pipeline.

## 1. Key Features
- **Booking Flow:** Event selection -> Queue -> Seat selection -> Payment -> Completion
- **Behavior Log Collection:** Collects clicks, mouse trajectories, hovers, and metadata, then sends them to `/api/logs`
- **Risk Assessment:** Combines rule-based scores with machine learning model scores
- **Operations & Management:** Admin event management, user restriction/unrestriction, report viewing, and user-profile booking history
- **Analysis Reports:** Generates reports for block/challenge events (with optional LLM-powered summaries)

## 2. Tech Stack
- **Backend:** `FastAPI`, `Uvicorn`, `Pydantic`
- **Frontend:** `HTML/CSS/Vanilla JS`
- **ML:** `scikit-learn`, `numpy`, `joblib`, `shap`, `torch(optional)`
- **Storage:** File-based JSON (`data/`, `model/data/raw/`, `model/*`)
- **Automation:** `Node.js + Puppeteer` (Simulation scripts)

## 3. Project Structure
```text
.
├─ main.py                         # FastAPI server + middleware + risk runtime
├─ html/*.html, css/, js/          # Frontend pages/styles/scripts
├─ data/                           # Operational data (JSON) for users/sanctions, etc.
├─ model/                          # Serving code, reports, active artifacts
├─ hybrid_model/                   # Training/evaluation pipelines and benchmark results
├─ automation/                     # Puppeteer-based macro/human simulator
├─ macro/                          # Code related to seat-selection assistant macro (F2)
└─ docs (*.md)                     # Model/architecture/API documentation
```

## 4. Quick Start
### 4.1 Prerequisites
- Python `3.11+` recommended
- Includes setup scripts for Windows environments (`server.bat`)

### 4.2 Installation
```bash
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

### 4.3 Running the Server
```bash
python main.py
```
Or:
```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

### 4.4 Accessing the Service
- Main Service: `http://localhost:8000`
- Admin Viewer: `http://localhost:8000/viewer.html`

Default Admin Accounts (for development purposes):
- `admin@ticket.com / admin1234`
- `manager@ticket.com / manager1234`

## 5. Active Model (Operational Standards)
- **Active Model:** `One-Class SVM`
- **Active Parameters:** `model/artifacts/active/human_model_params.json`
- **Active Artifact:** `model/artifacts/active/human_model_oneclass_svm.joblib`
- **Operational Decision Thresholds:**
  - `model_score < 0.50`: `allow`
  - `0.50 <= model_score < 0.60`: `challenge`
  - `model_score >= 0.60`: `block`

For more details, refer to `model_definition.md`.

## 6. Key APIs
Refer to `API_SPEC_v1.md` for the full list.

- **Auth:** `/api/auth/signup`, `/api/auth/login`
- **Queue:** `/api/booking/start-token`, `/api/queue/join`, `/api/queue/status`, `/api/queue/enter`
- **Logs:** `/api/logs` (POST/GET), `/api/logs/{filename}`
- **Risk Status:** `/api/risk/runtime-status`
- **Admin:** `/api/admin/*` (Event/sanction/cancellation management)
- **MyPage:** `/api/mypage/bookings/{email}`, `/api/mypage/update-delivery`
- **Reports:** `/api/reports`, `/api/reports/{filename}`
- **Macro:** `/api/macro/f2`, `/api/macro/f2/status`

## 7. Environment Variables
Configure via a `.env` file or system environment variables.

| Variable | Default Value | Description |
|---|---|---|
| `RISK_ALLOW_THRESHOLD` | `0.50` | Override for allow/challenge boundary |
| `RISK_CHALLENGE_THRESHOLD` | `0.60` | Override for challenge/block boundary |
| `RISK_DECISION_MODE` | `risk_weighted` | `risk_weighted` or `model_threshold_fixed` |
| `RISK_MODEL_FIXED_THRESHOLD` | None | Directly specify model threshold in fixed mode |
| `RISK_MODEL_SCORE_SCALING` | `auto` | `auto`, `minmax`, `logistic_p95` |
| `RISK_BLOCK_AUTOMATION` | `false` | Force real-time blocking enforcement |
| `LLM_REPORT_ENABLED` | `true` | Enable LLM report generation |
| `OPENAI_API_KEY` | None | Required for LLM report generation |
| `QUEUE_REQUIRE_START_TOKEN` | `true` | Enforce start token requirement when entering the queue |
| `SEAT_F2_MACRO_ENABLED` | `true` | Enable seat F2 helper macro API |

## 8. Related Documentation
- `model_definition.md`: Active model definition (preprocessing, features, training, validation, inference)
- `active_model.md`: Active model preprocessing/feature summary
- `architecture.md`: System architecture
- `API_SPEC_v1.md`: API specifications
- `DB.md`: Data storage structure

## 9. Notes
- This project utilizes a file-based data storage structure.
- Some authentication and credential handling logics are implemented for development convenience and will require security enhancements (hashing, permission controls, secret management) for production environments.