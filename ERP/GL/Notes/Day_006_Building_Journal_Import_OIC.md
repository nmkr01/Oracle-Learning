📘 Day 6 – Building a Journal Import Integration in Oracle Integration Cloud

Today we'll move from the business flow to the actual OIC implementation. By the end of today, you'll know which OIC activities to drag into the canvas, why they're used, and how they connect.

---

🎯 Learning Objectives

* Design an OIC Scheduled Integration.
* Configure the FTP Adapter.
* Use Stage File to read and write CSV files.
* Create the FBDI ZIP package.
* Upload to UCM.
* Submit the ESS Job.
* Implement polling.
* Add fault handling.

---

Complete OIC Flow


            Scheduled Trigger
                    │
                    ▼
            Read Payroll File (FTP Adapter)
                    │
                    ▼
            Stage File (Read File)
                    │
                    ▼
            For Each Record
                    │
                    ▼
            Validate Data
                    │
            ┌───────────────┐
            │ Valid?        │
            └───────────────┘
                │Yes             │No
                ▼                ▼
            Write FBDI CSV     Log Error
                │                │
                └──────┬─────────┘
                        ▼
            Generate ZIP
                        ▼
            Upload to UCM
                        ▼
            Submit ESS Job
                        ▼
            Get Request ID
                        ▼
            Wait (30 sec)
                        ▼
            Get Job Status
                        ▼
            Completed?
                │Yes           │No
                ▼              ▼
            Download Report   Wait Again
                ▼
            Success Email


---

Step 1 – Scheduled Trigger

Pattern:

Scheduled Orchestration

Example Schedule:


11:00 PM
Every Day


Why?

* Payroll file arrives daily.
* No external system needs to trigger OIC.

---

Step 2 – FTP Adapter

Operation:

Read File

Example:


/payroll/Payroll_20260722.csv


Output:

CSV file.

---

Step 3 – Stage File

Action:

Read Entire File

Then:

Read Records

Configuration:

* Delimiter = Comma
* Header = Yes
* Character Set = UTF-8

Output Example:


Ledger
Account
Debit
Credit
Description


---

Step 4 – For Each

Loop through each payroll record.

Example:


Employee 1
Employee 2
Employee 3
...
Employee 100000


Each iteration processes one journal line.

---

Step 5 – Validation

Before writing to the FBDI file, validate:

* Ledger exists.
* Account is valid.
* Debit/Credit values are correct.
* Mandatory fields are populated.

Use a Switch action if needed.

---

Step 6 – Write FBDI CSV Files

Use Stage File → Write File.

Generate:


JournalBatch.csv
JournalHeader.csv
JournalLine.csv


These files are required for Journal Import.

---

Step 7 – ZIP the Files

Use Stage File → Zip File.

Output:


JournalImport.zip


---

Step 8 – Upload to UCM

Use Oracle ERP Cloud Adapter.

Operation:

Upload File to UCM

Input:


JournalImport.zip


Output:

File stored in Oracle UCM.

---

Step 9 – Submit ESS Job

ERP Adapter operation:

Submit ESS Job

Typical sequence:

1. Load Interface File for Import
2. Import Journals
3. Post Journals

Save the returned Request ID.

---

Step 10 – Poll the ESS Job

Use this pattern:


Submit ESS Job
      │
      ▼
Request ID
      │
      ▼
Wait (30 sec)
      │
      ▼
Get ESS Job Status
      │
      ▼
Succeeded?
 ├── Yes → Continue
 └── No  → Wait and Poll Again


---

Step 11 – Success Path

When the job succeeds:

* Download ESS output.
* Archive the input ZIP.
* Log the Request ID.
* Notify Finance.

---

Step 12 – Error Path

If any step fails:

* Capture the error message.
* Log the Request ID.
* Move the file to an error folder.
* Notify the support team.

---

Recommended Scope Structure


Main Scope
│
├── Read File
├── Parse File
├── Generate CSV
├── ZIP Files
├── Upload to UCM
├── Submit ESS
└── Poll ESS


Each scope can have its own fault handler for localized error handling.

---

Components Used

| OIC Component     | Purpose                |
| ----------------- | ---------------------- |
| Scheduled Trigger | Start integration      |
| FTP Adapter       | Read payroll file      |
| Stage File        | Read, write, zip files |
| For Each          | Process each record    |
| Switch            | Validation             |
| ERP Cloud Adapter | UCM upload and ESS     |
| Wait              | Polling delay          |
| Logger            | Debugging              |
| Scope             | Group related steps    |
| Fault Handler     | Error handling         |

---

Real Project Best Practices

* Validate data before generating FBDI files.
* Store Request IDs for troubleshooting.
* Continue processing valid records even if some fail validation.
* Archive processed files.
* Log meaningful error messages.
* Avoid hardcoding file paths or credentials.
* Use lookup tables for configurable values.

---

Interview Questions

1. Why use a For Each loop?
2. Why validate records before creating FBDI files?
3. Why use Stage File instead of Java?
4. Why do we need the Request ID?
5. Why is polling implemented with a Wait action?
6. What happens if the UCM upload fails?
7. How would you process 100,000 records efficiently?
8. Where would you implement fault handling?
9. Why archive processed files?
10. How would you make this integration reusable?

---

Assignment

Design this integration in OIC and identify:

* Trigger
* FTP Adapter
* Stage File actions
* Validation logic
* For Each
* ERP Adapter operations
* Polling loop
* Fault handling scopes

---

Quiz

1. Why do we use a For Each loop in this integration?

---

2. Why should validation happen before generating the FBDI CSV files?

---

3. Which Stage File actions are used in this integration, and at what steps?

---

4. Why is the Wait action used before checking the ESS Job status?

---

5. If the FTP file contains 10,000 records and 200 records are invalid, how would you design the integration so the valid records are still imported into Oracle ERP?

---

📁 Save Today's Notes


01_ERP
└── GL
    └── Day_006_Building_Journal_Import_OIC.md


#🎯 Today's Goal

By the end of Day 6, you should be able to confidently explain the end-to-end implementation of a Journal Import integration in OIC—from reading a file on FTP to successfully posting journals in Oracle ERP, including validation, FBDI generation, UCM upload, ESS processing, polling, and fault handling. This is a common scenario in Oracle Integration Cloud interviews and real-world implementations.
