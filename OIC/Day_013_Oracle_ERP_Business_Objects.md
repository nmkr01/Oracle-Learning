# 📘 Day 13 – Oracle ERP Business Objects & Parent–Child Relationships

Today we move deeper into **Oracle Fusion ERP data and business objects**. The main goal is to understand how Oracle ERP organizes business data and how OIC uses those objects when building integrations.

---

## 🎯 Day 13 Objectives

You will learn:

* What is an Oracle ERP Business Object?
* Business Object vs REST API
* Parent–Child relationships
* AP Invoice structure
* Purchase Order structure
* GL Journal structure
* Supplier structure
* How to identify the correct API/object
* Nested payloads
* Parent → Child processing
* Real OIC integration scenarios

---

# 1. What is a Business Object?

A **Business Object** represents a business entity or transaction in Oracle Fusion.

Examples:

```text
Supplier
Invoice
Purchase Order
Receipt
Journal
Customer
Payment
Asset
```

Think of it as:

```text
Business Requirement
        │
        ▼
Oracle Business Object
        │
        ▼
API / SOAP Service / ERP Adapter
        │
        ▼
OIC
```

---

# 2. Business Object vs API

These are not the same thing.

### Business Object

Represents the **business entity**.

Example:

```text
Purchase Order
```

### API

Provides a way to **create, retrieve, update, or process** that business object.

Example:

```text
Purchase Orders REST API
```

So:

```text
Purchase Order
      │
      ▼
REST API
      │
      ▼
OIC
```

---

# 3. Parent–Child Relationship ⭐

This is extremely important when working with Oracle Fusion APIs.

Many ERP transactions contain hierarchical data.

Example:

```text
Purchase Order
      │
      ├── Header
      │
      ├── Lines
      │     ├── Line 1
      │     ├── Line 2
      │     └── Line 3
      │
      └── Schedules
            ├── Schedule 1
            └── Schedule 2
```

Think:

> **Parent = Main transaction**

> **Child = Details belonging to the parent**

---

# 4. AP Invoice Structure

An invoice can be represented as:

```text
Invoice
   │
   ├── Header
   │     ├── Invoice Number
   │     ├── Supplier
   │     ├── Business Unit
   │     └── Invoice Date
   │
   └── Lines
         ├── Line 1
         ├── Line 2
         └── Line 3
```

Example:

```text
INV-1001
   │
   ├── Header
   │
   └── Lines
        │
        ├── Consulting – $500
        ├── Software – $300
        └── Support – $200
```

---

# 5. Purchase Order Structure

This is particularly important for your OIC project experience.

```text
Purchase Order
       │
       ├── Header
       │
       ├── Lines
       │      │
       │      ├── Line 1
       │      └── Line 2
       │
       └── Schedules
              │
              ├── Schedule 1
              └── Schedule 2
```

Example:

```text
PO 450001
   │
   ├── Line 1 → Laptop
   │       └── Schedule 1
   │
   └── Line 2 → Monitor
           └── Schedule 2
```

---

# 6. GL Journal Structure

```text
Journal
   │
   ├── Header
   │     ├── Ledger
   │     ├── Period
   │     ├── Source
   │     └── Category
   │
   └── Lines
         │
         ├── Line 1
         │     ├── Account
         │     └── Debit/Credit
         │
         └── Line 2
               ├── Account
               └── Debit/Credit
```

Example:

```text
Journal
   │
   └── Lines
        │
        ├── Cash Account
        │      Debit $1,000
        │
        └── Revenue Account
               Credit $1,000
```

---

# 7. Supplier Structure

Supplier data can also have multiple levels.

```text
Supplier
   │
   ├── Supplier Header
   │
   ├── Supplier Sites
   │       │
   │       ├── Site 1
   │       └── Site 2
   │
   └── Contacts
           │
           ├── Contact 1
           └── Contact 2
```

This becomes important when an integration needs to process supplier sites or contacts.

---

# 8. Why Parent–Child Matters in OIC

Suppose you retrieve:

```text
PO
 ├── Header
 ├── 10 Lines
 └── 20 Schedules
```

You cannot treat every element as an independent transaction.

You need to understand the hierarchy.

Typical OIC flow:

```text
Get PO
   │
   ▼
Read Header
   │
   ▼
For Each Line
   │
   ▼
For Each Schedule
   │
   ▼
Process
```

---

# 9. Nested REST Payload

Oracle REST APIs may return nested structures.

Example:

```json
{
  "OrderNumber": "450001",
  "Supplier": "ABC Ltd",
  "lines": [
    {
      "LineNumber": 1,
      "Item": "Laptop",
      "schedules": [
        {
          "ScheduleNumber": 1,
          "Quantity": 10
        }
      ]
    }
  ]
}
```

Hierarchy:

```text
Order
 │
 └── Lines
       │
       └── Schedules
```

---

# 10. OIC Mapping

Suppose the source is:

```text
SAP
 │
 ▼
Purchase Order
 │
 ├── Header
 ├── Lines
 └── Schedules
```

Target:

```text
Oracle ERP
 │
 ▼
Purchase Order API
```

The mapper must preserve the hierarchy:

```text
SAP Header
      ↓
ERP Header

SAP Line
      ↓
ERP Line

SAP Schedule
      ↓
ERP Schedule
```

---

# 11. Parent–Child Processing Pattern

A common OIC design is:

```text
Get Parent Records
        │
        ▼
For Each Parent
        │
        ▼
Get Child Records
        │
        ▼
For Each Child
        │
        ▼
Validate
        │
        ▼
Process
```

Example:

```text
Get Purchase Orders
        │
        ▼
For Each PO
        │
        ▼
Get PO Lines
        │
        ▼
For Each Line
        │
        ▼
Get PO Schedules
        │
        ▼
Process Schedule
```

This is very similar to the **GetPurchaseOrders → GetLines → GetPOSchedules → CreateReceipt** pattern you've already worked with.

---

# 12. API Parameters and Relationships

When retrieving ERP business objects, you may use:

### `q`

Filter records.

```text
q=OrderNumber=450001
```

### `fields`

Return only required attributes.

```text
fields=OrderNumber,Supplier
```

### `expand`

Retrieve child resources.

```text
expand=lines
```

### `limit`

Control number of records.

```text
limit=100
```

### `offset`

Implement pagination.

```text
offset=100
```

---

# 13. Real OIC Example

### Requirement

> Retrieve all Purchase Orders created today and send them to SAP.

Architecture:

```text
Scheduled OIC
      │
      ▼
ERP REST API
      │
      ▼
Get Purchase Orders
      │
      ▼
Pagination
      │
      ▼
For Each PO
      │
      ▼
Get Lines
      │
      ▼
For Each Line
      │
      ▼
Validate
      │
      ▼
SAP REST API
      │
      ▼
Success / Failure
```

---

# 14. Parent–Child Error Handling

Suppose:

```text
PO 1001
 ├── Line 1 → Success
 ├── Line 2 → Success
 └── Line 3 → Failed
```

You need to decide whether:

### Option A – Fail entire PO

```text
Line 3 Failed
     ↓
PO Failed
```

### Option B – Continue processing

```text
Line 1 → Success
Line 2 → Success
Line 3 → Failed
              ↓
          Log Error
              ↓
       Continue Next PO
```

The correct approach depends on the **business requirement**.

---

# 15. Business Object Selection

Before creating an integration, ask:

```text
What business process?
       │
       ▼
What business object?
       │
       ▼
What operation?
       │
       ▼
Which API/service?
       │
       ▼
What authentication?
       │
       ▼
What integration pattern?
```

Example:

```text
Requirement:
Create Supplier

        ↓

Business Object:
Supplier

        ↓

Operation:
Create

        ↓

API:
Supplier REST API

        ↓

OIC:
ERP REST Invocation
```

---

# 🔥 Interview Scenario

### Requirement

> A company wants to send Purchase Orders from Oracle ERP to SAP.

Interviewer asks:

**"How would you design the integration?"**

A strong answer:

> "I would first identify the Purchase Order business object and understand its parent-child hierarchy of header, lines, and schedules. I would use the Oracle ERP REST API or ERP Cloud Adapter based on the required operation. I would retrieve the records using appropriate filters and pagination, iterate through the Purchase Orders and their child records, map them to the SAP structure, implement idempotency and correlation tracking, and handle transient failures using retry and fault handling."

That answer demonstrates **business understanding + API knowledge + OIC architecture**.

---

# 🎤 Day 13 Quiz

Answer these in your notes in simple language.

### 1.

What is an Oracle ERP Business Object?

### 2.

What is the difference between a Business Object and an API?

### 3.

What is a Parent–Child relationship? Give an Oracle ERP example.

### 4.

Explain the hierarchy of a Purchase Order.

### 5.

How would you process a Purchase Order with multiple Lines and Schedules in OIC?

### 6.

What is the purpose of `expand` in an Oracle REST API?

### 7.

Why is understanding the business object important before developing an OIC integration?

### 8.

A PO has 10 lines and line 7 fails while sending to SAP. Should the entire PO fail or should OIC continue? Explain your approach.

---

# 🧪 Day 13 Assignment

Design this flow:

> **Oracle ERP Purchase Order → OIC → SAP**

Requirements:

* Retrieve POs from ERP.
* Handle pagination.
* Process each PO.
* Process PO Lines.
* Validate mandatory fields.
* Send valid data to SAP.
* Log failed records.
* Prevent duplicate processing.
* Maintain a correlation ID.
* Send a final summary.

Draw the complete flow and identify:

**Parent → Child → Validation → SAP → Success/Failure**

---

## 📁 Save Today's Notes

Since you are keeping the daily topics together rather than creating many folders:

```text
D:\Nysh_Work\Oracle Integration Architect Roadmap\
└── 01_ERP
    └── Day_013_Oracle_ERP_Business_Objects.md
```

### ⭐ Day 13 Key Takeaway

Remember this simple hierarchy:

```text
Business Requirement
        ↓
Business Object
        ↓
Parent / Child Structure
        ↓
API / Adapter
        ↓
OIC Mapping
        ↓
Integration Processing
        ↓
Target System
```

This is the thinking pattern you should use when designing Oracle ERP integrations.