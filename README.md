# MarketSentinel

**AI-Powered Market Surveillance & Stock Research Assistant**

MarketSentinel is a real-time financial intelligence platform built to help analysts detect, investigate, and understand unusual stock-market activity.

The platform continuously analyzes market signals such as price movements, trading volume, trade activity, buy/sell pressure, volatility, and cross-stock relationships to identify behavior that differs from normal market patterns.

When an unusual event is detected, MarketSentinel combines statistical signals, machine-learning anomaly detection, cross-stock analysis, market context, and relevant public information to generate an explainable risk alert.

Its integrated **AI Stock Research Assistant** can retrieve permitted company filings, financial results, investor materials, and news, identify relevant information, extract financial facts, compare reporting periods, and generate source-backed research briefs with citations.

## What MarketSentinel Does

```text
Market Data
     ↓
Data Normalization
     ↓
Feature Engineering
     ↓
Anomaly Detection
     ↓
Cross-Stock & Market Context
     ↓
ML Anomaly Detection
     ↓
Risk Scoring
     ↓
🚨 Alert
     ↓
AI Research Assistant
     ↓
Evidence & Citations
     ↓
Analyst Investigation
```

### Core Features

* 📊 **Real-Time Market Surveillance**

  * Monitors price, volume, trade activity, buy/sell pressure, volatility, and market context.

* 🚨 **Anomaly Detection**

  * Detects unusual price movements, volume spikes, trade bursts, and abnormal market behavior.

* 🤖 **ML-Based Detection**

  * Uses machine-learning models such as Isolation Forest to identify behavior that differs from representative normal patterns.

* 🔗 **Cross-Stock Intelligence**

  * Detects synchronized movements between multiple stocks and determines whether an event appears stock-specific, sector-wide, or market-wide.

* 🧠 **AI Stock Research Assistant**

  * Searches relevant filings, financial results, investor materials, and news.
  * Summarizes important developments.
  * Extracts financial metrics and relevant facts.
  * Answers analyst questions using retrieved sources.

* 📚 **RAG-Based Financial Research**

  * Uses embeddings and vector search to retrieve relevant document sections before generating AI responses.

* 📝 **Citation-First Research**

  * Important claims are linked to their underlying document, article, page, section, or retrieved source whenever available.

* 📈 **Risk & Alert Engine**

  * Combines multiple signals into a configurable risk score and generates investigation alerts.

* ⚡ **Real-Time Dashboard**

  * Provides live market updates and alerts through WebSockets.

* 🔎 **Analyst Investigation Workspace**

  * Combines market evidence, anomaly signals, research findings, sources, notes, and case status in one interface.

* 📂 **Case Management**

  * Allows analysts to create cases, add notes, track investigation status, and record resolutions.

## Technology Stack

### Frontend

* Next.js
* React
* TypeScript
* Tailwind CSS
* shadcn/ui
* Apache ECharts / Lightweight Charts

### Backend

* Python
* FastAPI
* WebSockets

### Data & Infrastructure

* PostgreSQL
* TimescaleDB
* Redis
* Kafka / Redpanda
* Docker & Docker Compose

### Machine Learning & AI

* scikit-learn
* Isolation Forest
* sentence-transformers
* pgvector
* Optional FinBERT
* Provider-agnostic LLM API
* Retrieval-Augmented Generation (RAG)

### Document Processing

* PyMuPDF
* pdfplumber
* Unstructured

### Testing

* Pytest
* Playwright

## Responsible AI

MarketSentinel is designed as an **analyst-assistance and investigation platform**, not an automatic trading or investment-advice system.

Anomaly scores indicate unusual behavior that may require investigation; they are not proof of market manipulation.

The research assistant is designed to:

* Cite retrieved sources.
* Distinguish extracted facts from generated interpretation.
* Avoid fabricating financial figures or events.
* Clearly indicate when relevant information cannot be found in configured sources.
* Keep the human analyst responsible for the final investigation and conclusion.

## Project Goal

The goal of MarketSentinel is to reduce the time analysts spend manually monitoring markets and searching through large volumes of financial information.

Instead of:

```text
Watch hundreds of stocks
        ↓
Notice unusual movement
        ↓
Search news manually
        ↓
Read filings
        ↓
Compare financial results
        ↓
Analyze related stocks
        ↓
Create investigation notes
```

MarketSentinel provides:

```text
Detect
  ↓
Contextualize
  ↓
Research
  ↓
Explain
  ↓
Investigate
```

The platform brings market surveillance, machine learning, financial document intelligence, and AI-assisted research together in a single analyst workspace.
