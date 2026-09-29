<div align="center">

# 🛡️ UPI Shield

### Real-time payments, protected in milliseconds, explained in plain language.

An explainable AI fraud layer that sits between payment initiation and settlement, so risky transactions are stopped **before** money moves, and every decision comes with a reason.

![Status](https://img.shields.io/badge/status-proposal%20%2B%20prototype%20in%20progress-blue)
![Challenge](https://img.shields.io/badge/Drunix%20Hackathon-Citi%20CHL--7007-red)
![Focus](https://img.shields.io/badge/focus-Real--Time%20Payments-success)
![Python](https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?logo=fastapi&logoColor=white)
![React](https://img.shields.io/badge/React-61DAFB?logo=react&logoColor=black)
![XGBoost](https://img.shields.io/badge/XGBoost-ML-orange)
![SHAP](https://img.shields.io/badge/SHAP-Explainable%20AI-purple)
![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)

**Drunix Hackathon in collaboration with Citi · Problem Statement 1: Real-Time Payments**

</div>

---

## 📑 Table of Contents

1. [The Problem](#-the-problem)
2. [Our Solution](#-our-solution)
3. [How It Works](#-how-it-works)
4. [System Architecture](#-system-architecture)
5. [Risk Signals](#-risk-signals-what-the-model-looks-at)
6. [The ML Engine](#-the-ml-engine)
7. [Explainability](#-explainability-every-decision-has-a-reason)
8. [API Design](#-api-design)
9. [Data Model](#-data-model)
10. [Bank Compliance Dashboard](#-bank-compliance-dashboard)
11. [Security, Privacy & Governance](#-security-privacy--governance)
12. [Deployment](#-deployment)
13. [Evaluation Plan](#-evaluation-plan)
14. [Why UPI Shield Is Different](#-why-upi-shield-is-different)
15. [Business & Social Impact](#-business--social-impact)
16. [Roadmap](#-roadmap)
17. [Repository Structure](#-repository-structure)
18. [Getting Started](#-getting-started)

---

## 🚨 The Problem

India's real-time payment rails settle in **seconds**. Fraudsters exploit that same speed.

| Pain point | What happens today |
|---|---|
| **Detection is late** | Fraud is often identified only after funds have already moved, making recovery slow and uncertain. |
| **Rules are rigid** | Static rule-based systems miss new fraud patterns and block genuine customers by mistake. |
| **Decisions are opaque** | A blocked user gets no explanation, so trust drops and support tickets rise. |
| **Compliance is manual** | Analysts review flagged transactions without clear reasoning, which slows every case. |

> **The gap:** payments are instant, but protection and explanation are not.

---

## 💡 Our Solution

**UPI Shield** is a real-time risk engine exposed as a single API. For every payment it:

1. ⚡ **Scores** the transaction for fraud risk in milliseconds
2. 🚦 **Decides** to **Approve**, **Challenge** or **Block**
3. 🗣️ **Explains** the decision in plain language
4. 📊 **Logs** everything for compliance review

```mermaid
flowchart LR
    A["💳 Payment<br/>Initiated"] --> B["🛡️ UPI Shield<br/>Risk Engine"]
    B --> C{"Risk<br/>Level?"}
    C -->|Low| D["✅ APPROVE<br/>Instant settlement"]
    C -->|Medium| E["⚠️ CHALLENGE<br/>Step-up verification"]
    C -->|High| F["⛔ BLOCK<br/>Reason shown to user"]
    D --> G[("📁 Audit Log")]
    E --> G
    F --> G
    G --> H["📊 Compliance<br/>Dashboard"]

    style B fill:#4f46e5,color:#fff,stroke:#312e81
    style D fill:#16a34a,color:#fff
    style E fill:#f59e0b,color:#fff
    style F fill:#dc2626,color:#fff
```

**Genuine users feel nothing. Fraudsters get stopped. Everyone gets a reason.**

---

## ⚙️ How It Works

```mermaid
sequenceDiagram
    autonumber
    participant U as 📱 Payment App
    participant G as 🚪 UPI Shield API
    participant F as 🧮 Feature Engine
    participant M as 🤖 ML Risk Model
    participant X as 🔍 SHAP Explainer
    participant D as 🗄️ Database
    participant B as 🏦 Bank / PSP

    U->>G: POST /score (payment details)
    G->>F: Build behavioral features
    F->>D: Fetch user & payee history
    D-->>F: Historical patterns
    F->>M: Feature vector
    M->>X: Risk score
    X-->>G: Score + top reasons
    G->>D: Store decision (audit log)

    alt Low risk
        G-->>U: APPROVE
        U->>B: Continue to settlement
    else Medium risk
        G-->>U: CHALLENGE (step-up check)
    else High risk
        G-->>U: BLOCK + plain-language reason
    end
```

---

## 🏗️ System Architecture

```mermaid
flowchart TB
    subgraph Client["📱 Client Layer"]
        A1["Payment / UPI App"]
        A2["Bank Compliance Dashboard<br/>(React)"]
    end

    subgraph Service["🚪 Service Layer (FastAPI)"]
        B1["POST /score"]
        B2["GET /decisions"]
        B3["GET /explain/:id"]
        B4["Auth and Rate Limiting"]
    end

    subgraph Intelligence["🧠 Intelligence Layer"]
        C1["Feature Engineering<br/>velocity · amount · device · payee · time"]
        C2["Anomaly Detection<br/>Isolation Forest"]
        C3["Supervised Classifier<br/>XGBoost"]
        C4["Score Fusion +<br/>Threshold Policy"]
        C5["SHAP Explainer"]
    end

    subgraph Data["🗄️ Data Layer"]
        D1[("PostgreSQL<br/>Transactions · Profiles · Decisions")]
        D2[("Model Registry<br/>Versioned artifacts")]
    end

    subgraph Rails["🔗 Payment Rails"]
        E1["NPCI / Drunix Sandbox APIs<br/>(simulated flow until access is granted)"]
    end

    A1 --> B4 --> B1
    A2 --> B2
    A2 --> B3
    B1 --> C1
    C1 --> C2
    C1 --> C3
    C2 --> C4
    C3 --> C4
    C4 --> C5
    C5 --> B1
    C1 <--> D1
    B1 --> D1
    C3 -.loads.-> D2
    B1 --> E1

    style Intelligence fill:#eef2ff,stroke:#4f46e5
    style Service fill:#ecfeff,stroke:#0891b2
    style Data fill:#f0fdf4,stroke:#16a34a
```

---

## 🔎 Risk Signals: What the Model Looks At

| Category | Example signals | Fraud pattern it catches |
|---|---|---|
| **Amount** | Ratio to user's usual amount, round-number amounts, just-below-limit amounts | Account takeover, structuring |
| **Velocity** | Payments in last 1 min / 1 hr / 24 hr, burst frequency | Rapid drain of accounts |
| **Payee** | New payee, payee seen across many senders, payee age | Mule accounts, scam collection accounts |
| **Device** | New or changed device, multiple accounts on one device | SIM swap, device farms |
| **Time** | Unusual hour for this user, sudden change in usage rhythm | Off-hours takeover attempts |
| **Behavior** | Deviation from the user's own historical profile | Anything that "doesn't look like them" |
| **Sequence** | Small test payment followed by a large payment | Probing before the real attack |

---

## 🤖 The ML Engine

UPI Shield combines two complementary models so it handles both **known** and **unknown** fraud.

```mermaid
flowchart LR
    X["Feature<br/>Vector"] --> A["🌲 Isolation Forest<br/>Unsupervised<br/><i>Catches new, unseen patterns</i>"]
    X --> B["⚡ XGBoost<br/>Supervised<br/><i>Catches known fraud patterns</i>"]
    A --> C["⚖️ Score Fusion"]
    B --> C
    C --> D["🎯 Risk Score<br/>0 – 100"]
    D --> E["🚦 Threshold Policy"]

    style C fill:#4f46e5,color:#fff
    style D fill:#f59e0b,color:#fff
```

| Model | Role | Why it is here |
|---|---|---|
| **Isolation Forest** | Unsupervised anomaly detection | Fraud evolves. This flags behavior that is unusual even without labeled examples. |
| **XGBoost** | Supervised classification | Strong performance on tabular payment data; learns known fraud signatures. |
| **Score fusion** | Weighted combination | Balances novelty detection with known-pattern accuracy. |

### Threshold policy (configurable defaults)

| Risk score | Decision | User experience |
|---|---|---|
| **0 – 39** | ✅ Approve | Payment goes through instantly |
| **40 – 74** | ⚠️ Challenge | Step-up verification (PIN / OTP / confirmation) |
| **75 – 100** | ⛔ Block | Payment held; clear reason shown; analyst notified |

Banks can tune these thresholds to match their own risk appetite.

---

## 🗣️ Explainability: Every Decision Has a Reason

Black-box blocking destroys trust. UPI Shield uses **SHAP** to turn model output into human-readable reasons.

```mermaid
flowchart LR
    A["Risk Score: 87"] --> B["SHAP<br/>Feature Attribution"]
    B --> C["Reason 1: Amount is far above<br/>this user's normal payments"]
    B --> D["Reason 2: Payee is new and<br/>received payments from many senders today"]
    B --> E["Reason 3: Payment made from<br/>a new device"]
    C --> F["📝 Plain-language message<br/>for the user and the analyst"]
    D --> F
    E --> F
```

**Example user-facing message:**

> *"We paused this payment because the amount is much higher than your usual transfers and the receiver is new to you. Please confirm it's really you."*

**Example analyst view:** ranked contributing factors with their impact on the score, so review takes seconds instead of minutes.

---

## 🔌 API Design

### `POST /score`

**Request** *(illustrative)*

```json
{
  "transaction_id": "txn_001",
  "sender_id": "user_123",
  "payee_id": "payee_456",
  "amount": 48000,
  "currency": "INR",
  "device_id": "dev_789",
  "timestamp": "2026-10-05T02:14:00+05:30"
}
```

**Response** *(illustrative)*

```json
{
  "transaction_id": "txn_001",
  "risk_score": 87,
  "decision": "BLOCK",
  "reasons": [
    "Amount is far above this user's normal payments",
    "Payee is new and received payments from many senders today",
    "Payment initiated from a new device"
  ],
  "model_version": "v0.1.0"
}
```

### Other endpoints

| Endpoint | Purpose |
|---|---|
| `GET /decisions` | List recent decisions with filters (for the dashboard) |
| `GET /explain/{transaction_id}` | Detailed feature attribution for one transaction |
| `POST /feedback` | Analyst marks a decision as correct or incorrect (feeds retraining) |
| `GET /health` | Service health check |

---

## 🗃️ Data Model

```mermaid
erDiagram
    USER ||--o{ TRANSACTION : initiates
    PAYEE ||--o{ TRANSACTION : receives
    DEVICE ||--o{ TRANSACTION : used_in
    TRANSACTION ||--|| DECISION : produces
    DECISION ||--o{ REASON : explained_by
    DECISION ||--o| FEEDBACK : reviewed_by

    USER {
        string user_id PK
        float avg_amount
        int typical_hour
        datetime created_at
    }
    PAYEE {
        string payee_id PK
        int unique_senders_24h
        datetime first_seen
    }
    DEVICE {
        string device_id PK
        int linked_accounts
    }
    TRANSACTION {
        string txn_id PK
        float amount
        datetime timestamp
    }
    DECISION {
        string decision_id PK
        int risk_score
        string action
        string model_version
    }
    REASON {
        string reason_id PK
        string text
        float impact
    }
    FEEDBACK {
        string feedback_id PK
        string label
        string analyst_id
    }
```

---

## 📊 Bank Compliance Dashboard

A React dashboard for risk and compliance teams:

| Panel | What it shows |
|---|---|
| **Live feed** | Incoming transactions with risk score and decision |
| **Flagged queue** | Challenged and blocked payments awaiting review |
| **Case view** | Full explanation with ranked reasons for each decision |
| **Trends** | Fraud patterns over time, top risky payees and devices |
| **Feedback loop** | One-click "correct / false alarm" to improve the model |

```mermaid
flowchart LR
    A["🚨 Flagged<br/>Transaction"] --> B["🔍 Analyst opens<br/>case view"]
    B --> C["📖 Reads plain-language<br/>reasons"]
    C --> D{"Verdict"}
    D -->|Genuine fraud| E["Confirm block<br/>label as fraud"]
    D -->|False alarm| F["Release payment<br/>label as genuine"]
    E --> G[("🔁 Feedback Store")]
    F --> G
    G --> H["🤖 Periodic model<br/>retraining"]
```

---

## 🔐 Security, Privacy & Governance

| Area | Approach |
|---|---|
| **Data minimization** | Only the fields needed for scoring are used; identifiers are hashed or tokenized in the data layer. |
| **Auditability** | Every decision is stored with score, reasons and model version for full traceability. |
| **Access control** | API authentication and role-based access on the dashboard. |
| **Fairness** | Decisions rely on behavioral signals, and sensitive personal attributes are excluded from the model. |
| **Human in the loop** | Borderline cases go to step-up verification or analyst review instead of automatic denial. |
| **Model governance** | Versioned models, so any decision can be traced to the exact model that made it. |

---

## 🐳 Deployment

```mermaid
flowchart LR
    subgraph Docker["🐳 Docker Compose"]
        A["api<br/>FastAPI + Model"]
        B["frontend<br/>React Dashboard"]
        C[("db<br/>PostgreSQL")]
    end
    U["👤 Bank / PSP"] --> A
    Analyst["🧑‍💼 Analyst"] --> B
    B --> A
    A --> C
```

One command brings up the full stack. The scoring service is stateless and can be scaled horizontally behind a load balancer.

---

## 📏 Evaluation Plan

We will measure the system honestly on held-out data and report the results from our own runs.

| Metric | Why it matters |
|---|---|
| **Precision** | Of the payments we flag, how many are truly fraud? |
| **Recall** | Of all fraud, how much do we catch? |
| **F1 and PR-AUC** | Balanced view on heavily imbalanced fraud data |
| **False positive rate** | Genuine users wrongly blocked (direct cost to trust) |
| **Latency (p95)** | Must fit within a real-time payment flow |
| **Explanation quality** | Are the reasons understandable to users and analysts? |

**Design goals:** low-latency scoring suitable for real-time flows, and a false-positive rate low enough that genuine users rarely notice the system.

---

## 🆚 Why UPI Shield Is Different

| | Traditional rule engines | Typical black-box ML | **UPI Shield** |
|---|---|---|---|
| Catches new fraud patterns | ❌ | ✅ | ✅ |
| Catches known fraud patterns | ✅ | ✅ | ✅ |
| Explains every decision | ⚠️ Rule ID only | ❌ | ✅ Plain language |
| Adapts to each user's behavior | ❌ | ⚠️ | ✅ |
| Analyst feedback loop | ❌ | ⚠️ | ✅ |
| Plugs in through one API | ⚠️ | ⚠️ | ✅ |
| Real-time, before settlement | ✅ | ⚠️ | ✅ |

---

## 🌍 Business & Social Impact

| Stakeholder | Impact |
|---|---|
| **Banks and PSPs** | Lower fraud losses, fewer false declines, faster and more consistent compliance review |
| **Users** | Safer payments, clear reasons when something is held, fewer support headaches |
| **Regulators and ecosystem** | Auditable, explainable decisions that support trust in India's digital payments growth |
| **Citi-aligned focus** | Strengthens risk management and digital banking experience |

### Future scope

- 🕸️ **Graph analytics** to detect mule-account networks
- 🌐 **Cross-border payments** extension for remittance fraud
- 🗣️ **Vernacular and voice alerts** to support financial inclusion
- 🔒 **Federated learning** so banks can improve models without sharing raw data
- 📱 **On-device risk signals** for faster, privacy-preserving scoring

---

## 🗺️ Roadmap

```mermaid
gantt
    title UPI Shield: Indicative Build Timeline
    dateFormat  YYYY-MM-DD
    axisFormat  %d %b

    section Data & Model
    Data prep and feature engineering      :a1, 2026-10-01, 5d
    Train and evaluate models              :a2, after a1, 4d

    section Backend
    FastAPI /score with SHAP reasons       :b1, after a2, 5d
    Simulated UPI payment flow             :b2, after b1, 3d

    section Frontend
    Payment demo screen                    :c1, after b1, 4d
    Compliance dashboard                   :c2, after c1, 4d

    section Finish
    Threshold tuning and testing           :d1, after c2, 3d
    Docs and demo video                    :d2, after d1, 2d
```

| Phase | Deliverable |
|---|---|
| **1. Foundation** | Dataset preparation, feature engineering, baseline models |
| **2. Core engine** | Working `/score` endpoint with explainable reasons |
| **3. Experience** | Payment demo and compliance dashboard |
| **4. Polish** | Tuning, testing, documentation, demo video |

---

## 📁 Repository Structure

```
upi-shield/
├── README.md
├── data/               # sample or synthetic datasets
├── model/              # training notebooks and saved models
├── api/                # FastAPI scoring service
│   ├── main.py
│   ├── features.py
│   ├── scoring.py
│   └── explain.py
├── frontend/           # React demo and dashboard
├── docs/               # architecture diagrams and notes
└── docker-compose.yml
```

---

## 🚀 Getting Started

```bash
# 1. Clone
git clone https://github.com/parthkharat20/upi-shield.git
cd upi-shield

# 2. Run everything with Docker
docker compose up --build

# 3. Or run pieces individually
# API
cd api && pip install -r requirements.txt
uvicorn main:app --reload

# Frontend
cd ../frontend && npm install && npm start
```

Try the API:

```bash
curl -X POST http://localhost:8000/score \
  -H "Content-Type: application/json" \
  -d '{"transaction_id":"txn_001","sender_id":"user_123","payee_id":"payee_456","amount":48000,"currency":"INR","device_id":"dev_789","timestamp":"2026-10-05T02:14:00+05:30"}'
```

> The commands above become runnable as the prototype code lands in this repository, following the roadmap.

---

<div align="center">

### 🛡️ Fast payments deserve fast protection.

**UPI Shield** · Built for the Drunix Hackathon in collaboration with Citi

</div>
