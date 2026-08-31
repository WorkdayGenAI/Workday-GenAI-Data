# DGW Excel Data Pre-Validation Agent

**Setup Guide**

---

## Contents

1. [Prerequisites](#1-prerequisites)
2. [Create the Folder Structure](#2-create-the-folder-structure)
3. [Add the Input Workbooks](#3-add-the-input-workbooks)
4. [Prepare the DGW Workbook](#4-prepare-the-dgw-workbook)
5. [Prepare the Validation Rules Workbook](#5-prepare-the-validation-rules-workbook)
6. [Prepare the Reference ID Sections](#6-prepare-the-reference-id-sections)
7. [Configure the Input and Output Paths](#7-configure-the-input-and-output-paths)
8. [Run the Validator](#8-run-the-validator)
9. [Verify the Setup](#9-verify-the-setup)

---

## 1. Prerequisites

Ensure the following are available:

- Microsoft Excel-compatible `.xlsx` files
- Python 3.x
- Python package for Excel processing:
  ```bash
  pip install openpyxl
  ```
- Read access to the input location
- Write access to the output location

> **Note:** Input workbooks must not be password protected.

---

## 2. Create the Folder Structure

Create the following folders:

```
DGW_Validation/
├── input/
├── output/
└── run_validator.py
```

| Folder / File | Purpose |
|---------------|---------|
| `input/` | Stores the two source workbooks |
| `output/` | Stores the generated validation report |
| `run_validator.py` | Contains the validation agent implementation |

---

## 3. Add the Input Workbooks

Place the following two files in the `input` folder:

```
input/
├── DGW_Input.xlsx
└── Validation_Rules.xlsx
```

Only two input workbooks are required for a run:

- **DGW workbook** — containing the DGW data sheets
- **Validation Rules workbook** — containing validation rules and Reference ID sections

> The implementation should accept file paths dynamically and must not depend on these example file names.

---

## 4. Prepare the DGW Workbook

Each DGW data sheet should contain:

- A row with recognizable business field names
- At least one populated business record below the header
- Meaningful business columns

The header may appear below template or metadata rows. Example layout:

| Row | Content |
|-----|---------|
| Row 1 | Section label |
| Row 2 | Integration mapping |
| Row 3 | Data type |
| Row 4 | Required/Optional indicator |
| Row 5 | **Business field names** ← actual header |
| Row 6+ | Business data |

The workbook may also contain instruction, walkthrough, reference, code-list, DQ-rule, blank, or metadata sheets. These do not need to be removed — the agent detects and ignores them dynamically.

---

## 5. Prepare the Validation Rules Workbook

Include validation rule sections with identifiable labels such as:

- `Validation Rule`
- `Validation Rules`
- `Required Fields`

Each validation section should include:

- Section or process name
- Required fields
- Rule text
- Business field names referenced by the rules

**Example rule descriptions:**

```
Worker ID is required.
Worker ID must be unique.
Hire Date Format must be YYYY-MM-DD.
Annual Salary must be numeric.
Annual Salary cannot be zero.
```

> Do not depend on exact validation sheet names. The agent discovers and matches rule sections dynamically.

---

## 6. Prepare the Reference ID Sections

Reference sections should contain:

- **Reference ID** in the section title or sheet name
- One header row with reference field names
- Allowed values listed below the corresponding headers

**Example:**

| Worker Type | Location ID | Pay Rate Type |
|-------------|-------------|---------------|
| Employee | BLR01 | HOURLY |
| Contingent Worker | HYD01 | SALARIED |
| Intern | MUM01 | COMMISSION |

Keep allowed values field-specific. Do **not** include the following within the allowed-value rows:

- Data types
- Required/Optional indicators
- Instructions
- Notes
- Repeated headers

> If metadata is required, place it **above** the reference header row.

---

## 7. Configure the Input and Output Paths

The validator should receive three paths:

- DGW workbook path
- Validation Rules workbook path
- Output report path

**Example:**

```bash
python run_validator.py \
  --dgw "input/DGW_Input.xlsx" \
  --rules "input/Validation_Rules.xlsx" \
  --output "output/DGW_Validation_Report.xlsx"
```

> The file paths, workbook names, sheet names, process names, and column names must **not** be hardcoded.

---

## 8. Run the Validator

From the `DGW_Validation` folder, execute:

```bash
python run_validator.py \
  --dgw "input/DGW_Input.xlsx" \
  --rules "input/Validation_Rules.xlsx" \
  --output "output/DGW_Validation_Report.xlsx"
```

**Before execution, confirm:**

- [ ] Input workbooks are closed in Excel (if the environment restricts access to open files)
- [ ] Any existing output report with the same name is closed
- [ ] The output folder is writable
- [ ] Both input files open without Excel repair warnings

---

## 9. Verify the Setup

Confirm all of the following before production execution:

- [ ] Python 3.x is installed
- [ ] `openpyxl` is installed
- [ ] The DGW workbook is in the `input` folder
- [ ] The Validation Rules workbook is in the `input` folder
- [ ] The DGW workbook contains recognizable business headers
- [ ] Intended DGW sheets contain at least one data row
- [ ] Validation sections contain clear rule text
- [ ] Reference sections contain field-specific allowed values
- [ ] Input paths are passed dynamically
- [ ] The output folder exists and is writable
- [ ] The output workbook is not already open
- [ ] No process, sheet, or column names are hardcoded
