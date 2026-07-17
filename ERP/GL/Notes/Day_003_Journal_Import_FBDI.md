 🌅 Day 3 – Oracle ERP Journal Import (FBDI) & OIC Integration

Today you'll learn how journals are loaded into Oracle ERP from external systems, which is one of the most common integration scenarios in Oracle projects.

---

 📅 Day 3 Agenda

| Time   | Topic                              |
| ------ | ---------------------------------- |
| 20 min | What is Journal Import?            |
| 20 min | Manual Journal vs Imported Journal |
| 30 min | FBDI Architecture                  |
| 25 min | FBDI File Structure                |
| 20 min | UCM Upload                         |
| 20 min | ESS Jobs                           |
| 30 min | OIC Integration Flow               |
| 20 min | REST APIs                          |
| 15 min | Assignment                         |
| 15 min | Quiz                               |

---

 1. Why Journal Import?
    Imagine your company has:
        * SAP Payroll
        * Banking System
        * Legacy ERP
        * Third-party Finance Application

Every day thousands of accounting entries are generated.
Creating them manually is impossible.
Instead,
they are imported.
---

 Business Flow

    External System
            │
            ▼
        CSV File
            │
            ▼
        Oracle ERP
            │
            ▼
    Journal Import
            │
            ▼
    Journal Created
            │
            ▼
    Journal Posted

---
 Definition

Journal Import is the Oracle process that imports accounting journals from external systems into Oracle General Ledger.

---

 Manual Journal vs Imported Journal

| Manual Journal     | Imported Journal            |
| ------------------ | --------------------------- |
| Created by User    | Created by System           |
| Small Volume       | Large Volume                |
| UI Screen          | CSV / API                   |
| Slow               | Fast                        |
| Mostly Adjustments | Daily Business Transactions |

---

 Real Client Example

Company uses:

                SAP Payroll
                Every month
                50,000 salary records
                ↓
                OIC
                ↓
                Oracle ERP
                ↓
                Journal Import
                ↓
                GL

Nobody enters 50,000 journals manually.

---

 2. What is FBDI?
        FBDI stands for
        File Based Data Import
        Oracle provides Excel templates.
        You fill them.
        Generate CSV.
        Zip them.
        Upload.
        Import.

---

Think:
        Excel
        ↓
        CSV
        ↓
        ZIP
        ↓
        Oracle ERP
        ↓
        Journal

---

FBDI Flow

                FBDI Excel
                        │
                Generate CSV
                        │
                ZIP File
                        │
                Upload to UCM
                        │
                Run ESS Job
                        │
                Import Journals
                        │
                Post Journals

---

 3. FBDI File Structure
Typical Journal Import ZIP

        JournalImport.zip
        │
        ├── JournalBatch.csv
        ├── JournalHeader.csv
        └── JournalLine.csv

        ---
        Journal Batch
        Groups journals together.

        Example
            Payroll Batch
            Month End Batch
            Adjustment Batch
        ---

        Journal Header
        Contains
            * Ledger
            * Currency
            * Period
            * Source
            * Category

        ---

        Journal Line
        Contains
            * Account Combination
            * Debit
            * Credit
            * Description
---

Complete Flow
    Load Interface File for Import
            * Import Journals
            * Post Journals
---

Flow
            Upload ZIP
            ↓
            ESS Job
            ↓
            Journal Import
            ↓
            Journal Created
            ↓

 6. Complete OIC Flow

This is a real implementation scenario.
            Payroll System
                    │
                    ▼
            Oracle Integration Cloud
                    │
                    ▼
            Read Payroll Data
                    │
                    ▼
            Mapping
                    │
                    ▼
            Generate FBDI CSV
                    │
                    ▼
            ZIP File
                    │
                    ▼
            Upload to UCM
                    │
                    ▼
            Run ESS Job
                    │
                    ▼
            Import Journals
                    │
                    ▼
            Post Journals
                    │
                    ▼
            General Ledger
---

        OIC Components Used
            * Scheduled Integration / REST Trigger
            * Stage File
            * File Zip
            * ERP Adapter
            * UCM Upload
            * Submit ESS Job
            * Monitor ESS Job
            * Log Result

---

            ERP REST APIs

                Some useful APIs:
                Upload File
                Handled using the ERP Adapter/UCM upload operation.
                Submit ESS Job

                Runs:
                    * Load Interface File
                    * Import Journals
                    * Post Journals

---

        Why Companies Prefer FBDI
                * Fast
                * Bulk Upload
                * Easy to Validate
                * Reliable
                * Oracle Recommended
                * Used in Almost Every ERP Project
---

Real Project Scenario

            Company: ABC Manufacturing

            Every month:
            Payroll
            ↓
            SAP
            ↓
            CSV
            ↓
            OIC
            ↓
            FBDI
            ↓
            Oracle ERP
            ↓
            Import Journal
            ↓
            Post Journal
            ↓
            Financial Reports

            This is a very common interview scenario.

---

Interview Questions

    1. What is FBDI?
    2. Why do companies use Journal Import?
    3. Difference between Manual and Imported Journals?
    4. What is UCM?
    5. What is ESS?
    6. What is inside a Journal Import ZIP?
    7. Explain the Journal Import process.
    8. How does OIC upload Journals?
    9. Which adapter is used?
    10. What happens after uploading the ZIP?

---

Quiz

Answer these without searching.
1. What is Journal Import?
2. What is FBDI?
3. What is stored inside the Journal Import ZIP file?
4. What is UCM used for?
5. What is the role of ESS Jobs?
---

Key Takeaways
---
* Journal Import → Imports journals from external systems into Oracle GL.
* FBDI → File-Based Data Import using Excel → CSV → ZIP.
* UCM → Stores uploaded ZIP files.
* ESS → Executes background jobs like Import Journals and Post Journals.
* OIC → Automates the complete process from external system to Oracle GL.