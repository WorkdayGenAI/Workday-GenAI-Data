# Clover Graph Optimizer Agent

**User Guide**  
Workday Data Conversion and CloverDX Graph Optimization

**Version:** 1.0  
**Last Updated:** September 2026  
**Audience:** Workday data-conversion consultants, CloverDX developers, technical leads, and reviewers  
**Classification:** Internal project documentation

## Contents

- [A. Document Control](#a-document-control)
  - [Purpose of this guide](#purpose-of-this-guide)
- [1. Introduction](#1-introduction)
  - [1.1 What Does This Agent Do?](#11-what-does-this-agent-do)
  - [1.2 Who Should Use This Agent?](#12-who-should-use-this-agent)
  - [1.3 Important Scope](#13-important-scope)
- [2. Key Capabilities](#2-key-capabilities)
  - [2.1 Graph Performance Analysis](#21-graph-performance-analysis)
  - [2.2 Map Component Optimization](#22-map-component-optimization)
  - [2.3 DGW Comparison](#23-dgw-comparison)
  - [2.4 Error Diagnosis](#24-error-diagnosis)
- [3. Prerequisites](#3-prerequisites)
  - [3.1 Do Not Provide](#31-do-not-provide)
- [4. Installation](#4-installation)
  - [4.1 Obtain the Application Package](#41-obtain-the-application-package)
  - [4.2 Extract the Package](#42-extract-the-package)
  - [4.3 Verify the Package](#43-verify-the-package)
  - [4.4 Confirm Clover Compatibility](#44-confirm-clover-compatibility)
- [5. Configuration](#5-configuration)
  - [5.1 Configuration Principles](#51-configuration-principles)
  - [5.2 Recommended Defaults](#52-recommended-defaults)
  - [5.3 Secrets and Credentials](#53-secrets-and-credentials)
- [6. Preparing Input Files](#6-preparing-input-files)
  - [6.1 Clover Graph XML](#61-clover-graph-xml)
  - [6.2 DGW](#62-dgw)
  - [6.3 Error Logs](#63-error-logs)
- [7. Using the Agent](#7-using-the-agent)
  - [7.1 Start a New Review](#71-start-a-new-review)
  - [7.2 Recommended Request Format](#72-recommended-request-format)
  - [7.3 Expected Response](#73-expected-response)
- [8. Optimization Rules](#8-optimization-rules)
  - [8.1 Priority 1: Correctness](#81-priority-1-correctness)
  - [8.2 Priority 2: Execution Performance](#82-priority-2-execution-performance)
  - [8.3 Priority 3: Maintainability](#83-priority-3-maintainability)
  - [8.4 Priority 4: Observability](#84-priority-4-observability)
- [9. DGW-to-Graph Column Comparison](#9-dgw-to-graph-column-comparison)
  - [9.1 Field Addition Rule](#91-field-addition-rule)
  - [9.2 Missing Source Rule](#92-missing-source-rule)
- [10. Mapping Rules](#10-mapping-rules)
  - [10.1 Direct Mapping](#101-direct-mapping)
  - [10.2 CTL2 Transformation](#102-ctl2-transformation)
  - [10.3 Null-Safe Mapping](#103-null-safe-mapping)
  - [10.4 Multiple Required Fields](#104-multiple-required-fields)
  - [10.5 Prohibited Pattern](#105-prohibited-pattern)
- [11. Output Format](#11-output-format)
  - [11.1 Key Findings](#111-key-findings)
  - [11.2 Recommended Changes](#112-recommended-changes)
  - [11.3 Complete Rewritten XML](#113-complete-rewritten-xml)
  - [11.4 Error Solution and Mapping Summary](#114-error-solution-and-mapping-summary)
- [12. Validation and Quality Checks](#12-validation-and-quality-checks)
  - [12.1 XML Checks](#121-xml-checks)
  - [12.2 CTL2 Checks](#122-ctl2-checks)
  - [12.3 Graph Checks](#123-graph-checks)
  - [12.4 DGW Checks](#124-dgw-checks)
- [13. Common Tasks](#13-common-tasks)
  - [Task A: Optimize an Existing Clover Graph](#task-a-optimize-an-existing-clover-graph)
  - [Task B: Add DGW-Required Fields](#task-b-add-dgw-required-fields)
  - [Task C: Correct a CTL1 Mapping](#task-c-correct-a-ctl1-mapping)
  - [Task D: Diagnose an Execution Error](#task-d-diagnose-an-execution-error)
  - [Task E: Create a New Field Mapping](#task-e-create-a-new-field-mapping)
- [14. Troubleshooting](#14-troubleshooting)
  - [Problem: CTL1 Is Not Supported](#problem-ctl1-is-not-supported)
  - [Problem: At Least One Output Port Must Be Defined](#problem-at-least-one-output-port-must-be-defined)
  - [Problem: Graph Configuration Is Invalid](#problem-graph-configuration-is-invalid)
  - [Problem: XML Processing Instruction Is Not Allowed](#problem-xml-processing-instruction-is-not-allowed)
  - [Problem: Output Field Is Missing](#problem-output-field-is-missing)
  - [Problem: Input Field Is Missing](#problem-input-field-is-missing)
  - [Problem: Data-Type Mismatch](#problem-data-type-mismatch)
  - [Problem: Graph Runs Slowly](#problem-graph-runs-slowly)
- [15. Frequently Asked Questions](#15-frequently-asked-questions)
- [16. Security and Data-Handling Guidelines](#16-security-and-data-handling-guidelines)
  - [16.1 Mandatory Rules](#161-mandatory-rules)
  - [16.2 Recommended Masking](#162-recommended-masking)
- [17. Getting Support](#17-getting-support)

# A. Document Control

| **Item** | **Value** |
|----|----|
| Document | Clover Graph Optimizer Agent: User Guide |
| Version | 1.0 |
| Updated | September 2026 |
| Classification | Internal project documentation |
| Review cycle | Update when agent behavior, Clover version, or DGW rules change |

## Purpose of this guide

This guide explains how to prepare inputs, use the Clover Graph Optimizer Agent, review its outputs, validate rewritten Clover graph XML, troubleshoot common failures, and protect Workday conversion data.

| **Important** All generated graph changes must be reviewed and validated in the target CloverDX or Clover ETL version before use with production workloads. Use masked or approved test data. |
|----|

# 1. Introduction

## 1.1 What Does This Agent Do?

The Clover Graph Optimizer Agent analyzes, corrects, and optimizes CloverDX or Clover ETL graph XML used in Workday data-conversion activities.

- Identify likely graph performance bottlenecks.
- Detect maintainability and design risks.
- Simplify unnecessarily complex graph patterns.
- Reduce redundant Clover components.
- Optimize transformation logic inside mapping components.
- Convert unsupported CTL1 code to CTL2.
- Compare a Data Gathering Workbook (DGW) with a Clover graph.
- Add applicable DGW-required columns to the graph output.
- Create or correct input-to-output mapping rules.
- Diagnose errors from logs and recommend solutions.
- Return complete rewritten graph XML in the correct execution order.
- Provide a focused checklist of what to change and what to measure.

## 1.2 Who Should Use This Agent?

- Workday data-conversion consultants.
- CloverDX and Clover ETL developers.
- Integration developers.
- Technical architects.
- Conversion leads and reviewers.
- Team members responsible for graph optimization, mapping validation, or defect resolution.

## 1.3 Important Scope

The agent works from Clover graph XML, DGW field definitions, metadata and record layouts, masked sample records, record counts and transformation summaries, and sanitized error logs or validation messages.

| **Data protection** Do not provide raw employee PII, passwords, access tokens, production credentials, unmasked employee records, bank details, or compensation data tied to identifiable workers. |
|----|

# 2. Key Capabilities

## 2.1 Graph Performance Analysis

- Redundant components and repeated transformations.
- Unnecessary sorting, joins, lookups, or repeated file reads.
- Excessive metadata conversion and avoidable data copying.
- Duplicate validation or inefficient error-routing patterns.
- Long, repetitive map expressions and sequential processing that can be simplified.

## 2.2 Map Component Optimization

- Use CTL2 for transformation code.
- Reduce repeated expressions and parsing operations.
- Simplify conditional logic and null handling.
- Avoid unsupported declarations.
- Use direct port references and explicit output assignments.
- Preserve existing business rules unless the requested change requires modification.

| **Required mapping convention** Do not use var. Use $in.0 for input references and $out.0 for output assignments. Initialize or map output fields explicitly. |
|----|

## 2.3 DGW Comparison

- Identify DGW columns missing from graph output.
- Review each field’s requirement classification.
- Add only applicable new fields marked Required.
- Locate corresponding source input columns.
- Update output metadata and CTL2 mapping logic.
- Report required fields for which no approved source mapping is available.

Optional, conditional, informational, and not-applicable DGW fields are not added unless explicitly requested.

## 2.4 Error Diagnosis

- Identify the likely failing component.
- Explain the probable root cause.
- Recommend or apply a correction when sufficient context is available.
- Provide validation checks for the corrected graph.

# 3. Prerequisites

| **Requirement** | **Details** |
|----|----|
| Clover graph | Complete .grf file or graph XML content |
| Metadata | Embedded metadata or referenced record layouts |
| DGW | Relevant workbook or field extract with requirement status |
| Error log | Recommended for failed graph troubleshooting |
| Input schema | Field names, types, sizes, nullability, and formats |
| Output schema | Fields expected by the target Workday load |
| Samples | Masked examples only |
| Counts | Input, rejected, transformed, and output counts when available |
| Clover version | CloverDX or Clover ETL version used for execution |
| Environment | Non-sensitive runtime and execution information |

## 3.1 Do Not Provide

- Employee names, personal email addresses, home addresses, or phone numbers.
- Government identifiers, bank details, or identifiable compensation data.
- Passwords, Workday credentials, API keys, authentication cookies, or access tokens.
- Unmasked production records.

Use schemas, aggregate counts, summaries, sanitized logs, and masked samples instead.

# 4. Installation

## 4.1 Obtain the Application Package

Download or copy the approved Clover Graph Optimizer Agent package from the project’s authorized repository or shared location. Do not use software packages received from an unverified source.

## 4.2 Extract the Package

- Right-click the ZIP file.
- Select Extract All.
- Choose an approved local folder.
- Select Extract.
- Run the application only from the extracted folder.

## 4.3 Verify the Package

- Application executable or launcher.
- Configuration template and dependency files.
- Prompt or rules configuration.
- Documentation and masked sample input.
- Output and logs folders.

## 4.4 Confirm Clover Compatibility

- Graph XML schema.
- CTL2 transformation language.
- Source graph components.
- Metadata attributes in rewritten XML.
- Runtime parameters and referenced connections.

# 5. Configuration

## 5.1 Configuration Principles

Use the approved project configuration template. Do not place secrets in a README, prompt, graph XML, or shared log.

APPLICATION_MODE=local  
OUTPUT_DIRECTORY=\<approved-output-folder\>  
LOG_LEVEL=INFO  
MASK_SENSITIVE_VALUES=true  
REQUIRE_CTL2=true  
ALLOW_CTL1=false  
ADD_DGW_REQUIRED_FIELDS_ONLY=true  
RETURN_COMPLETE_XML=true

These field names are illustrative. Use the settings supplied with the approved application package.

## 5.2 Recommended Defaults

| **Setting** | **Recommended value** | **Purpose** |
|----|----|----|
| Sensitive-value masking | Enabled | Prevents sensitive values from appearing in logs |
| CTL2 enforcement | Enabled | Prevents unsupported CTL1 mappings |
| Required-fields-only mode | Enabled | Limits DGW additions to required fields |
| Complete XML output | Enabled | Prevents partial graph fragments |
| Validation mode | Enabled | Performs structural checks before returning output |
| Log level | INFO | Captures useful diagnostics without excessive detail |

## 5.3 Secrets and Credentials

Never place credentials in graph XML, CTL2 code, agent prompts, shared error logs, screenshots, README files, or source-control repositories. Use approved secure parameters, runtime properties, or credential-management services.

# 6. Preparing Input Files

## 6.1 Clover Graph XML

Provide the complete graph XML whenever a rewrite is required. Include graph declaration, global settings, metadata, connections, component definitions, edges, ports, relevant runtime parameters, and existing mapping code.

| **Why complete XML matters** A complete graph allows the agent to preserve component IDs, metadata references, edges, ports, connections, and execution order. A mapping fragment alone is not enough to produce a reliably executable graph. |
|----|

## 6.2 DGW

| **DGW attribute** | **Example**                       |
|-------------------|-----------------------------------|
| Field name        | Worker_ID                         |
| Requirement       | Required                          |
| Description       | Unique worker reference           |
| Data type         | String                            |
| Length            | 50                                |
| Source column     | Employee_ID                       |
| Transformation    | Direct mapping                    |
| Default rule      | Blank or project-approved default |

## 6.3 Error Logs

- Complete error message.
- Component name and component ID.
- Graph validation output and relevant line or column number.
- Clover version.
- Masked input summary and record counts.
- Operation being performed when the error occurred.

# 7. Using the Agent

## 7.1 Start a New Review

- State the optimization or correction objective.
- Provide complete Clover graph XML.
- Attach the DGW when comparison is required.
- Include a sanitized error log when troubleshooting.
- Provide input and output metadata.
- Include masked transformation examples if needed.
- Provide baseline execution measurements when available.

## 7.2 Recommended Request Format

Objective:  
Optimize the attached Clover graph and add newly required DGW fields.  
  
Clover version:  
\[Version\]  
  
Input:  
\[Complete graph XML or attached graph\]  
  
DGW:  
\[Workbook or field extract\]  
  
Known error:  
\[Masked error log\]  
  
Baseline:  
Input count:  
Output count:  
Rejected count:  
Execution duration:  
Peak memory:  
Largest component duration:  
  
Constraints:  
Use CTL2.  
Do not use var.  
Use $in.0 and $out.0 references.  
Return complete XML only for the XML section.  
Do not modify existing business rules unless required.

## 7.3 Expected Response

- Key findings.
- Recommended changes.
- Complete rewritten XML.
- Error solution, when applicable.
- Rewritten mapping rule.
- Measurement checklist.

# 8. Optimization Rules

## 8.1 Priority 1: Correctness

- Preserve valid business logic and required graph connections.
- Maintain compatible metadata and valid graph configuration.
- Define required output ports.
- Ensure CTL2 fields exist in associated metadata.
- Avoid malformed XML, invalid nested declarations, and duplicate IDs.

## 8.2 Priority 2: Execution Performance

- Remove redundant components only when output behavior is preserved.
- Consolidate compatible transformations and repeated conversions.
- Filter records as early as safely possible.
- Retain only fields required downstream.
- Review sort, join, lookup, and temporary-file operations.
- Use direct mappings where no transformation is required.
- Separate reject processing from the main success path.

## 8.3 Priority 3: Maintainability

- Use clear component and metadata names.
- Keep mappings readable and separate business logic from error handling.
- Document non-obvious defaulting rules.
- Avoid unnecessary hard-coded values.
- Preserve traceability from DGW to source and output fields.

## 8.4 Priority 4: Observability

- Total execution duration and records per second.
- Input, output, and rejected record counts.
- Component-level duration and peak memory.
- Temporary-file size and sort or join duration.
- Lookup hits and misses where applicable.

# 9. DGW-to-Graph Column Comparison

- Read the DGW field list.
- Normalize names for comparison without silently renaming fields.
- Select records marked Required.
- Read input and output metadata.
- Compare required fields with output fields.
- Identify newly required columns.
- Find matching source fields.
- Validate source and target data types.
- Add fields to output metadata.
- Add CTL2 mappings.
- Preserve expected output order.
- Report unresolved required fields.

## 9.1 Field Addition Rule

A field is added only when it exists in the DGW, is marked Required, is not already in the graph output, has an approved source or transformation rule, and can be defined without creating a validation error.

## 9.2 Missing Source Rule

If a required field has no corresponding source column, the agent must not invent employee data. The field is reported as unresolved. A default is used only when explicitly defined by the DGW or an approved project rule.

# 10. Mapping Rules

## 10.1 Direct Mapping

Example: source input ABC must populate target output ABCD.

$out.0.ABCD = $in.0.ABC;

Apply the assignment inside the CTL2 mapping component responsible for creating the target output record. Output metadata must define ABCD with the correct type, length, scale, nullability, and format.

## 10.2 CTL2 Transformation

function integer transform() {  
$out.0.ABCD = $in.0.ABC;  
return ALL;  
}

## 10.3 Null-Safe Mapping

function integer transform() {  
$out.0.ABCD = isnull($in.0.ABC) ? "" : $in.0.ABC;  
return ALL;  
}

Use a blank string only when permitted by the business rule and target metadata. Do not use it for a date, decimal, integer, Boolean, or another non-string type.

## 10.4 Multiple Required Fields

function integer transform() {  
$out.0.ABCD = $in.0.ABC;  
$out.0.Worker_Reference = $in.0.Employee_ID;  
$out.0.Effective_Date = $in.0.Source_Effective_Date;  
return ALL;  
}

## 10.5 Prohibited Pattern

var string mappedValue = $in.0.ABC;  
$out.0.ABCD = mappedValue;

Use a direct assignment instead. This follows the agent’s mapping convention and removes an unnecessary local declaration.

# 11. Output Format

## 11.1 Key Findings

Three to five concise findings covering bottlenecks, validation errors, metadata or mapping gaps, maintainability risks, and DGW-required differences.

## 11.2 Recommended Changes

- Validation-critical corrections.
- Mapping and metadata corrections.
- Performance improvements.
- Maintainability improvements.
- Measurements for the next execution.

## 11.3 Complete Rewritten XML

- Complete graph XML with declaration at the document start.
- Valid graph settings and dependency order.
- Metadata, components, ports, connections, and edges.
- CTL2 mapping code.
- No explanatory prose inside the XML block.

## 11.4 Error Solution and Mapping Summary

For errors, include cause, affected component, correction, validation step, and where the change appears in the rewritten XML. For mapping changes, include source field, target field, transformation rule, component, metadata impact, and null or default behavior.

# 12. Validation and Quality Checks

## 12.1 XML Checks

- [ ] XML declaration appears only at the beginning.
- [ ] No XML declaration exists inside a component mapping.
- [ ] Special characters are escaped correctly.
- [ ] CDATA is used only where supported and is correctly closed.
- [ ] Tags are nested and closed.
- [ ] Component and edge IDs are unique.
- [ ] Metadata references resolve.

## 12.2 CTL2 Checks

- [ ] No CTL1 code remains.
- [ ] No prohibited var declarations are introduced.
- [ ] $in.0 and $out.0 references use valid ports.
- [ ] Input and output fields exist in metadata.
- [ ] Assignments use compatible data types.
- [ ] The transform function returns the correct result code.

## 12.3 Graph Checks

- [ ] Required output ports exist.
- [ ] Edges reference valid components and ports.
- [ ] Required components are connected.
- [ ] Parameters and connections are defined.
- [ ] No removed component leaves an orphaned edge.

## 12.4 DGW Checks

- [ ] Only qualifying Required fields are added.
- [ ] Existing fields are not duplicated.
- [ ] Output column order follows the DGW or approved target order.
- [ ] Source fields are mapped correctly.
- [ ] Unresolved fields are listed.
- [ ] No sensitive value is invented or exposed.

# 13. Common Tasks

## Task A: Optimize an Existing Clover Graph

**Goal:** Improve execution performance while preserving output behavior.

- Supply complete graph XML.
- Provide counts, runtime, and component timings.
- State known bottlenecks.
- Review redundancy and mapping complexity.
- Validate the rewritten graph.
- Run with masked or approved test data.
- Compare counts, field-level results, runtime, and memory.

## Task B: Add DGW-Required Fields

**Goal:** Add newly required fields to the target output.

- Supply the DGW and complete graph XML.
- Identify the output writer or output-producing component.
- Compare DGW fields with output metadata.
- Review missing Required fields and source mappings.
- Validate rewritten metadata and CTL2.
- Execute and verify output order.

## Task C: Correct a CTL1 Mapping

**Goal:** Convert an unsupported transformation to CTL2.

- Supply complete graph XML and CTL1 validation error.
- Identify the failing component.
- Review CTL2 logic and metadata fields.
- Validate the graph.
- Compare transformed results with expected behavior.

## Task D: Diagnose an Execution Error

**Goal:** Identify a graph failure and apply a correction.

- Supply sanitized log, complete graph XML, and Clover version.
- Provide counts and failing stage.
- Review cause and proposed change.
- Validate rewritten XML.
- Execute with masked data and verify reject routing.

## Task E: Create a New Field Mapping

**Goal:** Map source field ABC to target field ABCD.

- Add ABCD to output metadata.
- Open the CTL2 component producing the output.
- Add the direct assignment.
- Validate type compatibility.
- Execute and confirm the target value.

$out.0.ABCD = $in.0.ABC;

# 14. Troubleshooting

## Problem: CTL1 Is Not Supported

**Error or symptom:** CTL1 is not supported language any more, please convert to CTL2.

**Cause:** The component uses CTL1 or obsolete syntax.

**Solution:**

- Set the transformation language to CTL2.
- Use $in.0 and $out.0 references.
- Use a valid transform() function where required.
- Verify metadata and revalidate.

## Problem: At Least One Output Port Must Be Defined

**Error or symptom:** At least 1 output port must be defined.

**Cause:** The component has no valid outgoing edge, output metadata is missing, or the XML does not define the required port.

**Solution:**

- Add or restore an outgoing edge.
- Assign output metadata.
- Confirm supported port and valid port numbers.
- Remove stale references and revalidate.

## Problem: Graph Configuration Is Invalid

**Error or symptom:** Graph configuration is invalid.

**Cause:** Typical causes include missing metadata, invalid properties, broken edges, duplicate IDs, absent parameters, unsupported language, malformed mapping, or missing ports.

**Solution:**

- Validate XML structure.
- Confirm metadata and unique component IDs.
- Check edges, ports, parameters, and connections.
- Convert CTL1 to CTL2.
- Open and validate in the target version.

## Problem: XML Processing Instruction Is Not Allowed

**Error or symptom:** The processing instruction target matching "\[xX\]\[mM\]\[lL\]" is not allowed.

**Cause:** An XML declaration was placed inside a mapping value.

**Solution:**

- Remove the nested XML declaration.
- Keep one declaration at the top of the graph.
- Store only CTL2 in the mapping.
- Escape XML-special characters and revalidate.

## Problem: Output Field Is Missing

**Error or symptom:** $out.0.\<field\> does not exist, or the expected output column is absent.

**Cause:** The output metadata or writer does not contain the target field.

**Solution:**

- Add the field to output-port metadata.
- Confirm spelling and case.
- Ensure the writer uses updated metadata.
- Add the mapping and verify output emission.

## Problem: Input Field Is Missing

**Error or symptom:** $in.0.\<field\> cannot be resolved.

**Cause:** The source field is absent, named differently, or connected on another port.

**Solution:**

- Confirm field and port.
- Check approved source-to-target mapping.
- Update metadata or mapping.
- Do not invent a source field or employee value.

## Problem: Data-Type Mismatch

**Error or symptom:** String-to-date, invalid number, compilation, or nullability failure.

**Cause:** The source and target types or formats are incompatible.

**Solution:**

- Compare types and formats.
- Apply explicit CTL2 conversion.
- Validate values before conversion.
- Route invalid records to reject output.
- Use approved defaults only.

## Problem: Graph Runs Slowly

**Error or symptom:** Runtime or throughput is below the baseline.

**Cause:** Potential factors include volume, sorting, joins, lookups, repeated parsing, temporary files, reject volume, or memory pressure.

**Solution:**

- Filter early and remove unused fields.
- Consolidate compatible mappings.
- Avoid duplicate reads and unnecessary sorting.
- Restructure stable lookups where supported.
- Separate reject processing.
- Measure after each material change.

# 15. Frequently Asked Questions

**Q: Can the agent optimize a graph without complete XML?**

> **A:** It can review a mapping or error, but it cannot reliably return a complete executable graph without the graph structure, metadata, components, ports, and edges.

**Q: Will it add every DGW column?**

> **A:** No. By default, it adds only newly identified fields marked Required and maps them from approved source fields.

**Q: Can it invent a default for a missing required source?**

> **A:** No. A default is used only when approved by the DGW or project rule.

**Q: Does it support CTL1?**

> **A:** No. Transformation code is rewritten in CTL2.

**Q: Why use $in.0 and $out.0?**

> **A:** They explicitly identify the input and output ports used by the mapping and align with the required convention.

**Q: Can it use local variables?**

> **A:** The standard rule prohibits var declarations. Direct assignments should be used wherever practical.

**Q: Does optimization guarantee faster execution?**

> **A:** No. Validate with runtime, throughput, component duration, memory, and correctness comparisons.

**Q: Can production employee data be supplied?**

> **A:** No. Use schemas, aggregate counts, masked samples, and sanitized logs.

**Q: What should be supplied for a validation failure?**

> **A:** Complete sanitized error, component name and ID, full graph XML, Clover version, and associated metadata.

**Q: Why return complete XML?**

> **A:** It preserves ordering, metadata references, component dependencies, ports, and edges and reduces manual merge errors.

# 16. Security and Data-Handling Guidelines

## 16.1 Mandatory Rules

- Treat Workday conversion data as sensitive.
- Do not submit raw employee PII.
- Mask sample values.
- Remove credentials and tokens from XML and logs.
- Use aggregate counts where possible.
- Keep project files in approved repositories.
- Do not place production data in public AI services or repositories.
- Follow project-specific retention and access controls.
- Review generated changes before production execution.

## 16.2 Recommended Masking

| **Sensitive value** | **Masked example**          |
|---------------------|-----------------------------|
| Employee ID         | EMP-XXXXX                   |
| Name                | Employee_A                  |
| Email               | masked.user@example.invalid |
| Phone               | XXX-XXX-1234                |
| Bank account        | XXXXXXXX1234                |
| Government ID       | XXX-XX-1234                 |

# 17. Getting Support

- Capture the complete sanitized error message.
- Note graph name, component name, and component ID.
- Record Clover version and the operation performed.
- Record input, output, and rejected counts.
- Capture component-level execution measurements.
- Provide complete graph XML without credentials or raw employee data.
- Include relevant DGW definitions for missing-column issues.
- Share masked screenshots only through an approved project channel.
- Contact the project technical lead or CloverDX administrator.
