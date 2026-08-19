This is one of the **most frequently asked Oracle Integration Cloud interview questions**.

# Different Error Handling Mechanisms in Oracle Integration Cloud (OIC)

OIC provides multiple ways to handle errors depending on the type of integration and business requirement.

---

# 1. Global Fault Handler ⭐⭐⭐⭐⭐

## What is it?

A **Global Fault Handler** catches any unhandled exceptions that occur anywhere in the integration.

### Architecture

        Integration
            │
        Invoke ERP
            │
        Invoke FTP
            │
        Invoke DB
            │
        Any Error
            ▼
        Global Fault Handler
            │
        Log Error
            ↓
        Send Email
            ↓
        Re-throw Fault


### Use Case

* Send notification emails
* Log errors
* Move files to an error folder
* Update an error table

---

# 2. Scope Fault Handler ⭐⭐⭐⭐⭐

Handles errors only within a specific **Scope**.

    Scope
    │
    Invoke SAP
    │
    Invoke ERP
    │
    Error
    ▼
    Scope Fault Handler


### Example

If SAP fails,

handle only SAP-related errors without affecting other scopes.

---

# 3. Throw New Fault

Creates a **custom business exception**.

Example:

    Invoice Amount > 1,000,000
        ↓
    Throw New Fault
        ↓
    "Invoice amount exceeds business limit."


Used for:

* Business validations
* Mandatory field validation
* Duplicate record validation

---

# 4. Re-throw Fault

Used after handling an exception.

Example

    ERP Error
        ↓
    Log Error
        ↓
    Email
        ↓
    Re-throw Fault
        ↓
    Integration Failed

The original exception is preserved.

---

# 5. Stop Action

Stops integration execution.

    Error
        ↓
    Stop


Use when you don't want to continue processing.

---

# 6. Switch-Based Error Handling

Validate data before processing.

    Employee ID
        ↓
    Null?
        ↓
    Yes
        ↓
    Throw New Fault
        ↓
    No
        ↓
    Continue


Avoids unnecessary service calls.

---

# 7. Retry Mechanism

Retry transient failures.

Example

text id="qor2l6"
    REST API
        ↓
    Timeout
        ↓
    Retry 3 Times
        ↓
    Still Failed?
        ↓
    Error Handler


Useful for:

* Network issues
* Temporary service outages

---

# 8. Database Error Logging

Instead of losing errors,

store them.

    Error
        ↓
    Insert into ERROR_LOG Table
        ↓
    Error Details Stored


Typical columns:

* Integration Name
* Error Code
* Error Message
* Payload
* Timestamp

---

# 9. File Error Handling

If processing a CSV fails:

    Input Folder
        ↓
    Processing Failed
        ↓
    Move File
        ↓
    Error Folder


Example folders:

    /Inbound
    /Archive
    /Error


---

# 10. Email Notification

Notify support team automatically.

    Error
        ↓
    Send Email
        ↓
    Support Team


Include:

* Integration Name
* Instance ID
* Error Message
* Timestamp

---

# 11. Callback Error Handling

For asynchronous integrations:

    Submit ESS Job
        ↓
    Request ID
        ↓
    Callback
        ↓
    Job Failed
        ↓
    Notification

---

# 12. Business Validation

Validate input before calling downstream systems.

    PO Number
        ↓
    Exists?
        ↓
    No
        ↓
    Throw New Fault


---

# 13. Batch Error Handling

Example

    10 batches

    Batch 1 ✔
    Batch 2 ✔
    Batch 3 ✔
    Batch 4 ✔
    Batch 5 ❌
    ↓
    Stop Processing
    ↓
    Notify Support
    Or
    Retry only failed batch.

---

# 14. Connectivity Agent Error Handling

If the Connectivity Agent is unavailable:

    Database Connection
        ↓
    Failed
        ↓
    Retry
        ↓
    Notification
        ↓
    Stop


---

# 15. REST/SOAP Fault Handling

Example

    REST
        ↓
    404
        ↓
    Switch
        ↓
    Business Error


SOAP

    SOAP Fault
        ↓
    Fault Handler
        ↓
    Notification


---

# Real Example (Your Hanwha Project)

        Buyer
            ↓
        VBCS
            ↓
        OIC
            ↓
        SAP
            ↓
        Timeout
            ↓
        Scope Fault Handler
            ↓
        Insert Error into ATP
            ↓
        Send Email
            ↓
        Re-throw Fault


---

# Real Example (CRTS Project)

        BI Publisher
        ↓
        CSV
        ↓
        Stage File
        ↓
        Database Insert
        ↓
        Primary Key Error
        ↓
        Error Table
        ↓
        Move File to Error Folder
        ↓
        Notification


---

# Best Practice Architecture

        Integration
            │
        Business Validation
            │
        Scope
            │
        Invoke
            │
        Error?
            │
        Scope Fault Handler
            │
        Log Error
            ↓
        Insert Error Table
            ↓
        Move File
            ↓
        Email
            ↓
        Re-throw Fault
            ↓
        Global Fault Handler
            ↓
        Monitoring

Scenario 1: Exception is Handled (Integration Continues)
-----------------------------------
    Start
    │
    ▼
    Scope
    ├── Invoke REST
    ├── Invoke ERP  ❌ Exception
    └── Scope Fault Handler
            │
            ├── Log Error
            ├── Send Email
            └── Mark Error as Handled
                │
                ▼
    Continue with Next Activity
        │
        ▼
    End


Scenario 2: Exception is Not Handled
----------------------
    Start
    │
    ▼
    Scope
    ├── Invoke ERP ❌
    └── Scope Fault Handler
            │
            ▼
    Rethrow Exception
            │
            ▼
    Global Fault Handler
            │
            ▼
    Integration Ends

    
---

# Summary Table

| Mechanism                   | Purpose                                      |
| --------------------------- | -------------------------------------------- |
| Global Fault Handler        | Handle all unhandled errors                  |
| Scope Fault Handler         | Handle errors in a specific scope            |
| Throw New Fault             | Create business exceptions                   |
| Re-throw Fault              | Propagate the original error                 |
| Stop                        | Terminate integration                        |
| Retry                       | Retry temporary failures                     |
| Switch Validation           | Validate business rules                      |
| Database Logging            | Store error details                          |
| Error Folder                | Preserve failed files                        |
| Email Notification          | Alert support teams                          |
| Callback Handling           | Handle async failures                        |
| Batch Error Handling        | Control batch failures                       |
| Connectivity Agent Handling | Recover from on-premises connectivity issues |

---

# Interview Answer (2 Minutes)

"Oracle Integration Cloud provides several error handling mechanisms. At the integration level, we use the Global Fault Handler to catch unhandled exceptions, while Scope Fault Handlers allow us to manage errors for specific sections of the integration. For business validation failures, we use Throw New Fault to create custom exceptions. After logging or notifying about a system error, we use Re-throw Fault to preserve and propagate the original exception. For transient issues, retry mechanisms can be used. In file-based integrations, failed files are moved to an error folder, and in enterprise projects, error details are stored in database tables and email notifications are sent to the support team. This combination ensures robust monitoring, troubleshooting, and reprocessing capabilities."
