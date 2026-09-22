# 📘 Day 14 – OIC Integration Error Handling & Fault Management

Day 13 covered **Business Objects and Parent–Child relationships**. Today we'll focus on one of the most important skills for a real OIC developer:

> **What happens when an integration fails, and how do we design it so one failure doesn't bring down the entire process?**

This is especially important for interviews and production support.

---

# 🎯 Day 14 Objectives

You will learn:

* OIC Fault Handling
* Scope
* Fault Handler
* Global Fault Handler
* Stage File errors
* REST errors
* ERP errors
* SOAP Faults
* Retry mechanisms
* Business vs Technical errors
* Error logging
* Reprocessing
* Notifications
* Error handling architecture

---

# 1. What is Error Handling?

Consider this integration:

```text
FTP
 │
 ▼
OIC
 │
 ▼
Oracle ERP
 │
 ▼
ATP
```

Suppose ERP returns:

```text
401 Unauthorized
```

If there is no error handling:

```text
Integration
     │
     ▼
    ❌
Terminated
```

A production integration should instead:

```text
Integration
     │
     ▼
Error
     │
     ▼
Capture Error
     │
     ▼
Log Error
     │
     ▼
Retry / Reprocess
     │
     ▼
Notify Support
```

---

# 2. Types of Errors

There are two major categories.

## Technical Error

Caused by the technology/system.

Examples:

```text
HTTP 500
HTTP 503
Connection Timeout
Network Failure
SOAP Fault
Database Connection Error
```

These may be **retryable**.

---

## Business Error

Caused by invalid business data.

Examples:

```text
Invalid Supplier
Invalid Business Unit
Invalid Currency
Missing Invoice Number
Invalid Accounting Combination
```

These generally should **not be blindly retried**.

---

# 3. Scope in OIC ⭐

A **Scope** groups multiple actions into one logical unit.

Example:

```text
Scope
 │
 ├── Mapper
 │
 ├── REST Invoke
 │
 ├── Transformation
 │
 └── ATP Insert
```

You can attach a **Fault Handler** to the Scope.

---

# 4. Scope Fault Handler

Example:

```text
              Scope
                │
        ┌───────┴────────┐
        │                │
     Success           Failure
        │                │
        ▼                ▼
     Continue       Fault Handler
                         │
                    ┌────┴────┐
                    │         │
                  Log       Notify
```

This is one of the most important OIC patterns.

---

# 5. Example – REST API Failure

Suppose OIC calls an external REST API.

```text
For Each Invoice
       │
       ▼
     Scope
       │
       ▼
  REST API
       │
    ┌──┴───┐
    │      │
  200     500
    │      │
    ▼      ▼
Success  Fault
           │
           ▼
       Retry Logic
```

If the retry succeeds:

```text
500
 ↓
Retry
 ↓
200
 ↓
Success
```

If all retries fail:

```text
500
 ↓
Retry
 ↓
500
 ↓
Retry
 ↓
500
 ↓
Log Failure
```

---

# 6. Don't Retry Everything ❗

This is a common interview question.

| Error                     | Retry?    |
| ------------------------- | --------- |
| 400 Bad Request           | ❌         |
| 401 Unauthorized          | ❌         |
| 403 Forbidden             | ❌         |
| 404 Not Found             | Usually ❌ |
| 409 Conflict              | Depends   |
| 500 Internal Server Error | ✅         |
| 502 Bad Gateway           | ✅         |
| 503 Service Unavailable   | ✅         |
| 504 Gateway Timeout       | ✅         |
| Network Timeout           | ✅         |

### Remember:

> **Technical/transient errors → consider retry.**

> **Business/validation errors → fix the data instead.**

---

# 7. Retry Pattern

Example:

```text
REST Call
   │
   ▼
HTTP 503
   │
   ▼
Wait
   │
   ▼
Retry #1
   │
   ▼
HTTP 503
   │
   ▼
Retry #2
   │
   ▼
HTTP 200
   │
   ▼
Success
```

Don't create infinite retries.

Example:

```text
Maximum Attempts = 3
```

---

# 8. Business Error Handling

Suppose:

```text
Invoice Number = INV1001
Supplier = ABC
Currency = XYZ
```

ERP responds:

```text
Invalid Currency
```

This isn't a temporary technical problem.

Don't do:

```text
Retry
Retry
Retry
```

Instead:

```text
ERP Validation Error
        │
        ▼
Capture Error
        │
        ▼
Log Invoice
        │
        ▼
Error Table / FTP
        │
        ▼
Notify Support
```

---

# 9. Error Logging in ATP

For enterprise integrations, ATP can be used as an error/audit repository.

Example table:

```text
OIC_ERROR_LOG
```

Columns:

```text
INTEGRATION_NAME
CORRELATION_ID
BUSINESS_KEY
ERROR_CODE
ERROR_MESSAGE
ERROR_TIMESTAMP
STATUS
RETRY_COUNT
PAYLOAD_REFERENCE
```

Example:

```text
Integration : AP_INVOICE_SYNC
Correlation : INV-2026-000123
Business Key: INV1001
Error Code  : 503
Status      : FAILED
Retry Count : 3
```

---

# 10. Correlation ID

Correlation IDs are extremely useful when troubleshooting.

Example:

```text
CORR-20260902-00125
```

Use it across:

```text
OIC
 │
 ├── ERP
 │
 ├── REST API
 │
 └── ATP
```

Now support can search using one ID.

---

# 11. Global Fault Handler

A **Global Fault Handler** provides centralized handling for faults that aren't handled locally.

Conceptually:

```text
Integration
     │
 ┌───┼────────┐
 │   │        │
Scope Scope  Invoke
 │
 ▼
Local Fault Handler
     
        +
        
Global Fault Handler
```

Use local handlers when you need **specific recovery behavior**.

Use the global handler for **common/unhandled failures** such as:

* Logging
* Notifications
* Common error processing

---

# 12. For Each + Fault Handling ⭐

This is extremely important for large integrations.

Suppose you have:

```text
10,000 invoices
```

And invoice #125 fails.

### Bad design

```text
For Each
   │
   ├── Invoice 1 ✅
   ├── Invoice 2 ✅
   ├── ...
   ├── Invoice 125 ❌
   │
   └── Integration Stops
```

### Better design

```text
For Each
   │
   ├── Invoice 1 → ✅
   ├── Invoice 2 → ✅
   ├── ...
   ├── Invoice 125 → ❌ → Log
   ├── Invoice 126 → ✅
   ├── Invoice 127 → ✅
   └── Continue
```

This is why **Scope + Fault Handler** is so important.

---

# 13. Complete Error Handling Architecture

```text
                 OIC Integration
                       │
                       ▼
                  Read Records
                       │
                       ▼
                    For Each
                       │
                       ▼
                     Scope
                       │
                 ┌─────┴─────┐
                 │           │
              Success      Failure
                 │           │
                 ▼           ▼
             Continue    Fault Handler
                             │
                    ┌────────┼────────┐
                    │        │        │
                 Identify   Log     Retry?
                   Error     │        │
                             ▼        ▼
                            ATP    Transient?
                                     │
                                ┌────┴────┐
                                │         │
                               Yes        No
                                │         │
                                ▼         ▼
                              Retry    Mark Failed
                                │         │
                           ┌────┴────┐    │
                           │         │    │
                       Success    Failed  │
                           │         │    │
                           ▼         └────┼────┘
                       Continue           │
                                          ▼
                                    Error Table
                                          │
                                          ▼
                                    Notification
```

---

# 14. Reprocessing Pattern

Production integrations should ideally support reprocessing.

Example:

```text
Failed Transactions
        │
        ▼
ATP Error Table
        │
        ▼
Support Fixes Data
        │
        ▼
Reprocessing Integration
        │
        ▼
OIC
        │
        ▼
Oracle ERP
```

This avoids manually recreating transactions.

---

# 15. Error Handling Example

### Requirement

> Process 5,000 invoices from FTP and create them in Oracle ERP.

Architecture:

```text
FTP
 │
 ▼
Stage File
 │
 ▼
Read in Segments
 │
 ▼
For Each Invoice
 │
 ▼
Scope
 │
 ▼
ERP Invoice API
 │
 ├───────────────┐
 │               │
Success          Error
 │               │
 ▼               ▼
Success++    Fault Handler
                 │
          ┌──────┴──────┐
          │             │
       500/503        400/401
          │             │
        Retry        Log Failure
          │             │
      ┌───┴───┐         │
      │       │         │
   Success  Failed      │
      │       │         │
      ▼       └────┬────┘
 Success++         │
                   ▼
                ATP Log
                   │
                   ▼
             Continue Next
                   │
                   ▼
             Summary Email
```

---

# 16. Error Notification

At the end:

```text
Total Records       : 5,000
Successful          : 4,920
Failed              : 80
Retried             : 65
Permanent Failures  : 15
```

Email can contain:

```text
Integration:
AP Invoice Import

Correlation ID:
CORR-20260902-00125

Status:
Completed with Errors

Failed Records:
80
```

---

# ⭐ Architect-Level Principle

Don't design integrations like:

```text
Call API
 ↓
If error
 ↓
Integration fails
```

Instead think:

```text
Detect
 ↓
Classify
 ↓
Retry if transient
 ↓
Log
 ↓
Recover / Reprocess
 ↓
Notify
 ↓
Continue where possible
```

---

# 🎤 Day 14 Quiz

Answer these in your own words:

### 1.

What is the difference between a **technical error** and a **business error**?

### 2.

Why do we use **Scope + Fault Handler** in OIC?

### 3.

Would you retry an HTTP **400** error? Why?

### 4.

Would you retry an HTTP **503** error? Why?

### 5.

How would you prevent one failed record from stopping a `For Each` loop?

### 6.

What information would you store in an **ATP error table**?

### 7.

What is a **Correlation ID**, and why is it useful?

### 8.

What is the purpose of a **Global Fault Handler**?

### 9.

Explain a reprocessing mechanism for failed transactions.

### 10. ⭐ Interview Scenario

You have **50,000 records** in an FTP file.

During processing:

* 49,500 succeed.
* 300 fail with HTTP 503.
* 150 fail with HTTP 400.
* 50 fail with HTTP 401.

How would you design the error handling?

---

# 🧪 Day 14 Assignment

Design an OIC integration:

> **FTP → OIC → Oracle ERP → ATP**

Requirements:

* Read a large file using **Read File in Segments**.
* Process records using **For Each**.
* Use **Scope + Fault Handler**.
* Retry transient REST errors.
* Don't retry validation errors.
* Store failures in ATP.
* Generate a correlation ID.
* Continue processing remaining records.
* Send a final summary email.
* Support reprocessing of failed records.

Draw the complete architecture.

---

# 📁 Save Today's Notes

```text
D:\Nysh_Work\Oracle Integration Architect Roadmap\
└── 01_ERP
    └── 03_ERP_Integration
        └── Day_014_OIC_Error_Handling_Fault_Management.md
```

---

## ⭐ Day 14 Key Takeaway

Remember this interview statement:

> **"I classify errors into technical and business errors. For transient technical failures such as 5xx or timeout errors, I implement controlled retry. For business validation errors, I log the transaction and route it for correction and reprocessing. I use Scope Fault Handlers to isolate record-level failures, maintain correlation IDs for traceability, store errors in ATP, and use centralized fault handling for unhandled exceptions."**

That's a strong **3-year OIC developer / integration architect** answer.
