# MarketSentinel — Complete Integration Build (Developer A + Developer B)

MarketSentinel is an automated, real-time financial market surveillance, ML anomaly detection, and AI research intelligence system. This repository contains the complete contract-locked integration build for both **Developer A** and **Developer B**.

---

## 1. Master Ownership Matrix

| Service / Directory | Owner | Responsibilities | Output Contract |
| :--- | :--- | :--- | :--- |
| `services/ingestion/` | **Developer A** | Provider interface (`SimulatorProvider`, `LiveMarketProvider`) | `MarketTick` $\rightarrow$ `market.ticks` |
| `services/features/` | **Developer A** | Rolling-window feature calculation engine | `FeatureSnapshot` $\rightarrow$ `market.features` |
| `services/detectors/` | **Developer A** | 5 Statistical Anomaly Detectors (Price, Volume, Trade, Flow, Book) | `AnomalySignal` $\rightarrow$ `market.anomaly_signals` |
| `services/research_ingest/` | **Developer A** | PyMuPDF parsing, sentence-transformers (`all-MiniLM-L6-v2`), pgvector | `ResearchDocument` $\rightarrow$ `research.documents` |
| `apps/api/a_routes/` | **Developer A** | Market snapshot, history, and document search API | REST endpoints |
| `services/cross_stock/` | **Developer B** | Cross-stock correlation, synchronized move detection | `CROSS_STOCK_SYNC` AnomalySignal |
| `services/ml/` | **Developer B** | scikit-learn Isolation Forest unsupervised outlier detection | `ML_ISOLATION_FOREST` AnomalySignal |
| `services/research_assistant/` | **Developer B** | AI Research Assistant, grounded RAG briefs, analyst Q&A | `ResearchBrief` $\rightarrow$ `research.briefs` |
| `services/risk_alerts/` | **Developer B** | Signal consolidation, deduplication, cooldown, alert scoring | `Alert` $\rightarrow$ `alerts` |
| `apps/api/b_routes/` | **Developer B** | Alerts, AI research briefs, Q&A, and case management APIs | REST endpoints |
| `apps/web/` | **Developer B** | Next.js 15 + React + TypeScript + Apache ECharts + Tailwind UI | Analyst Dashboard (Port 3000) |
| `services/simulation/` | **Shared** | Simulation engine (A owns engine / B owns scenario UI) | `POST /api/simulation/scenario` |
| `packages/schemas/` | **Shared** | Contract-locked Pydantic schemas (`schema_version=1`) | Locked schemas |

---

## 2. Canonical Topics & Event Pipeline

```
[ MarketDataProvider ] (SimulatorProvider | LiveMarketProvider)
          │
          ▼
   [ MarketTick ] (schema_version=1, ISO-8601 UTC ending in Z)
          │
          ▼
   [ market.ticks ]
          │
          ▼
  [ FeatureService ] (Rolling windows in memory / TimescaleDB)
          │
          ▼
 [ FeatureSnapshot ] (13 locked metrics)
          │
          ▼
  [ market.features ]
          │
          ├─────────────────────────┬─────────────────────────┐
          ▼                         ▼                         ▼
 [ 5 Statistical Detectors ] [ Cross-Stock Engine ]      [ ML Isolation Forest ]
 (Price, Volume, Burst, Flow, Book) (Synchronized Moves)    (Unsupervised Outlier)
          │                         │                         │
          └─────────────────────────┼─────────────────────────┘
                                    │
                                    ▼
                         [ AnomalySignal ] (0.0 - 1.0 scores)
                                    │
                                    ▼
                        [ market.anomaly_signals ]
                                    │
                                    ▼
                        [ Risk / Alert Engine ]
                        (Weighted scoring, deduplication, cooldown)
                                    │
                                    ▼
                            [ Alert (0-100) ]
                                    │
                        ┌───────────┴───────────┐
                        ▼                       ▼
            [ alerts (Redpanda) ]        [ AI Research Assistant ]
                        │               (Retrieves filings, generates brief)
                        ▼                       │
            [ WebSocket: /ws/alerts ]           ▼
                        │               [ ResearchBrief (Citations) ]
                        ▼                       │
            [ Next.js Analyst UI ] ◄────────────┘
```

---

## 3. The Signal & Risk Scoring Weights (Developer B)

The Risk/Alert Engine calculates the composite alert score using locked weights:
- **Price (Z-Score & CUSUM)**: `0.20`
- **Volume (Z-Score & Relative Volume)**: `0.20`
- **Trade Activity (Trade Rate & Burst)**: `0.15`
- **Order Flow (Imbalance & Book Anomaly)**: `0.15`
- **Cross-Stock Correlation (Synchronized Moves)**: `0.10`
- **Research / News Context**: `0.10`
- **ML Isolation Forest**: `0.10`

$$BaseScore = \sum (weight \times signal\_0\_1) \times 100$$
- **Concurrence Boost**: $+10$ when 3 or more distinct detector categories trigger simultaneously.
- **Alert Severity**:
  - `0–30`: **NORMAL**
  - `31–50`: **WATCH**
  - `51–70`: **SUSPICIOUS**
  - `71–85`: **HIGH**
  - `86–100`: **CRITICAL**

---

## 4. API Endpoints Reference

| Method | Endpoint | Owner | Description |
| :--- | :--- | :--- | :--- |
| `GET` | `/health` | **Shared** | Health status of Developer A & B services |
| `GET` | `/api/stocks/{symbol}/snapshot` | **Dev A** | Latest 13-metric FeatureSnapshot |
| `GET` | `/api/stocks/{symbol}/history` | **Dev A** | Historical ticks and prices |
| `GET` | `/api/research/documents` | **Dev A** | List indexed regulatory filings |
| `GET` | `/api/research/search` | **Dev A** | Vector similarity search returning citation metadata |
| `POST` | `/api/simulation/scenario` | **Shared** | Trigger simulator scenarios (A engine / B UI) |
| `WS` | `/ws/market` | **Shared** | Real-time market tick and feature stream |
| `GET` | `/api/alerts` | **Dev B** | List consolidated alerts (filter by symbol, severity, status) |
| `GET` | `/api/alerts/{alert_id}` | **Dev B** | Get alert details and contributing signals |
| `POST` | `/api/alerts/{alert_id}/status` | **Dev B** | Update alert status (`OPEN`, `INVESTIGATING`, `RESOLVED`, `CLOSED`) |
| `POST` | `/api/research/brief` | **Dev B** | Generate grounded AI ResearchBrief with page citations |
| `POST` | `/api/research/ask` | **Dev B** | Grounded analyst Q&A on verified disclosures |
| `POST` | `/api/cases` | **Dev B** | Create investigation case linked to alert |
| `GET` | `/api/cases/{case_id}` | **Dev B** | Retrieve case with full audit trail and notes |
| `POST` | `/api/cases/{case_id}/notes` | **Dev B** | Add analyst note to case |
| `WS` | `/ws/alerts` | **Dev B** | Real-time WebSocket alert broadcasting |

---

## 5. Quickstart & Verification

### Run with Docker Compose
```bash
# Clean state startup
docker compose down -v
docker compose up --build -d

# Verify all services
curl http://localhost:8000/health
```

### Run Full Test Suite (42 Tests: Unit, Contract, and E2E)
```bash
python -m pytest -v
```

### Run Demonstration Scripts
```bash
# Developer A Demonstration (Simulator -> Ticks -> Features -> 5 Detectors -> Ingestion)
python scripts/demonstrate_pipeline.py

# Developer B Demonstration (Cross-stock -> ML -> Risk Engine -> AI Research Brief -> Cases)
python scripts/demonstrate_developer_b_pipeline.py

# Live Docker Verification
python scripts/verify_docker_scenarios.py
```
