[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](#quick-start)

# Bank statement processor (Excel → consolidated Transactions)

A practical automation to **merge and standardize bank statement exports** into a single, analysis‑ready table.
The output is designed to be consumed directly in **Excel / Power BI / Python** and keeps **traceability** to the source file.

---

## Table of contents
- [Overview](#overview)
  - [Scope](#scope)
- [Who this is for](#who-this-is-for)
- [Quick start](#quick-start)
- [Key features](#key-features)
- [How it works](#how-it-works)
- [Input data requirements](#input-data-requirements)
- [Output](#output)
- [Methodology (Docs / GitHub Pages)](#methodology-docs--github-pages)
- [Reference notes](#reference-notes)
- [Project structure](#project-structure)
- [Data privacy](#data-privacy)
- [Formatting and code style](#formatting-and-code-style)
- [Tests (smoke)](#tests-smoke)
- [License](#license)
- [Contact](#contact)

---

## Overview
Bank statements vary by bank and export method: headers, date formats, amount conventions, and text fields are inconsistent.
This project consolidates multiple Excel statements into one workbook and standardizes them into a canonical **Transactions** table.

### Scope
This repository focuses on:
- consolidating multiple statement files into one output
- keyword-based column detection (date / description / debit / credit / amount / balance / transaction id)
- normalization into a standard schema
- basic cleanup (dates, numeric parsing, text normalization)
- traceability via a source tag (`Source_Bank` derived from the file name)

Planned extensions (kept explainable, no black box):
- QC summary (missing values, duplicates, date ranges, consistency checks)
- explainable screening/flags (keyword + rule labels)

---

## Who this is for
- Financial analysts and BI specialists building dashboards from bank data
- Professionals who need repeatable preprocessing (instead of manual Excel cleaning)
- Review/audit workflows that require traceability back to the source file

---

## Quick start
This project is designed to run from the **/run** folder (it acts as a workspace).

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

### 3) Put input files into the run workspace
Copy your bank statement exports (`.xlsx` / `.xls`) into:
- `run/` (next to `bank_statement_processor.py`)

Tip: Excel temporary files like `~$...` are ignored automatically.

### 4) Run the processor
```bash
cd run
python bank_statement_processor.py
```

### 5) Open the result
The processor creates an output file in the same folder:
- `run/consolidated_statements.xlsx`

---

## Key features
- **Multi-file consolidation**: processes multiple statement files in one run
- **Canonical schema**: converts different headers into a consistent Transactions layout
- **Robust parsing**: tolerant date parsing + numeric cleanup
- **Traceability**: adds a source tag (`Source_Bank` = file name without extension)
- **Excel-first output**: a single workbook for quick review and BI use
- **Simple workflow**: minimal steps, easy to reproduce and explain

---

## How it works
High-level pipeline:
1. Read all statement files from the `run/` workspace
2. Detect columns by keywords (date / description / in / out / amount / id / balance)
3. Filter obvious non-transaction/service rows (e.g., subtotal/balance/opening/closing lines)
4. Normalize:
   - dates → a standard date type
   - amounts → standardized numeric columns (`Amount_In`, `Amount_Out` when available)
   - descriptions → optional normalized text helper column
5. Export a consolidated Excel workbook with a `Transactions` sheet

Notes:
- `Source_Bank` is derived from the input file name (stem), so each row remains traceable.

---

## Input data requirements
### File formats
- Excel exports: `.xlsx` / `.xls`

### Minimum required meaning-level fields
Each input file must contain:
- a transaction **date** column
- a **description** column

Amounts can be represented as:
- a signed **amount** column, or
- separate **in/out** (credit/debit) columns (recommended)

Supported header patterns and detection notes:
- `docs/reference/supported_formats.md`

---

## Output
### Output file
- `consolidated_statements.xlsx` (saved into `run/`)

### Output sheet
- `Transactions` (consolidated standardized table)

Typical output columns (depending on what the source provides):
- `Date`
- `Transaction_ID` (when detected)
- `Amount_In` (when detected)
- `Amount_Out` (when detected)
- `Description`
- `Source_Bank`
- `Balance` (when detected)
- `Description_normalized` (when generated)

---

## Methodology (Docs / GitHub Pages)
This repository includes a documentation mini-site under `/docs`:
- `docs/index.html`
- `docs/methodology.html`

To publish it via GitHub Pages:
1. Repository Settings → Pages
2. Source: **Deploy from a branch**
3. Folder: **/docs**

---

## Reference notes
- Architecture overview: `docs/reference/architecture.md`
- Supported formats & header matching: `docs/reference/supported_formats.md`
- Case study notes: `docs/reference/case_study.md`

---

## Project structure
Current repository layout (matches the repo):
```
bank-statement-processor/
├─ .gitignore
├─ LICENSE
├─ README.md
├─ requirements.txt
├─ run/
│  ├─ bank_statement_processor.py         # main script (run from this folder)
│  └─ README.md                           # workspace rules (what to put here / what to keep)
└─ docs/
   ├─ _config.yml                         # GitHub Pages / Jekyll config
   ├─ index.html                          # docs landing page
   ├─ methodology.html                    # methodology page
   ├─ assets/
   │  ├─ .gitkeep
   │  ├─ css/
   │  │  └─ portfolio.css                 # styling for the docs pages
   │  └─ screenshots/
   │     └─ .gitkeep                      # place portfolio/evidence screenshots here
   └─ reference/
      ├─ architecture.md                  # architecture notes
      ├─ case_study.md                    # case study notes
      └─ supported_formats.md             # supported formats and header detection notes
```

---

## Data privacy
Do not commit client statements.
Use synthetic/anonymized examples for demos and screenshots.

---

## Formatting and code style
- Keep comments in English.
- Prefer readable, explainable rules over “black box” logic.
- Do not store sensitive information in the repository or screenshots.

---

## Tests (smoke)
Quick check workflow:
1. Put 2–3 statement files into `run/`
2. Run `python bank_statement_processor.py`
3. Verify `consolidated_statements.xlsx` is created and the `Transactions` sheet looks correct

---

## License
MIT License (see [LICENSE](LICENSE)).

---

## Contact
Irina Tokmianina  
LinkedIn: https://www.linkedin.com/in/tokmianina
