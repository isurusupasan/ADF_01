# ADF_01

#This will repo will evalute the linkedin job posting data via Azure Data 

# Azure Data Factory Pipeline: Process_Listed_Items_Pipeline

## Overview
This pipeline demonstrates the use of **Lookup**, **ForEach**, **If Condition**, and **Data Flow** activities
to process a dynamic list of files or business keys in Azure Data Factory.
It extends the Week-5 workshop patterns (Filter → Aggregate → Sink; Select/Split; Join) with iteration and conditional logic.

---

## 1) Create the pipeline and enable debug
1. In ADF Studio → Orchestrate → New pipeline → name it **Process_Listed_Items_Pipeline**.
2. Toggle **Data Flow debug** ON to speed up interactive testing.

---

## 2) Prepare (or reuse) datasets and linked services
- **SourceBlobDataset**: CSV in Blob Storage (from Week-5 activity).
- **AzureSqlDatabase2**: Linked service pointing to **workshopDB** in Azure SQL.

Optional: Create a small CSV (file_list.csv) or SQL table listing items to process.

---

## 3) Parameterize your Data Flow
Open **TransformSalaries_df** and add parameters:
- `pFileName` (string)
- `pPayPeriod` (string, optional)
- `pOutTable` (string, optional)

Bind dataset paths and filters to these parameters.
Keep Filter, Aggregate, and Sink logic from Week-5.

---

## 4) Add a Lookup activity
- Name: **lkp_FileList**
- Source: CSV or SQL query returning a list of items.
- Set **First row only** = False.

---

## 5) Add a ForEach activity
- Name: **fe_EachFile**
- Items: `@activity('lkp_FileList').output.value`
- Optionally set **Sequential** = True for debugging.

---

## 6) Inside ForEach: Add an If Condition
- Name: **if_ProcessThis**
- Expression (example): `@endswith(item().FileName, '.csv')`

---

## 7) If True branch: Run the Data Flow
- Name: **df_RunTransform**
- Data Flow: **TransformSalaries_df**
- Parameters:
  - `pFileName`: `@item().FileName`
  - `pPayPeriod`: `''`
  - `pOutTable`: `'Total_Max_salary'`

---

## 8) Optional post-processing
Add Stored Procedure or Copy activity to finalize or audit results.

---

## 9) Validate and run
1. Validate the pipeline.
2. Debug run with a small item list.
3. Monitor activity outputs and verify tables in **workshopDB**.

---

## 10) Variations
- Iterate by pay period using a SQL Lookup (`SELECT DISTINCT pay_period ...`).
- List files dynamically using Get Metadata → ForEach.
- Run Job/Salary split logic per file inside the loop.

---

## 11) Success checklist
- Lookup returns an array.
- ForEach iterates expected count.
- If Condition filters correctly.
- Data Flow writes to the expected table.
- Successful runs visible in Monitor, verified in SQL.

---
