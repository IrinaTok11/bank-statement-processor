# Bank Statement Processor

A small Python utility that merges multiple **Excel bank statement exports** into one clean, analysis‑ready workbook.

This repo is intentionally simple: copy the script into a folder with your statements, run it, and get a single consolidated file you can filter, reconcile, and review.

---

## Who this is for
- Analysts who need a single table before building pivots / dashboards
- Bankruptcy / compliance workflows where you receive statements from several banks
- Anyone tired of copy‑pasting rows across exports

---

## Quick start

```bash
cd run
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate

pip install -r ../requirements.txt
python bank_statement_processor.py
```

**How to use it**
1) Put your statement files (`.xlsx` / `.xls`) into the `run/` folder (next to the script).  
2) Run the script.  
3) Open the output file: `consolidated_statements.xlsx`.

> Tip: keep only the statements you want to merge in `run/` while you run the script.

---

## What the script expects
The processor detects columns by keyword matching. It works best when each file has, at minimum:
- a **date** column (e.g., Date, Transaction Date)
- a **description/payee** column (e.g., Description, Payee, Merchant)
- either **Debit/Credit** columns *or* a single **Amount** column

If a bank uses unusual headers, the quickest fix is often to rename the columns in Excel to include obvious keywords like “date”, “description”, “debit”, “credit”, or “amount”.

---

## What it outputs
- `consolidated_statements.xlsx` saved in the same folder where you run the script (`run/`)
- A single sheet called **Transactions**
- Rows include a **Source_Bank** column (taken from the input filename) so you can trace every line back to its origin

Common output columns:
- `Date`
- `Description`
- `Amount_Out` / `Amount_In` (when available)
- `Transaction_ID` (when available)
- `Balance` (when available)
- `Description_normalized` (light cleanup for grouping/search)

---

## Repository structure
- `run/` — working folder (script + your statement files)
- `docs/` — portfolio‑style documentation (methodology + references)

---

## License
See `LICENSE`.
