📘 Day 12 – Enterprise Integration Patterns in OIC

Today we move from individual Oracle technologies into integration architecture. The goal is to understand how to choose the right OIC integration pattern for a business requirement.

---

🎯 Day 12 Objectives

By the end of today, you should understand:

* Request-Response
* Fire-and-Forget
* Scheduled/Bulk Integration
* Event-Driven Integration
* Publish-Subscribe
* Orchestration
* Synchronous vs Asynchronous
* Idempotency
* Correlation ID
* Retry & Fault Handling
* Batch Processing
* When to use each pattern

---

1. Request-Response Pattern

The caller sends a request and waits for a response.


External System
      │
      │ Request
      ▼
     OIC
      │
      ▼
 Oracle ERP
      │
      │ Response
      ▼
     OIC
      │
      ▼
External System


Example

External application asks OIC:

Create this Purchase Order."

OIC calls ERP and returns the result.

Use when: the caller needs an immediate response.

---

2. Fire-and-Forget

The caller sends the request but doesn't wait for the complete business processing.


System
  │
  ▼
 OIC
  │
  ▼
Queue / ERP / External System
  │
  ▼
Processing


Example

A system sends 10,000 records to OIC for processing.

OIC accepts the request and processes them asynchronously.

Use when: immediate business response isn't required.

---

3. Scheduled Integration

OIC starts the integration at a defined time.


Scheduler
   │
   ▼
 OIC
   │
   ▼
 ERP / FTP / Database
   │
   ▼
Process Data


Example

Every night at 11 PM:


FTP
 ↓
Read Files
 ↓
Process
 ↓
ERP


You've already used this pattern in your FBDI Journal Import exercises.

---

4. Event-Driven Integration

The integration starts when an event occurs.


Oracle ERP
    │
    │ Business Event
    ▼
   OIC
    │
    ▼
External System


Example


PO Approved
     │
     ▼
Business Event
     │
     ▼
OIC
     │
     ▼
SAP


Use when: near-real-time processing is required and the source supports events.

---

5. Publish-Subscribe Pattern

One event can be consumed by multiple systems.


                 ┌──► SAP
                 │
Business Event ──┼──► OIC Integration 1
                 │
                 ├──► Notification
                 │
                 └──► Data Warehouse


Example:


Invoice Created
       │
       ▼
   Event Bus
       │
   ┌───┼────┐
   ▼   ▼    ▼
 SAP  OIC  Analytics


Use when: multiple consumers need the same event.

---

6. Orchestration Pattern

OIC coordinates multiple systems and steps.


                    ┌──► ERP
                    │
Request ──► OIC ────┼──► SAP
                    │
                    ├──► ATP
                    │
                    └──► Email


Example:


Receive PO
    │
    ▼
Validate
    │
    ▼
Create PO in ERP
    │
    ▼
Get PO Details
    │
    ▼
Send to SAP
    │
    ▼
Store Audit Data in ATP
    │
    ▼
Send Notification


This is one of the most important patterns for an OIC Technical Consultant.

---

7. Batch Processing

Large volumes are processed in manageable chunks.


100,000 Records
       │
       ▼
 ┌─────────────┐
 │ Batch 1     │ 10,000
 ├─────────────┤
 │ Batch 2     │ 10,000
 ├─────────────┤
 │ Batch 3     │ 10,000
 ├─────────────┤
 │     ...     │
 └─────────────┘
       │
       ▼
     ERP


Why?

* Lower memory usage
* Better performance
* Easier error handling
* Better scalability

You've already applied this concept with Stage File → Read File in Segments.

---

8. Synchronous vs Asynchronous

#Synchronous


OIC
 │
 ▼
ERP
 │
 │ Wait
 ▼
Response


The caller waits.

Example


POST /purchaseOrders


---

#Asynchronous


OIC
 │
 ▼
Submit Job
 │
 ▼
Request ID
 │
 ▼
Continue
 │
 ▼
Poll Status


Example

ESS Job submission

You learned this in Days 5, 6 and 10.

---

9. Idempotency ⭐

This is a very important interview topic.

Idempotency means:

> Processing the same request multiple times should not create duplicate business transactions.

Problem

Suppose OIC receives:


Invoice INV1001


OIC creates the invoice in ERP.

But the response is lost.

OIC retries.

Without idempotency:


INV1001 → ERP
INV1001 → ERP


❌ Two invoices could potentially be created.

---

#Idempotent Design

Use a unique business key:


Invoice Number
+
Supplier
+
Business Unit


Before creating:


Check Existing?
      │
 ┌────┴────┐
 │         │
Yes        No
 │         │
Skip      Create


---

10. Correlation ID

Correlation ID allows you to track one transaction across multiple systems.


OIC
 │
 │ Correlation ID:
 │ CORR-2026-000123
 ▼
ERP
 │
 ▼
SAP
 │
 ▼
ATP


You can use the same ID in:

* Logs
* Tracking variables
* Database records
* External API calls
* Error notifications

---

11. Retry Pattern

Transient failures should usually be retried.


API Call
   │
   ▼
Failed?
   │
   ▼
Retry
   │
   ▼
Failed?
   │
   ▼
Retry
   │
   ▼
Success


Example:


Attempt 1 → HTTP 503
Attempt 2 → HTTP 503
Attempt 3 → HTTP 200


But don't blindly retry every error.

Usually retry:

* 500
* 502
* 503
* 504
* Temporary network failures

Usually don't retry:

* 400
* 401
* 403
* Invalid business data

---

12. Fault Handling Pattern


                API Call
                   │
             ┌─────┴─────┐
             │           │
          Success       Failure
             │           │
             ▼           ▼
          Continue    Fault Handler
                         │
                    ┌────┴────┐
                    │         │
                  Retry     Log Error
                    │         │
                    ▼         ▼
                 Continue   Notify


---

13. Complete Enterprise Example

Let's combine everything.

Requirement

> When a Purchase Order is approved in Oracle ERP, send it to SAP, store audit information in ATP, and notify support if SAP is unavailable.

Architecture


                    Oracle ERP
                        │
                        │ PO Approved
                        ▼
                 Business Event
                        │
                        ▼
                       OIC
                        │
                        ▼
                    Validate
                        │
                        ▼
                     Scope
                        │
                        ▼
                  SAP REST API
                        │
                 ┌──────┴──────┐
                 │             │
              Success        Failure
                 │             │
                 ▼             ▼
             ATP Audit      Retry
                               │
                          ┌────┴────┐
                          │         │
                       Success    Failed
                          │         │
                          ▼         ▼
                       ATP Audit  Error Log
                                     │
                                     ▼
                                  Email


This single example demonstrates:

* Event-driven
* Orchestration
* Request-response
* Fault handling
* Retry
* Audit
* Notification
* Idempotency

---

🔥 Pattern Selection Cheat Sheet

| Requirement                      | Pattern           |
| -------------------------------- | ----------------- |
| Immediate response required      | Request-Response  |
| No immediate response required   | Fire-and-Forget   |
| Run every night                  | Scheduled         |
| React to ERP transaction         | Business Event    |
| Multiple consumers               | Publish-Subscribe |
| Coordinate multiple systems      | Orchestration     |
| Millions of records              | Batch             |
| Long-running ESS process         | Asynchronous      |
| Prevent duplicates               | Idempotency       |
| Track transaction across systems | Correlation ID    |
| Temporary API failure            | Retry             |
| Permanent failure                | Fault Handler     |

---

🎤 Day 12 Interview Questions

Q1 What is the difference between synchronous and asynchronous integration?

Q2 When would you choose a Business Event instead of a Scheduled Integration?

Q3 What is idempotency and why is it important in OIC?

Q4 How would you prevent duplicate invoice creation?

Q5 What is a correlation ID?

Q6 When should you use retry logic?

Q7 Would you retry an HTTP 401 error? Why?

Q8 Explain the difference between orchestration and publish-subscribe.

Q9 How would you process 1 million records in OIC?

Q10 Design an integration where an ERP Business Event triggers OIC, OIC calls SAP, and failed transactions are retried and logged.

---

🧪 Today's Assignment

Design this integration:

> Oracle ERP → OIC → SAP

Requirement:

1. PO is approved in ERP.
2. ERP Business Event triggers OIC.
3. OIC validates the PO.
4. OIC checks whether the PO was already processed.
5. If not processed, send it to SAP.
6. Retry SAP failures up to 3 times.
7. Store successful transaction details in ATP.
8. Store failed transactions in an error table.
9. Send a summary/alert to support.

Draw the complete architecture and identify the integration pattern used at every stage.

---

📁 Save Today's Notes


D:\Nysh_Work\Oracle Integration Architect Roadmap\
└── 01_ERP
    └── 03_ERP_Integration
        └── Day_012_Enterprise_Integration_Patterns.md


#⭐ Day 12 Key Takeaway

Don't just say:

I will use OIC."

Start saying:

For this requirement, I would choose an event-driven orchestration pattern with idempotency, correlation tracking, retry handling, and asynchronous processing where appropriate."

That is the shift from OIC Developer thinking → Integration Architect thinking.
