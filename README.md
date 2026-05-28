# SignalRx
Converts plain-English questions into drug safety insights using Claude Haiku and Sonnet agents on 1.7M+ real FDA FAERS records

**A multi-agent pharmacovigilance system that detects drug safety signals from FDA FAERS data using natural language.**

Ask a plain-English question and three Claude AI agents collaborate to generate SQL, query 1.7M+ real adverse event records in Snowflake, and return an executive-ready summary.

---

## Demo

> *"Which drugs have the highest rate of life-threatening outcomes?"*

**Planner Agent** → identifies intent, tables, and metric focus  
**SQL Writer Agent** → generates production-ready Snowflake SQL  
**Narrator Agent** → writes a concise executive summary from the results  

---

## Architecture

```
React Frontend (localhost:3000)
        │
        ▼
FastAPI Backend (localhost:8000)
        │
   ┌────┴────────────────────┐
   │                         │
   ▼                         ▼
Anthropic API           Snowflake
(Claude Haiku +         FAERS_DB.RAW
 Claude Sonnet)         (1.7M+ records)
```

### Agent Pipeline

| Agent | Model | Role |
|---|---|---|
| Planner | Claude Haiku | Interprets business question, identifies tables and metrics |
| SQL Writer | Claude Haiku | Generates Snowflake-optimized SQL query |
| Narrator | Claude Sonnet | Writes executive summary from query results |

---

## Dataset

**FDA Adverse Event Reporting System (FAERS) — 2026 Q1**  
Source: [open.fda.gov](https://open.fda.gov/data/faers/)

| Table | Rows | Description |
|---|---|---|
| DEMOGRAPHICS | 397,224 | Patient age, sex, country, report date |
| DRUGS | 1,703,201 | Drug names, dosage, route, role (suspect/concomitant) |
| REACTIONS | 1,330,675 | MedDRA-coded adverse reaction terms |
| OUTCOMES | 291,580 | Patient outcomes (death, hospitalization, disability, etc.) |

All tables linked via `PRIMARYID`.

---

## Tech Stack

- **Frontend:** React, IBM Plex fonts
- **Backend:** FastAPI, Python
- **AI:** Anthropic API (Claude Haiku + Claude Sonnet)
- **Data Warehouse:** Snowflake
- **Data:** FDA FAERS (open.fda.gov)

---

## Getting Started

### Prerequisites
- Python 3.9+
- Node.js 18+
- Snowflake account (free trial at snowflake.com)
- Anthropic API key (console.anthropic.com)

### 1. Clone the repo
```bash
git clone https://github.com/yourusername/signalrx.git
cd signalrx
```

### 2. Set up the backend
```bash
pip install -r requirements.txt
cp .env.example .env
# Fill in your credentials in .env
uvicorn main:app --reload
```

### 3. Set up the frontend
```bash
cd frontend
npm install
npm run dev
```

### 4. Configure `.env`
```
SNOWFLAKE_ACCOUNT=your-account-identifier
SNOWFLAKE_USER=your_username
SNOWFLAKE_PASSWORD=your_password
SNOWFLAKE_WAREHOUSE=COMPUTE_WH
SNOWFLAKE_DATABASE=FAERS_DB
SNOWFLAKE_SCHEMA=RAW
ANTHROPIC_API_KEY=sk-ant-...
```

### 5. Load FAERS data into Snowflake
Download the ASCII ZIP from [FDA FAERS](https://fis.fda.gov/extensions/FPD-QDE-FAERS/FPD-QDE-FAERS.html), unzip, and follow the SQL setup in [`docs/snowflake_setup.sql`](docs/snowflake_setup.sql).

---

## Sample Queries

- `Which drugs are most frequently reported as the primary suspect?`
- `What are the top 10 adverse reactions reported this quarter?`
- `Which drugs have the most death outcomes reported?`
- `What adverse reactions are most commonly linked to DUPIXENT?`
- `Which drugs have the highest rate of life-threatening outcomes?`
- `Are adverse events more commonly reported in male or female patients?`
- `What are the top 5 drugs reported alongside DUPIXENT as concomitant medications?`

---

## Project Structure

```
signalrx/
├── main.py                  # FastAPI backend — /query and /agent endpoints
├── requirements.txt
├── .env.example
├── frontend/
│   ├── src/
│   │   └── App.jsx          # React frontend — 3-agent UI
│   └── package.json
└── docs/
    └── snowflake_setup.sql  # Table definitions and COPY commands
```

---

## Author

**Kalind Joshi**  
MS Information Systems, Northeastern University  
[LinkedIn](https://linkedin.com/in/kalindjoshi) · [GitHub](https://github.com/kalindj)
