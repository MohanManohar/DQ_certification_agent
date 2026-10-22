# Purchase Order Data Quality Agent — Failure Investigation Tests

## 1. Objective

The objective of these tests is to validate that the Manufacturing Purchase Order Data Quality Agent can retrieve actual data quality failures, explain them in business language, recommend corrective actions, and follow read-only guardrails.

The agent is built using Databricks AI/ML Playground and Unity Catalog functions.

## 2. Environment

- **Platform:** Databricks Free Edition
- **Project:** DQ_certification_agent
- **Git branch:** `dev`
- **Architecture:** Medallion Architecture
- **Data domain:** Manufacturing Purchase Orders and Suppliers
- **Failure source:** `workspace.silver.po_exceptions`
- **Summary tool:** `workspace.gold.get_dq_summary`
- **Failure investigation tool:** `workspace.gold.get_dq_failures`

## 3. Test Scope

The tests cover:

- Retrieval of actual failed purchase-order records.
- Identification of failed data quality and business rules.
- Explanation of failures in business language.
- Recommendations for corrective actions.
- Separation of verified facts from unknown information.
- Prevention of unauthorized data modifications.

## 4. Test Cases and Results

### Test 1: Retrieve All Failed Records

**Prompt:** List all failed purchase-order records with PO ID, source row ID, failed rule ID, and rule description.

**Expected result:**
- Call `get_dq_failures`.
- Return records and rule details from the tool output.
- Do not invent record IDs, rules, or counts.

**Actual result:** The tool returned nine failure rows covering eight distinct source records. PO1006 failed two rules, and the duplicate PO1005 line was reported for both source rows.

**Status:** PASS

### Test 2: Explain Quantity Failure

**Prompt:** Investigate PO1004 and recommend a corrective action.

**Expected result:**
- Identify source row 4 and rule DQ04.
- Explain the positive-quantity requirement.
- Recommend verification against the original purchase order.
- Do not claim the record was corrected.

**Actual result:** The agent identified the correct PO and rule, explained the issue, and recommended verification with the procurement team.

**Status:** PASS

**Improvement identified:** Include the actual quantity in the tool output so the explanation is grounded in retrieved values.

### Test 3: Investigate Duplicate PO Lines

**Prompt:** Investigate duplicate purchase-order line failures.

**Expected result:**
- Identify PO1005 and source rows 5 and 6.
- Explain rule DQ03, which requires PO ID and line number to form a unique key.
- Recommend source verification before any correction.

**Actual result:** The agent identified both records, explained the uniqueness violation, and recommended reviewing the original purchase order.

**Status:** PASS

**Improvement identified:** Verify the root cause before recommending merging, deleting, or modifying records.

### Test 4: Investigate Invalid Supplier Reference

**Prompt:** Investigate the supplier reference failure for PO1003.

**Expected result:**
- Identify supplier ID