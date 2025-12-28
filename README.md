[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](#getting-started)

# Bank Statement Processor (Python)
A production-style pipeline for **bank statement normalization** and **transparent rule-based screening** of transactions.
It converts raw statements from different banks into a single, clean **Transactions** dataset with traceability, QC checks, and explainable flags.

**Public record:** repository created/published **Dec 28, 2025**.

---

## Table of contents
- [Overview](#overview)
- [Who this is for](#who-this-is-for)
- [Quick start](#quick-start)
- [Key features](#key-features)
- [How it works](#how-it-works)
- [Project structure](#project-structure)
- [Input data requirements](#input-data-requirements)
- [What it outputs](#what-it-outputs)
- [Demo: Before → After](#demo-before--after)
- [Data privacy](#data-privacy)
- [Tests (smoke)](#tests-smoke)
- [Versioning](#versioning)
- [Roadmap](#roadmap)
- [License](#license)
- [Contact](#contact)

---

## Overview
Bank statements vary wildly across banks and export methods:
- different column names and formats (dates, decimals)
- inconsistent debit/credit conventions
- noisy descriptions and counterparty text
- missing metadata needed for analytics and review

This project standardizes everything into a single canonical schema and produces a consolidated output ready for:
- analysis (Python / Excel)
- dashboards (Power BI)
- audit/review workflows (incl. insolvency / compliance screening)

---

## Who this is for
- Analysts processing bank statements for cash flow / reconciliation
- BI specialists who need clean “Transactions” tables for Power BI
- Professionals reviewing transactions for risk patterns (transparent rules, keyword flags)
- Consultants building repeatable ETL instead of manual Excel cleaning

---

## Quick start
### 1) Create virtual environment
```bash
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate
```

### 2) Install dependencies
```bash
pip install -r requirements.txt
```

### 3) Run
Entry scripts live in `/run`.
When `run/main.py` is added (v0.1.0), the typical run will be:
```bash
python run/main.py --input "data/input" --output "data/output"
```

> Note: The repo is intentionally published early to lock the public date. The runnable entry point(s) are being added incrementally.

---

## Key features
- **Canonical schema** for transactions across banks (consistent columns & types)
- **Traceability**: keep `Source_Bank`, `Source_File`, optional row hash/id
- **QC checks**: missing fields, duplicates, date range, totals consistency (incremental)
- **Explainable flags** (no black box): keyword lists + explicit rule labels
- **Extensible bank profiles**: mapping configs for each bank export format
- **Portfolio-ready**: demo files + screenshots can be added without client data

---

## How it works
Target pipeline (transparent and auditable):
1. **Detect source format** (bank profile / input signature)
2. **Read input** (xlsx/csv)
3. **Normalize columns** into canonical schema
4. **Clean values** (date parsing, numeric parsing, text cleanup)
5. **QC checks** (counts, missing fields, duplicates, ranges)
6. **Screening (optional)**:
   - keyword-based flags
   - simple rules (round amounts, repetition, unusual frequency)
7. **Export consolidated output**:
   - `Transactions` table
   - QC summary
   - Flags table (with reasons)

---

## Project structure

### Current repo layout (as of Dec 28, 2025)
This is the real structure right now (minimal scaffolding):
```
bank-statement-processor/
├─ docs/                  # documentation / GitHub Pages (in progress)
├─ run/                   # runnable scripts (in progress)
├─ LICENSE
├─ README.md
└─ requirements.txt
```

### Target layout (planned for v0.1.0+)
This is the structure you will see as the pipeline is implemented.
It’s documented here to keep the architecture explicit and consistent:
```
bank-statement-processor/
├─ run/
│  ├─ main.py                      # CLI entry point (batch processing)
│  ├─ settings.py                  # run settings (paths, toggles)
│  ├─ io/
│  │  ├─ read_excel.py             # xlsx ingestion
│  │  ├─ read_csv.py               # csv ingestion
│  │  └─ detect_profile.py         # bank/profile detection
│  ├─ profiles/
│  │  ├─ __init__.py
│  │  ├─ mapping_registry.py       # profile registry
│  │  └─ banks/
│  │     ├─ <bank_name>.yml        # column mapping + parsing rules
│  ├─ transform/
│  │  ├─ normalize_schema.py       # canonical column mapping
│  │  ├─ clean_text.py             # description cleanup
│  │  └─ parse_dates_amounts.py    # robust parsing
│  ├─ qc/
│  │  ├─ qc_checks.py              # missing/dup/range/consistency
│  │  └─ qc_report.py              # qc summary builder
│  ├─ screening/
│  │  ├─ keywords.py               # keyword lists + matching
│  │  ├─ rules.py                  # explainable rules
│  │  └─ flagger.py                # builds Flags table with reasons
│  ├─ export/
│  │  ├─ export_excel.py           # consolidated xlsx writer
│  │  └─ export_csv.py             # optional csv outputs
│  └─ utils/
│     ├─ log.py                    # run logs
│     └─ hashing.py                # optional row hashing for traceability
├─ data/
│  ├─ input/                       # demo input (synthetic/anonymized)
│  └─ output/                      # demo output (generated)
├─ docs/
│  ├─ index.md                     # GitHub Pages landing
│  ├─ screenshots/                 # evidence screenshots (demo)
│  └─ notes/                       # design notes & decisions
├─ requirements.txt
├─ LICENSE
└─ README.md
```

---

## Input data requirements
The processor supports different bank formats via **profiles**. Profiles map raw columns to canonical fields.

### Canonical fields (meaning-level requirements)
Minimum set (to build Transactions):
- `TransactionDate` — date of transaction
- `Description` — memo/purpose/details
- `Amount` — signed amount OR debit/credit pair
Optional but recommended:
- `Currency`
- `Counterparty`
- `Account`
- `Source_Bank` (explicit or inferred)

### What the script expects (README evidence-friendly section)
At minimum, the input must provide:
- a date column (any supported format)
- an amount representation (signed or debit/credit)
- a description/details column for text cleanup + keyword screening

---

## What it outputs
Main output (default planned name):
- `consolidated_statements.xlsx`

Sheets (v0.1.0 target):
- `Transactions` — normalized table
- `QC_Summary` — quality checks summary
- `Flags` — suspicious items with labels and reasons

### Transactions (target columns)
Typical fields:
- `TransactionDate`
- `Description`
- `Amount`
- `Currency`
- `Counterparty` (optional)
- `Source_Bank`
- `Source_File`
- `Row_ID` or `Row_Hash` (optional)
- `Flag_Label` / `Flag_Reason` (if screening enabled)

---

## Demo: Before → After
This section is where you will add evidence-friendly examples:
- a small synthetic/anonymized input statement (xlsx/csv)
- the produced `consolidated_statements.xlsx`
- 2–4 screenshots:
  - input sample (few rows)
  - Transactions sheet (standard schema)
  - QC_Summary
  - Flags (with reasons)

---

## Data privacy
- Do **not** commit client statements.
- Demo files must be synthetic or anonymized.
- Screenshots should never expose personal bank details.

---

## Tests (smoke)
Planned quick checks (v0.1.0):
- run on a small demo input and produce output without errors
- verify canonical columns exist
- verify QC report is generated

---

## Versioning
Releases are used to freeze a citable version:
- GitHub Release + tag (e.g., `v0.1.0`)
- Optional: Zenodo DOI minted from the release (for long-term referencing)

---

## Roadmap
- [ ] Add CLI entry point (`run/main.py`)
- [ ] Add 2–3 bank profiles (column mappings + parsing rules)
- [ ] Implement QC checks + `QC_Summary`
- [ ] Implement explainable screening (`Flags` table with reasons)
- [ ] Add demo input/output + screenshots
- [ ] Publish GitHub Pages landing (docs)

---

## License
MIT License (see [LICENSE](LICENSE)).

---

## Contact
**Irina Tokmianina**  
LinkedIn: https://www.linkedin.com/in/tokmianina
