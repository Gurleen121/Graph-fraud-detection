# Graph-Theoretic Anomaly Detection in Financial Networks
Multi-level graph analytics for detecting coordinated fraud rings using flow, structure, and transaction convergence.
![Python](https://img.shields.io/badge/Python-3.10-blue)
![Status](https://img.shields.io/badge/Status-Research%20Prototype-green)
![License](https://img.shields.io/badge/License-MIT-yellow)

Detecting coordinated financial fraud by analyzing transactions simultaneously at the transaction, pattern, and network levels, instead of relying on a single model. The system flags fraud when multiple analytical perspectives agree, enabling identification of structured fraud rings that appear normal individually.

---

##  Multi-Level Anomaly Localisation

A bank transaction network can be modelled as a graph:
- **Nodes** — bank accounts
- **Edges** — behavioural correlation between accounts (accounts that transact in similar patterns are connected)

The project is divided into three levels

| Level                 | Problem Targeted                                   | Algorithm                          |
|-----------------------|----------------------------------------------------|------------------------------------|
| **Flow level**        | Abnormal movement across the network               | Edmonds-Karp Maximum Flow          |
| **Structural level**  | Detection of known fraud patterns as subgraphs     | Ullmann's Subgraph Isomorphism     |
| **Transaction level** | Identification of suspicious individual events     | Knapsack with Suspicion Scoring    |

Each algorithm is mathematically independent — they share no assumptions, no features, no model parameters. When all three converge on the same node, that convergence is the evidence. 

---

## Features
1. Automated account risk ranking by total spend
2. Maximum flow computation — finds money movement paths
3. Fraud ring detection — finds structural ring patterns
4. Transaction-level suspicion scoring with knapsack optimisation
5. Convergence scoring — ranks accounts by how many algorithms flagged them
6. All outputs saved as graphs for visual analysis
---

## Pipeline

```
Transaction Network
495 accounts | 122,265 correlation edges
        │
        ▼
┌─────────────────────────────────────────────────────┐
│  LEVEL 1 — Flow Analysis (Edmonds-Karp)             │
│  Scans entire network for abnormal money flow       │
│  Finding: AC00455 is flow source                    │
│           Maximum flow: $14,189 to 8 accounts       │
│           Z-score: 4.28 — top 0.2% of 495 accounts │
│           0 accounts exceed it                      │
└─────────────────────────┬───────────────────────────┘
                          │  AC00455 flagged
                          ▼
┌─────────────────────────────────────────────────────┐
│  LEVEL 2 — Structural Pattern (Ullmann's)           │
│  Searches for fraud ring subgraph in network        │
│  Finding: 4368 raw permutations → 1 unique ring     │
│           Ring: AC00455, AC00020, AC00333, AC00358  │
│           AC00455 confirmed structurally embedded   │
└─────────────────────────┬───────────────────────────┘
                          │  AC00455 confirmed in ring
                          ▼
┌─────────────────────────────────────────────────────┐
│  LEVEL 3 — Transaction Isolation (Knapsack)         │
│  Finds most suspicious transactions in flagged acct │
│  Finding: 5 transactions, $989.82                   │
│           Clustered just under $1,000 limit         │
│           Suspicion score: 2.522                    │
│           Classic structuring pattern               │
└─────────────────────────┬───────────────────────────┘
                          │
                          ▼
        ┌─────────────────────────────┐
        │  CONVERGENCE: AC00455       │
        │  Flagged by all 3 levels    │
        │  4 → 1 accounts             │
        │  4x false positive reduction│
        └─────────────────────────────┘
```

---

## Results

| Algorithm       | Lens                    | Key Result                         | Strength     |
|-----------------|-------------------------|------------------------------------|--------------|
| Edmonds-Karp    | Flow behaviour          | Z-score = 4.28                     | STRONG       |
| Ullmann's       | Structural pattern      | 1 unique ring, 4368 permutations   | CONFIRMATORY |
| Knapsack        | Transaction behaviour   | Score 2.522 \| $989.82             | STRONG       |
| **Convergence** | **Pipeline efficiency** | **4 → 1 accounts**                 | **STRONG**   |

**AC00455 is anomalous by three independent measures:**
- **Flow** — Z-score 4.28, extreme outlier across 495 accounts, 0 accounts exceed it
- **Structure** — sole confirmed member of the only fraud ring found in the network
- **Behaviour** — $989.82 clustered just under the $1,000 structuring threshold

---

## Generalisation — Networked Systems Beyond Finance

This pipeline is domain-agnostic. The three levels of anomaly detection apply to any system where entities form a network and anomalies manifest across multiple levels simultaneously.

**Robot Sensor Network example:**

| This Project                   | Robot Arm Sensor Network                         |
|--------------------------------|--------------------------------------------------|
| Account node                   | Joint / sensor node                              |
| Correlation edge               | Physical coupling between joints                 |
| Edmonds-Karp — abnormal flow   | Abnormal torque / current propagation            |
| Ullmann's — fraud ring         | Known mechanical failure subgraph pattern        |
| Knapsack — transaction cluster | Anomalous sensor readings under diagnostic budget|
| Convergence finding            | Confirmed fault localisation                     |

The framework answers the same three questions in any domain: where is energy flowing abnormally, does a known failure subgraph exist, and which specific events are the anomalous ones.

---

## Dataset

`bank_transactions_data.csv` — 2,512 transactions across 495 unique accounts.

| Column              | Description                              |
|---------------------|------------------------------------------|
| AccountID           | Unique account identifier                |
| TransactionAmount   | Transaction value in USD                 |
| TransactionDate     | Timestamp of transaction                 |
| Location            | City where transaction occurred          |
| LoginAttempts       | Number of login attempts before transaction |
| TransactionDuration | Session length in seconds                |
| Channel             | ATM / Online / Branch                    |
| CustomerAge         | Age of account holder                    |
| AccountBalance      | Balance at time of transaction           |

---

## How to Run

**1. Clone the repository**
```bash
git clone https://github.com/GurleenKaurS/Graph-fraud-detection.git
cd Graph-fraud-detection
```

**2. Install dependencies**
```bash
pip install -r requirements.txt
```

**3. Open the notebook**
```bash
jupyter notebook Publish_Code.ipynb
```

**4. Run all cells**

`Kernel → Restart & Run All`

All outputs are deterministic — `np.random.seed(42)` is set throughout.

---

## Project Structure

```
Graph-fraud-detection/
│
├── README.md                      ← you are here
├── Publish_Code.ipynb             ← main notebook
├── bank_transactions_data.csv     ← dataset
├── requirements.txt               ← dependencies
│
└── outputs/
    ├── edmonds_karp_correct_final.png
    ├── ullmann_fraud_ring.png
    ├── knapsack_horizontal_final.png
    └── convergence_funnel.png
```
