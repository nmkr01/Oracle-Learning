Excellent! We've completed the Oracle ERP fundamentals and integration patterns. The next logical topic is one that almost every OIC project uses.

📘 Day 8 – Oracle BI Publisher (BIP) Reports in Oracle ERP Cloud

This is one of the most frequently used features in Oracle ERP + OIC integrations.

---

🎯 Learning Objectives

By the end of Day 8, you'll understand:

    * What BI Publisher (BIP) is.
    * Why BIP Reports are used.
    * Data Models vs Reports.
    * How OIC invokes BIP Reports.
    * Report Parameters.
    * Synchronous vs Asynchronous execution.
    * Downloading report output.
    * Real project scenarios.

---

Why Learn BIP?
Oracle ERP stores business data in thousands of tables.
Direct database access isn't available in Oracle Fusion SaaS.
Oracle provides BI Publisher Reports to expose data securely.

    Examples:

    * AP Invoices
    * GL Journals
    * Suppliers
    * Customers
    * Purchase Orders
    * Payments

---

What is BI Publisher?
BI Publisher is Oracle's reporting tool used to generate reports from Oracle ERP Cloud data.
    A report can output:

    * XML
    * CSV
    * Excel
    * PDF
    * HTML

---

BI Publisher Architecture

        Oracle ERP Database
                │
                ▼
        SQL Query (Data Model)
                │
                ▼
        BI Publisher Report
                │
                ▼
        Report Output
        (XML / CSV / PDF / Excel)
                │
                ▼
        Oracle Integration Cloud

---
Components of BI Publisher

1. Data Model
    Contains:
        * SQL Query
        * Parameters
        * Data Sets

Example:
            sql
                    SELECT invoice_num,
                        invoice_amount,
                        supplier_name
                    FROM ap_invoices_all
                    WHERE invoice_date >= :P_FROM_DATE

---

2. Report Layout
        Defines:

            * PDF
            * Excel
            * HTML
            * RTF
        The same Data Model can have multiple layouts.

---

3. Report
    Combines:

        * Data Model
        * Layout
        * Parameters
---

OIC Flow Using BI Publisher

            Scheduled Integration
                    │
                    ▼
            ERP Cloud Adapter
            Run Report
                    │
                    ▼
            ERP Executes Report
                    │
                    ▼
            Returns Report Job ID
                    │
                    ▼
            Wait
                    │
                    ▼
            Get Report Status
                    │
                    ▼
            Download Report
                    │
                    ▼
            Stage File
                    │
                    ▼
            Process Records
                    │
                    ▼
            Send to External System

---

Report Parameters
        Example     :    Invoice Report

        Parameters
                    P_FROM_DATE
                    P_TO_DATE
                    P_BUSINESS_UNIT

        OIC passes these values dynamically.

---

Synchronous Execution
    Small reports

        OIC
        │
        ▼
        Run Report
        │
        ▼
        Report Generated
        │
        ▼
        Receive Output


Suitable for:

    * Small datasets
    * Fast reports
---

Asynchronous Execution
    Large reports

        Run Report
            │
            ▼
        Job Submitted
            │
            ▼
        Report Job ID
            │
            ▼
        Wait
            │
            ▼
        Check Status
            │
            ▼
        Completed?
            │
            ▼
        Download Output
---

Real Project Example 1

            Supplier Master Integration
            Requirement:

            Every night,
            Send all new suppliers to SAP.

            Flow

                    Scheduled Integration
                            │
                            ▼
                    Run Supplier Report
                            │
                            ▼
                    Download XML
                            │
                            ▼
                    Stage File
                            │
                            ▼
                    Mapper
                            │
                            ▼
                    SAP REST API

---

Real Project Example 2
Invoice Synchronization

        Scheduled Trigger
                │
                ▼
        Run AP Invoice Report
                │
                ▼
        Download XML
                │
                ▼
        Read Records
                │
                ▼
        For Each Invoice
                │
                ▼
        Call External API

---

OIC ERP Adapter Operations
    Frequently used BI Publisher operations:

        * Run Report
        * Get Report Status
        * Download Report Output

---

Common Report Formats

        | Format | Usage             |
        | ------ | ----------------- |
        | XML    | OIC Integrations  |
        | CSV    | Data Processing   |
        | Excel  | Business Users    |
        | PDF    | Printable Reports |
        | HTML   | Browser Viewing   |

---

Best Practices

        * Use parameters to limit data volume.
        * Prefer XML for integrations.
        * Use asynchronous execution for large reports.
        * Avoid returning unnecessary columns.
        * Schedule reports during off-peak hours.
        * Handle report failures and timeouts gracefully.

---

Interview Questions

        1. What is BI Publisher?
        2. Why do we use BIP instead of querying ERP tables directly?
        3. What is the difference between a Data Model and a Report?
        4. How does OIC execute a BI Publisher report?
        5. When should you use synchronous vs asynchronous execution?
        6. What is the purpose of report parameters?
        7. Which report format is preferred for OIC integrations?
        8. How do you download report output in OIC?
        9. What happens if a report takes too long to complete?
        10. Explain a real-time project where you used BI Publisher.

---

Assignment
Design an integration for this requirement:

Every day at 11 PM:

        * Run an AP Invoice BI Publisher report.
        * Pass From Date and To Date.
        * Wait for completion.
        * Download XML.
        * Process invoices.
        * Send them to SAP.
        * Log failures and send an email notification if any invoice processing fails.

---

Quiz
        1. Why is BI Publisher required in Oracle ERP Cloud?
        2. What is the difference between a Data Model and a Report?
        3. Explain the end-to-end OIC flow for executing a BI Publisher report.
        4. Why are report parameters important?
        5. When would you choose asynchronous report execution instead of synchronous execution?

---

🚀 What's Coming Next

        Day 9 – Oracle ERP REST APIs
        You'll learn:
            * Oracle ERP REST resources
            * CRUD operations
            * Authentication (OAuth)
            * Pagination
            * Query parameters (q, fields, expand)
            * Attachments
            * Real project integrations

This sequence (Business Events → BI Publisher → REST APIs) mirrors how Oracle ERP integrations are commonly built in enterprise OIC projects.

---------------------------------------------------

Absolutely. Since we're following an Oracle Integration Architect roadmap, you should know how to create a BI Publisher (BIP) report from scratch in Oracle ERP Cloud, not just how to invoke it from OIC.

---

Step 1: Login to Oracle ERP Cloud
Open your Oracle ERP URL and sign in.
---

Step 2: Navigate to BI Publisher
There are two common navigation paths depending on your ERP version.

    Method 1 (Most Common)

    Home
    ↓
    Tools
    ↓
    Reports and Analytics
You will see the Reports and Analytics dashboard.
---

Method 2 (Direct BI Catalog)

    Navigator
        ↓
    Tools
        ↓
    Reports and Analytics
        ↓
    Browse Catalog
The Browse Catalog option opens the BI Publisher Catalog where custom reports are created and managed.
---

Step 3: Open the BI Catalog

    Reports and Analytics
            │
            ▼
    Browse Catalog
You'll see folders such as:

        Shared Folders
        My Folders
        Catalog
---

Step 4: Create a Data Model
Navigate to:

    Shared Folders
            │
            ▼
    Custom
            │
            ▼
    New
            │
            ▼
    Data Model
If Custom doesn't exist, create a folder where your organization stores custom reports.
---

Step 5: Create a SQL Data Set
Inside the Data Model:

    Data Sets
        │
        ▼
    New Data Set
        │
        ▼
    SQL Query

Example SQL:

    SELECT
        invoice_num,
        invoice_amount,
        vendor_name
    FROM
        ap_invoices_all
    WHERE
        invoice_date >= :P_FROM_DATE
---

Step 6: Create Parameters
Click:

    Parameters
        │
        ▼
    New Parameter

Example:
    P_FROM_DATE

    Datatype:
    Date

    Required:
    Yes

    Similarly create:

    P_TO_DATE
    P_BUSINESS_UNIT
---

Step 7: Save the Data Model

Example:
    Shared Folders
    Custom
    Training
    DM_AP_Invoice
---

Step 8: Create Report

    New
        │
        ▼
    Report

Choose:
    Use Existing Data Model
    ↓
    DM_AP_Invoice
---

Step 9: Choose Layout
    Possible layouts:
        * PDF
        * Excel
        * HTML
        * RTF

For OIC integrations:

    Layout
    ↓
    XML
(When using OIC, XML output is the most common because it is easy to map and process.)

---

Step 10: Save Report

Example:
    Shared Folders
    Custom
    Training
    RPT_AP_Invoice
---

Step 11: Test Report
Click
    Open
    ↓
    Run

Enter
    From Date
    To Date
    Business Unit

    Click
    Apply
    Oracle generates the report.
---

Step 12: Use Report in OIC

In OIC:

    ERP Cloud Adapter
    ↓
    Run Report
    ↓
    Select
        RPT_AP_Invoice

    Pass:

    P_FROM_DATE
    P_TO_DATE
    P_BUSINESS_UNIT

    Then:

    Run Report
        │
        ▼
    Download XML
        │
        ▼
    Stage File
        │
        ▼
    Mapper
        │
        ▼
    SAP


---

Complete Navigation Flow

    Home
    │
    ▼
    Tools
    │
    ▼
    Reports and Analytics
    │
    ▼
    Browse Catalog
    │
    ▼
    Shared Folders
    │
    ▼
    Custom
    │
    ▼
    New
    │
    ├── Data Model
    │       │
    │       ├── SQL Query
    │       ├── Parameters
    │       └── Save
    │
    └── Report
            │
            ├── Select Data Model
            ├── Choose Layout
            └── Save
---

Required Roles

If you don't see Browse Catalog or New → Data Model, you likely don't have the required permissions.
Common roles include:

    * BI Administrator
    * BI Author
    * BI Consumer (view only)
    * Application Implementation Consultant (varies by module and security setup)

    Your Oracle ERP administrator may need to grant BI Publisher authoring privileges.
---

📘 Day 8 Practical Exercise

Create a report named   :  RPT_AP_Invoice_Training

Using:

    * Data Model: DM_AP_Invoice_Training
    * Parameters:
        P_FROM_DATE
        P_TO_DATE
    
    Output: XML
    Save Location: Shared Folders Custom Training

Once you're comfortable with this, we can move to the next level: writing SQL queries against Oracle Fusion tables, creating Data Models with joins, and building production-ready BIP reports that are commonly used in OIC projects.

-------------------------------
Ques : Senior-Level Interview Question
       Let's move one step beyond theory.

        Scenario

        A BI Publisher report returns 100,000 AP invoices in XML format.

        The business requirement is:

        Send invoices to SAP in batches.
        If one invoice fails, continue processing the remaining invoices.
        At the end, send a summary email showing:
        Total invoices processed
        Successful invoices
        Failed invoices
        Archive the report output for audit purposes.
        Question

        How would you design this integration in Oracle Integration Cloud?

        Think about:

        ERP Cloud Adapter
        Stage File
        Read Records
        For Each
        Scope
        Fault Handler
        Variables (success/failure counters)
        Email notification
        File archiving

    This is the type of design question often asked in senior OIC technical interviews. 

Ans : For a large BI Publisher report, I would execute the report asynchronously and download the XML output. Using the Stage File action, I'd read the file in segments to avoid memory issues. Inside a For Each loop, I'd wrap the SAP invocation in a Scope with a Fault Handler so that a failure for one invoice doesn't stop the integration. I'd maintain success and failure counters using integration variables, archive the original report after processing, log failed invoices with error details, and send a summary email containing the total, successful, and failed record counts.

            Scheduled Trigger
                    │
                    ▼
            Run BI Publisher Report
                    │
                    ▼
            Download XML
                    │
                    ▼
            Stage File (Read in Segments)
                    │
                    ▼
            For Each Invoice
                    │
                    ▼
            Scope
                    │
            ┌────┴────┐
            │         │
            Success   Fault Handler
            │         │
            Increment  Log Error
            Success    Increment Failure
            Counter    Counter
            └────┬────┘
                    ▼
            Continue Next Record
                    │
                    ▼
            Archive Original Report
                    │
                    ▼
            Generate Summary
                    │
                    ▼
            Email Notification
