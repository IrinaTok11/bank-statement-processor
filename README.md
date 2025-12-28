[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](#quick-start)

# Bank statement processor (Excel → consolidated dataset)

A practical automation to **merge and standardize bank statements** into a single, analysis-ready dataset (Excel / Power BI / Python).
The core goal is repeatability: the same inputs and mapping rules produce the same output table with traceability.

---

## Table of contents
- [Overview](#overview)
  - [Scope](#scope)
- [Who this is for](#who-this-is-for)
- [Quick start](#quick-start)
- [Key features](#key-features)
- [How it works](#how-it-works)
- [Design decisions](#design-decisions)
- [Methodology](#methodology)
- [Reference](#reference)
- [Project structure](#project-structure)
- [Input data requirements](#input-data-requirements)
- [Output](#output)
- [Demo: Before → After](#demo-before--after)
- [Formatting and code style](#formatting-and-code-style)
- [Tests (smoke)](#tests-smoke)
- [License](#license)
- [Contact](#contact)

---

## Overview
Bank statements vary by bank and export method: headers, date formats, amount conventions, and text fields are inconsistent.
This project standardizes multiple statement files into a single canonical **Transactions** table and exports an Excel file that is ready for downstream analytics.

### Scope
This repository focuses on:
- consolidating multiple Excel statements into one output file
- normalizing the schema (canonical columns)
- lightweight cleanup (dates, amounts, text)
- traceability (source file / optional source bank tagging)

Optional (incremental additions as the project evolves):
- QC summary (missing values, duplicates, date ranges, consistency checks)
- explainable screening/flags (keyword + rule labels, no black box)

---

## Who this is for
- Financial analysts and BI specialists building Power BI dashboards from bank data
- Professionals who need repeatable preprocessing (instead of manual Excel cleaning)
- Review/audit workflows that require traceability back to the source file

---

## Quick start
This project is designed to run from the **/run** folder (it acts as a small workspace).

### 1) Create a virtual environment
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

### 3) Put input files into the run folder
Copy bank statement files (`.xlsx` / `.xls`) into:
- `run/` (next to `bank_statement_processor.py`)

### 4) Run the processor
```bash
cd run
python bank_statement_processor.py
```

---

## Key features
- **Multi-file consolidation**: processes multiple statement files in one run
- **Canonical schema**: converts different headers into a consistent Transactions layout
- **Robust parsing**: tolerant date parsing and numeric cleanup
- **Traceability**: keeps source file information for auditability
- **Excel-first output**: produces a single `consolidated_statements.xlsx` for quick review and BI use
- **Simple workflow**: minimal steps, easy to reproduce and explain

---

## How it works
High-level pipeline:
1. Read all statement files from the `run/` workspace (ignores Excel temp files like `~$...`)
2. Normalize headers into canonical fields (supported patterns documented in reference)
3. Parse and standardize:
   - dates
   - debit/credit / signed amounts
   - basic text cleanup
4. Export the consolidated result into a single Excel output file in the same folder

For a high-level diagram, see: `docs/reference/architecture.md`.

---

## Design decisions
- **Explainability first**: rules and mappings are explicit and auditable.
- **Minimal operational friction**: run from a single folder without complex CLI options.
- **Excel output as a baseline**: easy for review and sharing; Power BI can consume the Transactions table directly.
- **Traceability is non-negotiable**: outputs should always be linkable to a source file.

---

## Methodology
- Project documentation site (GitHub Pages-ready):
  - `docs/index.html`
  - `docs/methodology.html`

To publish it on GitHub Pages: set Pages source to the `/docs` folder in repository settings.

---

## Reference
- Architecture overview: `docs/reference/architecture.md`
- Supported formats & header matching: `docs/reference/supported_formats.md`
- Case study notes: `docs/reference/case_study.md`

---

## Project structure
Current repository layout:
```
bank-statement-processor/
├─ .gitignore
├─ LICENSE
├─ README.md
├─ requirements.txt
├─ run/
│  ├─ bank_statement_processor.py
│  └─ README.md
└─ docs/
   ├─ _config.yml
   ├─ index.html
   ├─ methodology.html
   ├─ assets/
   │  ├─ .gitkeep
   │  ├─ css/
   │  │  └─ portfolio.css
   │  └─ screenshots/
   │     └─ .gitkeep
   └─ reference/
      ├─ architecture.md
      ├─ case_study.md
      └─ supported_formats.md
```

---

## Input data requirements
### File formats
- Excel exports: `.xlsx` / `.xls` (primary)

### Minimum required meaning-level fields
Your input must contain:
- a transaction date column
- an amount representation (signed amount OR debit/credit columns)
- a description/details column

Different banks may name these columns differently.
Supported header patterns and mapping notes: `docs/reference/supported_formats.md`.

---

## Output
### Output file
- `consolidated_statements.xlsx` (saved into the `run/` folder)

### Output sheet
- `Transactions` (consolidated and standardized table)

Typical canonical fields:
- `TransactionDate`
- `Description`
- `Amount`
- `Currency` (optional)
- `Counterparty` (optional)
- `Source_File` (traceability)
- `Source_Bank` (optional / when enabled)

---

## Demo: Before → After
Recommended portfolio evidence (no client data):
- add a small synthetic/anonymized input file into `run/`
- run the script and keep the generated `consolidated_statements.xlsx`
- store screenshots in `docs/assets/screenshots/`:
  - input sample (few rows)
  - Transactions output (few rows)
  - (later) QC summary / Flags

---

## Formatting and code style
- Keep comments in English.
- Avoid embedding sensitive data in the repository (use synthetic/anonymized examples only).

---

## Tests (smoke)
Quick check workflow:
1. Put 2–3 sample statement files into `run/`
2. Run `python bank_statement_processor.py`
3. Verify `consolidated_statements.xlsx` is created and the Transactions sheet looks correct

---

## License
MIT License (see [LICENSE](LICENSE)).

---

## Contact
Irina Tokmianina  
LinkedIn: https://www.linkedin.com/in/tokmianina
