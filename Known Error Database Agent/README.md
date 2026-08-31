# Known Error Database Agent

**Workday EIB & Advanced Load Error Resolution**

_A practical guide to inputs, analysis workflow, outputs, and responsible use_

| | |
|---|---|
| Purpose | Resolve data-load errors faster |
| Primary Input | Workday HTML error report |
| Primary Output | Grouped correction table |
| Scope | EIB and Advanced Load |
| Style | Concise and consultant-grade |
| Version | 1.0 — August 2026 |

---

## Contents

1. [What the Agent Does](#1-what-the-agent-does)
2. [How to Use the Agent](#2-how-to-use-the-agent)
3. [How the Agent Processes a Report](#3-how-the-agent-processes-a-report)
4. [Standard Output Format](#4-standard-output-format)
5. [Interpreting and Applying the Result](#5-interpreting-and-applying-the-result)
6. [Quick Reference](#6-quick-reference)

---

## 1. What the Agent Does

The Known Error Database Agent analyzes Workday EIB and Advanced Load failure reports in HTML format. It converts row-level technical messages into a short, grouped explanation that helps a support or delivery team understand the error, the true cause, and the corrective action.

> **Primary objective:** Enable a user to correct the load with minimal back-and-forth, without repeating the same explanation for every failed row.

### Questions the Agent Answers

| WHAT | WHY | HOW |
|------|-----|-----|
| What is the error? | Why did it occur? | How can it be corrected? |

### Core Capabilities

- Reads and interprets Workday row-level error messages
- Groups identical or closely related errors into one issue
- Counts the failed records in each error group
- Identifies the root cause instead of only repeating the system message
- Provides a concise and actionable correction
- Explains EIB versus Advanced Load behavior only when the difference changes the fix

### What the Agent Does Not Do

- Execute Workday actions, integrations, or data loads
- Fabricate Workday behavior when evidence is missing
- Evaluate individual performance
- Escalate an issue unless data or configuration cannot be corrected from the available evidence

---

## 2. How to Use the Agent

Provide the original Workday HTML failure report whenever possible. The quality of the analysis depends on the completeness and clarity of the error data.

### Workflow

| Step | Action | What to Do |
|------|--------|-----------|
| 1 | Prepare the input | Export or save the EIB or Advanced Load error report in HTML format. Keep row-level messages and identifiers intact. |
| 2 | Submit the report | Attach the HTML file and state whether the load is EIB or Advanced Load if that information is known. |
| 3 | Review the grouped analysis | Use the Error Summary Table to see representative messages, failure counts, root causes, and corrections. |
| 4 | Apply the correction | Correct source data, reference values, configuration, effective dates, security, or dependencies as directed. |
| 5 | Rerun and verify | Run the corrected load and compare any remaining failures with the previous error groups. |

### Recommended Prompt

> _"Analyze the attached Workday HTML error report. Group related failures, count the affected records, identify the root cause, and provide the correction using the standard Error Summary Table."_

### Input Checklist

| | |
|---|---|
| Original HTML report | Complete row-level errors |
| Load type, if known | Relevant file or batch context |
| No removed error columns | Latest failed run |

---

## 3. How the Agent Processes a Report

The agent follows a consistent reasoning sequence. Classification supports analysis internally, but the user-facing answer remains focused on correction.

### Processing Steps

| Step | Activity | What Happens |
|------|----------|-------------|
| 1 | Extract | Read each row-level failure and retain the meaningful system message. |
| 2 | Normalize | Remove incidental differences that do not change the underlying issue. |
| 3 | Group | Combine identical or related messages and count affected records. |
| 4 | Diagnose | Determine the true cause using only evidence present in the report. |
| 5 | Correct | State the shortest practical action that resolves the cause. |
| 6 | Qualify | Add notes only where Workday behavior or prerequisites affect the fix. |

### Internal Error Categories

| Category | Working Definition |
|----------|--------------------|
| Validation | A value fails a business or format rule. |
| Reference Data | A referenced Workday object or value is missing or invalid. |
| Effective Dating | Dates conflict with the effective-dated state of the object. |
| Dependency | A prerequisite worker, object, or transaction does not exist yet. |
| Security | The executing account lacks required access. |
| Mapping | Source values or fields are mapped incorrectly. |
| System | The failure is caused by a platform or processing condition. |

---

## 4. Standard Output Format

The primary response is always one grouped **Error Summary Table**. A Notes section appears only when a Workday behavior, load-type difference, or prerequisite materially affects the correction.

### Error Summary Table (Example Structure)

| Error Group | Representative Error Message | Number of Records | Root Cause | How to Correct |
|-------------|------------------------------|:-----------------:|-----------|---------------|
| Missing reference | Invalid value for Location ID | 24 | The location reference in the file does not exist or is inactive. | Use an active Workday Location ID and reload. |
| Dependency | Worker reference not found | 8 | The worker has not been created or is not available as of the load date. | Create or load the worker first, then rerun these records. |
| Effective date | Effective date is not valid | 3 | The transaction date conflicts with the object's effective-dated history. | Use a valid effective date that follows the existing sequence. |

> **Note:** The sample messages above illustrate the required structure. Actual root causes and corrections must be derived from the submitted report.

### Output Rules

- One row per grouped error, not one row per failed record
- One representative message per error group
- A record count for every group
- Short, business-friendly wording
- No speculative or hypothetical causes
- No repeated explanation across the table and Notes section

### When Notes Are Appropriate

- Serialization versus validation behavior changes the resolution sequence
- EIB and Advanced Load handle the same condition differently
- A prerequisite transaction or object must exist before the failed records can load

---

## 5. Interpreting and Applying the Result

### Correction Order

Where multiple error groups exist, address dependencies and reference data first. These issues can create downstream validation errors that may disappear after the prerequisite is corrected.

| Order | Focus | Action |
|:-----:|-------|--------|
| 1 | Dependencies | Create prerequisite workers, objects, or transactions. |
| 2 | Reference data | Correct missing, inactive, or invalid Workday references. |
| 3 | Mapping and validation | Fix source values, formats, and business-rule failures. |
| 4 | Effective dating | Align transaction dates to the object history. |
| 5 | Security or system | Confirm access or platform conditions when data correction is insufficient. |

### Efficiency Guidance

- Fix the highest-volume error groups first to reduce the largest number of failures per change
- Retain the original and corrected reports to confirm which groups were eliminated
- Use consistent Workday reference IDs rather than display labels where the template requires identifiers
- Rerun a controlled subset when a correction affects configuration or effective dating

> **Important:** Time-saving figures shown in presentation materials are illustrative workflow estimates. Actual savings vary by report size, complexity, data quality, and user familiarity.

### Escalation Boundary

Escalation is appropriate only when the report indicates that source-data correction is not sufficient — such as an unresolved security restriction, unavailable configuration, or a system-processing condition. Include the error group, representative message, affected count, attempted correction, and rerun result.

### Stage-by-Stage Checklist

| Stage | Check |
|-------|-------|
| Before submission | Use the complete HTML report; preserve row-level messages; include the latest failed run. |
| During review | Confirm grouped counts; focus on root cause; check Notes for prerequisites. |
| Before correction | Prioritize dependencies and references; validate effective dates; confirm load type where relevant. |
| After correction | Rerun; compare remaining groups; retain evidence of resolved errors. |

> **Success outcome:** A concise, grouped answer that enables the user to fix the data or configuration and rerun the load with minimal clarification.

---

## 6. Quick Reference

### Good Request Examples

- _"Group the failures in this EIB HTML report and tell me what must be corrected."_
- _"Analyze this Advanced Load report and identify whether any errors share the same root cause."_
- _"Compare the remaining failure groups after the rerun with the previous grouped results."_

### Less Effective Requests

- A screenshot that hides or truncates row-level messages
- A copied list without the associated errors or record context
- A request to guess the cause without providing the failure report

