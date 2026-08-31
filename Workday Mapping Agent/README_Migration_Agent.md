# PeopleSoft → Workday Migration Agent

**Setup & User Guide**

| | |
|---|---|
| Version | 1.0 |
| Date | August 2026 |
| Confidentiality | Internal Use Only |

---

## 1. Overview

The Legacy → Workday Migration Agent is a browser-based, self-contained data migration tool built for Accenture delivery teams. It reads a source extract, applies Accenture's standard mapping and transformation rules (sourced from uploaded Knowledge Article files), validates every field, and generates a Workday-ready DGW output file — all without requiring any server, database, or IT setup.

**Key capabilities:**
- **Knowledge Article–driven:** transformation rules are read from uploaded Excel files, not hard-coded
- **11 built-in transformation rules** (zero-pad, date conversion, title-case, suffix normalisation, and more)
- **Intelligent mapping review:** shows only rules that match fields present in the uploaded source file
- **Full validation report:** mandatory fields, duplicates, format checks, referential integrity
- **One-click export:** DGW Excel file (all sheets), per-sheet CSV, and a Mapping Report
- **No installation required** — runs as a standalone HTML file in any modern browser

---

## 2. Prerequisites

| Requirement | Details |
|-------------|---------|
| Web Browser | Google Chrome (recommended) · Microsoft Edge · Mozilla Firefox |
| PeopleSoft Source File | Excel export (`.xlsx` / `.xls`) with 1–4 sheets e.g. Worker Names, Email, Phone, National IDs |
| Mapping Specification | `Legacy to Workday Mapping V1.xlsx` (Knowledge Article) |
| DGW Template | `Workday DGW Sample Expected File.xlsx` (Knowledge Article) |
| Python (optional) | Python 3.9+ with Streamlit — only needed for the full Streamlit web app mode |
| Internet access | Required at first launch only (loads xlsx.js CDN). Can be cached for offline use. |

> **Note:** The HTML agent is fully self-contained. No server is needed. Simply open the `.html` file in Chrome and upload the three required files.

---

## 3. File Structure

The agent package contains the following files:

| File | Purpose |
|------|---------|
| `PS_to_Workday_Migration_Agent.html` | Main browser-based migration agent (open in Chrome) |
| `run_workday_mapping_agent.bat` | Windows batch file — installs dependencies and launches Streamlit app |
| `workday_mapping_agent.py` | Python launcher shim — delegates to the full Streamlit app |
| `Knowledge Articles\Accenture PS to WD Mapping V1.xlsx` | Mapping Specification Knowledge Article |
| `Knowledge Articles\Workday DGW Sample Expected File.xlsx` | DGW Template Knowledge Article |
| `Input file\Input File - Sample Data.xlsx` | Sample PeopleSoft source data (25 workers, 4 sheets) |

---

## 4. How to Run the Agent

### Option A — HTML Agent (Recommended, No Setup)

This is the simplest and fastest option. No Python or server is required.

1. Navigate to the agent folder in Windows Explorer.
2. Double-click `PS_to_Workday_Migration_Agent.html` — it opens in your default browser.
3. If Chrome is not the default browser, right-click the file → **Open with** → **Google Chrome**.
4. Follow the 5-step wizard inside the app (see [Section 5](#5-step-by-step-usage-guide)).

> **Tip:** The HTML agent requires an internet connection on first load to fetch the xlsx.js library from CDN. After the first load, the library is cached by the browser for offline use.

### Option B — Streamlit Web App

Use this option if you need the full Python-backed Streamlit interface.

1. Open a Command Prompt or PowerShell window.
2. Run the batch file:
   ```
   run_workday_mapping_agent.bat
   ```
3. The batch file will automatically install required Python packages (`streamlit`, `pandas`, `openpyxl`).
4. When prompted, choose **Y** to open the HTML agent, or press **N** to proceed directly to Streamlit.
5. The Streamlit app opens automatically at: http://localhost:8502
6. To stop the server, press `Ctrl + C` in the Command Prompt window.

---

## 5. Step-by-Step Usage Guide

The HTML agent uses a 5-step wizard. Each step is accessible from the left sidebar.

### Step 1 — Upload Files

Upload three files:
- **PeopleSoft Source Data** (`.xlsx`) — your employee data export
- **Mapping Specification** (`.xlsx`) — Accenture PS→WD Mapping Knowledge Article
- **Workday DGW Template** (`.xlsx`) — DGW Sample Expected File Knowledge Article

All three files must be uploaded before the **Run Migration Agent** button is enabled.

> To test without a real source file, click **Load Demo Data (30 workers)** after uploading both Knowledge Articles.

### Step 2 — Mapping Review

Displays only the mapping rules whose PS fields are present in your uploaded source file. Rules from the Knowledge Article that do not apply to your source (e.g. address, relatives, bio-demo) are automatically excluded.

Review the PS Field → Workday Field mapping, Required/Optional status, and Transformation/Logic notes. Click **Apply & Transform →** to proceed.

### Step 3 — Data Preview

Shows the fully transformed Workday output data, organised into tabs matching the DGW template sheets (`NAME` · `EMAIL ADDRESS` · `PERSON PHONE` · `PERSON ID`).

All 11 transformation rules have been applied: Worker IDs are zero-padded, dates are in `YYYY-MM-DD` format, names are title-cased, suffixes are normalised, and empty columns (fields not in source) are dropped.

### Step 4 — Validation

Automated quality checks include:
- Mandatory field presence (Worker ID, First Name, Last Name, Email Address, etc.)
- Email format validation (regex)
- National ID type validation (`PR`, `SSN`, `NIN`, `SIN`, `TFN`)
- Primary flag enforcement (exactly 1 `Y` per worker per domain)
- Duplicate Worker ID detection
- Referential integrity across all sheets

Results are colour-coded: **PASS** (green) · **WARN** (amber) · **FAIL** (red).

### Step 5 — Export

Download options:
- **DGW File (Excel — all sheets):** the Workday-ready output file
- **Mapping Report (Excel):** filtered mapping rules + transformation rules + validation results
- **Per-sheet CSV:** individual CSV file for each DGW sheet

The recommended Workday load sequence is also displayed:
```
NAME → PERSON ID → EMAIL ADDRESS → PERSON PHONE
```

---

## 6. Knowledge Articles

The agent is knowledge article–driven. All mapping and output format rules are read at runtime from the two uploaded Knowledge Article files. This means the agent adapts automatically when the mapping specification or DGW template is updated — no code changes are needed.

### 6.1 Mapping Specification

**File:** `Accenture PeopleSoft to Workday Mapping V1.xlsx`

This file defines the field-level mapping between PeopleSoft source fields and Workday target fields. Each sheet represents a Workday data object. The agent parses all sheets and extracts:
- PS Source Table and Field name
- Workday Target Table and Field name
- Required / Optional flag
- Transformation notes and comments

**Sheets used by this agent** (others are parsed but excluded if not in source):

| Sheet Name | Workday Target |
|------------|---------------|
| Worker Name Data | NAME |
| Worker Email Data | EMAIL ADDRESS |
| Worker Phone Data | PERSON PHONE |
| Worker National ID Data | PERSON ID |

### 6.2 DGW Template

**File:** `Workday DGW Sample Expected File.xlsx`

This file defines the exact column structure of the Workday output. Each sheet name and its columns become the target schema for the corresponding transformed output sheet.

| DGW Sheet | Columns |
|-----------|---------|
| NAME | Worker ID · Prehire Effective Date · Prefix · Name Effective Date · First Name · Middle Name · Last Name · Suffix |
| EMAIL ADDRESS | Worker ID · Email Type · Primary · Email Address |
| PERSON PHONE | Worker ID · Phone Number Type · Primary · International Phone Code · Phone Number · Phone Extension |
| PERSON ID | Worker ID · National ID · National ID Type |

> **Key benefit:** If Workday adds or reorders columns in a future release, simply update the DGW Template file and re-upload — the agent will automatically produce output in the new column order.

---

## 7. Transformation Rules

The agent applies 11 built-in transformation rules automatically during Step 2 → Step 3. No manual configuration is required.

| Rule | Name | Source Example | Workday Output |
|------|------|---------------|---------------|
| R01 | Zero-Pad Worker ID | `10021` | `010021` |
| R02 | Excel Serial → ISO Date | `44927` | `2021-01-01` |
| R03 | ISO Date Pass-Through | `2021-01-01` | `2021-01-01` (no change) |
| R04 | Suffix Normalisation | `Junior` | `Jr.` |
| R05 | Email Lowercase + Validate | `User@Co.COM` | `user@co.com` |
| R06 | Phone/Email Type Pass-Through | `BUSN` | `BUSN` |
| R07 | Primary Flag Enforcement | `Y` | 1 Y per worker enforced |
| R08 | Null → Blank | `NULL / NaN` | `""` (empty string) |
| R09 | Name Title-Case | `JOHN DOE` | `John Doe` |
| R10 | National ID Type Validation | `PR` | Validated vs allowed list |
| R11 | Country Code Numeric | `+44 / +1` | `44 / 1` |

---

## 8. Input File Requirements

The PeopleSoft source file should be an Excel workbook (`.xlsx` or `.xls`) with one sheet per HR domain. The agent auto-detects each domain by scanning column names.

| Domain | Detection Columns | Required Source Columns |
|--------|------------------|------------------------|
| Worker Names | `FIRST_NAME` · `NAME_PREFIX` · `SALUTATION` | `EMPLID` · `PREHIRE_EFFECTIVE_DATE` · `FIRST_NAME` · `LAST_NAME` |
| Email | `EMAIL_ADDR` · `E_ADDR_TYPE` | `EMPLID` · `E_ADDR_TYPE` · `PREF_EMAIL_FLAG` · `EMAIL_ADDR` |
| Phone | `PHONE` · `PHONE_TYPE` · `PREF_PHONE_FLAG` | `EMPLID` · `PHONE_TYPE` · `PREF_PHONE_FLAG` · `COUNTRY_CODE` · `PHONE` |
| National ID | `NATIONAL_ID` · `NATIONAL_ID_TYPE` | `EMPLID` · `NATIONAL_ID` · `NATIONAL_ID_TYPE` |

> **Extra columns:** The agent accepts source files with additional columns beyond the standard ones. Extra columns are ignored during transformation. Extra metadata/title rows above the column header row are also handled automatically — the agent scans up to 10 rows to find the actual header.

---

## 9. Troubleshooting

| Issue | Resolution |
|-------|-----------|
| "Run Migration Agent" button stays disabled | Upload all 3 files (Source Data + Mapping File + DGW Template). The button only enables when all 3 are loaded. |
| Mapping Review shows 0 rules | Ensure the Mapping Specification file has the expected sheets (e.g. `Worker Name Data`) with Table/Field column headers. Also verify the source file columns match known PS field names (`EMPLID`, `FIRST_NAME`, etc.). |
| Data Preview shows blank columns | Blank columns are automatically dropped — they indicate that field is not in the source file. This is expected behaviour. |
| Date columns show incorrect values | Ensure dates in the source file are stored as date cells in Excel, not plain text. The agent handles ISO-8601 strings and Excel serial numbers. |
| Validation FAIL on Primary flag | Each worker must have exactly one `Y` in the `PREF_EMAIL_FLAG` or `PREF_PHONE_FLAG` column. Check for duplicate rows per worker or missing `Y` flags. |
| "No mapping rows found" error on Mapping File upload | The mapping file must contain a header row with the words "Table" and "Field" in each sheet. Check the file is the correct Accenture Mapping V1 format. |
| xlsx.js fails to load (blank page) | Check internet connectivity — the agent loads xlsx.js from `cdn.jsdelivr.net`. Alternatively, download `xlsx.full.min.js` and update the `<script>` src to a local path. |

---

_© 2026 Accenture. Internal Use Only._
