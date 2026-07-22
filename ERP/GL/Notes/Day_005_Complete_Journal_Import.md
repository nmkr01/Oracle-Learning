Day 5 – Build a Complete Journal Import Integration in Oracle Integration Cloud (OIC)
Today you'll learn how to design a real production-grade OIC integration that imports General Ledger journals into Oracle ERP Cloud using FBDI.
This is one of the most common Oracle ERP integration scenarios.

---

 🎯 Learning Objectives

    By the end of today, you'll be able to:

    * Design a complete Journal Import integration.
    * Understand each OIC activity in the flow.
    * Upload FBDI files to UCM.
    * Submit and monitor ESS Jobs.
    * Handle success and failure scenarios.
    * Explain the complete solution in an interview.

---

Business Requirement

A company uses SAP Payroll.
Every night at 11 PM, SAP generates a CSV file with employee salary journals.

    Requirement:

    * Read the file.
    * Convert it to Oracle FBDI format.
    * Upload it to Oracle ERP.
    * Import journals.
    * Post journals.
    * Notify Finance if successful.
    * Send an error notification if failed.

---

Complete Integration Flow

        SAP Payroll
            │
            ▼
        FTP Server
            │
            ▼
        Scheduled OIC Integration
            │
            ▼
        Read CSV File
            │
            ▼
        Stage File (Read Records)
            │
            ▼
        For Each Record
            │
            ▼
        Map to FBDI Format
            │
            ▼
        Generate JournalBatch.csv
        Generate JournalHeader.csv
        Generate JournalLine.csv
            │
            ▼
        ZIP the CSV Files
            │
            ▼
        Upload ZIP to UCM (ERP Adapter)
            │
            ▼
        Submit ESS Job
            │
            ▼
        Receive Request ID
            │
            ▼
        Wait
            │
            ▼
        Poll ESS Job Status
            │
            ▼
        Completed?
        ├── Yes → Download Report → Notify Success
        └── No/Error → Notify Failure

---
Step 1 – Trigger

    Integration Pattern:
    Scheduled Orchestration

    Reason:
    The payroll file arrives every night at a fixed time.

---

 Step 2 – Read CSV
    Use:
        FTP Adapter (or Stage File if already available locally)
    Example:
        Payroll_July.csv
---

Step 3 – Stage File
        Operations:

        * Read Entire File
        * Read Records
        * Parse CSV

Output:
Each journal record becomes available for mapping.

---

Step 4 – Mapping

Map the source fields to Oracle FBDI.
Example:

| Source      | Oracle FBDI      |
| ----------- | ---------------- |
| Ledger      | Ledger Name      |
| Account     | Code Combination |
| Debit       | Entered Debit    |
| Credit      | Entered Credit   |
| Description | Description      |

---

Step 5 – Generate FBDI Files

    Create:
        JournalBatch.csv
        JournalHeader.csv
        JournalLine.csv

These three files make up the Journal Import package.
---

Step 6 – ZIP File

    Use:
    Stage File → Zip File
    Output:
    JournalImport.zip
---

Step 7 – Upload to UCM

    ERP Adapter Operation:
    Upload File to UCM

    Result:
        JournalImport.zip
        ↓
        UCM
---

Step 8 – Submit ESS Job

ERP Adapter Operation:
Submit ESS Job

    Example sequence:

    1. Load Interface File
    2. Import Journals
    3. Post Journals

---

Step 9 – Receive Request ID
    Example:

    Request ID
    ↓
    45896321

Store it in an integration variable for tracking.

---

Step 10 – Polling
    Request ID
    ↓
    Get ESS Job Status
    ↓
    Running?
    ↓
    Yes
    ↓
    Wait 30 Seconds
    ↓
    Check Again


Repeat until the job finishes.

---

Step 11 – Success
    If status = Succeeded

    * Download ESS Output
    * Log Request ID
    * Send Email to Finance
    * Archive the file

---

Step 12 – Failure
    If status = Error

    * Download Error Report
    * Capture Request ID
    * Send Email to Support Team
    * Move file to Error Folder

---

OIC Components Used

| Component         | Purpose                 |
| ----------------- | ----------------------- |
| Scheduled Trigger | Runs nightly            |
| FTP Adapter       | Reads CSV               |
| Stage File        | Reads & creates files   |
| Mapper            | Maps fields             |
| ERP Cloud Adapter | UCM Upload & ESS        |
| Switch            | Success/Failure         |
| Wait              | Polling delay           |
| Notification      | Email alerts            |
| Logger            | Audit & troubleshooting |

---

Error Handling
    Possible errors:

    * CSV format invalid
    * Missing Ledger
    * Invalid Account Combination
    * UCM Upload Failed
    * ESS Job Failed
    * Journal Import Errors

Handle them using:

    * Scope
    * Global Fault Handler
    * Notifications
    * Logging

---

Real Project Architecture

        SAP Payroll
            │
            ▼
        FTP Server
            │
            ▼
        Oracle Integration Cloud
            │
        ┌───────────────┐
        │ Stage File    │
        │ Mapper        │
        │ ZIP           │
        │ ERP Adapter   │
        └───────────────┘
            │
            ▼
        Oracle ERP Cloud
            │
            ▼
        UCM
            │
            ▼
        ESS Jobs
            │
            ▼
        Oracle General Ledger

---

Interview Questions

    1. Why did you choose a Scheduled Integration?
    2. Why use Stage File?
    3. Why are three CSV files generated?
    4. What is stored in UCM?
    5. Why do we submit ESS Jobs?
    6. What is the purpose of the Request ID?
    7. Why do we poll the ESS Job?
    8. How do you handle failures?
    9. What adapter uploads files to UCM?
    10. Explain the complete Journal Import process.

---

Assignment

Design this integration in OIC.
Draw the flow and identify:

    * Trigger
    * Adapters
    * Stage File actions
    * ERP Adapter operations
    * Polling logic
    * Error handling

---
Quiz
Answer these without searching.

    1. Why is a Scheduled Integration preferred for this scenario?
    2. What is the purpose of the Stage File action?
    3. Why do we create `JournalBatch.csv`, `JournalHeader.csv`, and `JournalLine.csv`?
    4. What information should you save after submitting the ESS Job?
    5. What should happen if the ESS Job fails?



Tomorrow (Day 6), we'll move from theory to implementation by designing this integration step-by-step using actual OIC activities, including Stage File configuration, mapper logic, ERP Adapter configuration, polling loop, and fault handling. This will closely resemble what you build in a real Oracle Integration Cloud project.
