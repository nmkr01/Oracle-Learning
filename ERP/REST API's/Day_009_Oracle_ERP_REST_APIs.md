📘 Day 9 – Oracle ERP REST APIs in Oracle Integration Cloud (OIC)
After learning **Business Eventsand **BI Publisher**, today we'll learn the third major Oracle ERP integration pattern: **REST APIs**.

---

🎯 Learning Objectives
By the end of Day 9, you'll understand:

* What Oracle ERP REST APIs are.
* REST resources and endpoints.
* CRUD operations.
* Authentication (OAuth 2.0).
* Query parameters (`q`, `fields`, `expand`, `onlyData`, `limit`, `offset`).
* Pagination.
* Attachments.
* Using the ERP Cloud Adapter vs REST Adapter.
* Real-world integration scenarios.

---

Why REST APIs?

Oracle ERP Cloud exposes business objects as REST resources, allowing applications like OIC to interact with ERP in real time.

Examples:

* Create Supplier
* Create AP Invoice
* Get Customer
* Update Purchase Order
* Retrieve Journal

---

REST API Architecture


External System / OIC
          │
          ▼
     HTTPS Request
          │
          ▼
 Oracle ERP REST API
          │
          ▼
ERP Business Objects
(AP / AR / GL / Procurement)
          │
          ▼
JSON Response


---

REST Resource Structure

Example:


GET

/fscmRestApi/resources/11.13.18.05/invoices


Components:


Base URL
      │
      ▼
https://<erp-host>

↓

/fscmRestApi

↓

resources

↓

11.13.18.05

↓

invoices


---

CRUD Operations

GET

Retrieve data.


GET

.../invoices


---

POST

Create a record.


POST

.../invoices


Example Payload:

json
{
  "BusinessUnit": "Vision Operations",
  "Supplier": "ABC Suppliers",
  "InvoiceNumber": "INV10001",
  "InvoiceAmount": 5000
}


---

        PATCH

Update an existing record.


        PATCH

.../invoices/{InvoiceId}


Example:

json
{
   "InvoiceAmount": 6500
}


---

DELETE

Delete a resource (only for supported business objects).


DELETE

.../resource/{id}


---

Authentication

Oracle ERP REST APIs commonly use **OAuth 2.0**.


OIC
 │
 ▼
OAuth Token
 │
 ▼
ERP REST API


In OIC, this is typically configured in the **ERP Cloud Adapteror a **REST Adapterconnection.

---

Query Parameters

1. q

Filter records.


GET

/invoices?q=InvoiceNumber=INV10001


---

2. fields

Return only selected columns.


GET

/invoices?fields=InvoiceNumber,InvoiceAmount


---

3. expand

Return child resources.

Example:


GET

/invoices?expand=invoiceLines


This includes invoice header and lines in one response.

---

4. onlyData

Exclude metadata.


GET

/invoices?onlyData=true


Returns a cleaner JSON payload.

---

5. limit

Limit records returned.


GET

/invoices?limit=100


---

6. offset

Pagination.


GET

/invoices?limit=100&offset=200


Returns records 201–300.

---

Pagination

Large datasets should not be retrieved in one request.


Request 1
0–99

↓

Request 2
100–199

↓

Request 3
200–299


---

Attachments

Some ERP resources support attachments.

Example:


POST

.../attachments


Common use cases:

* Invoice PDFs
* Supplier Documents
* Purchase Order Attachments

---

ERP Cloud Adapter vs REST Adapter

| ERP Cloud Adapter      | REST Adapter                  |
| ---------------------- | ----------------------------- |
| Oracle-specific        | Generic REST                  |
| Built-in OAuth support | Manual configuration          |
| Business Events        | Not supported                 |
| BI Publisher           | Manual implementation         |
| REST Operations        | Supported                     |
| Easier ERP integration | Flexible for any REST service |

**Recommendation:Use the **ERP Cloud Adapterwhenever integrating with Oracle ERP Cloud.

---

Real Project Example 1

Create Supplier


CSV File
     │
     ▼
Stage File
     │
     ▼
Mapper
     │
     ▼
ERP REST API
POST /suppliers


---

Real Project Example 2

Get AP Invoice


OIC
 │
 ▼
GET /invoices
 │
 ▼
JSON Response
 │
 ▼
Mapper
 │
 ▼
SAP


---

Real Project Example 3

Update Purchase Order


REST Trigger
      │
      ▼
Mapper
      │
      ▼
        PATCH
/ purchaseOrders/{Id}
      │
      ▼
ERP Updated


---

Common Response

json
{
  "InvoiceId": 300100123456789,
  "InvoiceNumber": "INV10001",
  "InvoiceAmount": 5000,
  "Supplier": "ABC Suppliers"
}


---

Error Handling

| HTTP Code | Meaning               |
| --------- | --------------------- |
| 200       | Success               |
| 201       | Resource Created      |
| 400       | Bad Request           |
| 401       | Unauthorized          |
| 403       | Forbidden             |
| 404       | Resource Not Found    |
| 500       | Internal Server Error |

---

Best Practices

* Use `fields` to reduce payload size.
* Use `q` to filter records instead of fetching all data.
* Use `expand` only when child resources are required.
* Enable pagination for large datasets.
* Handle HTTP error codes gracefully.
* Use retry logic for transient failures (5xx).
* Store credentials securely using OIC connections.

---

REST vs BI Publisher vs Business Events

| Feature            | REST | BIP | Business Events        |
| ------------------ | ---- | --- | ---------------------- |
| Real-Time          | ✅    | ❌   | ✅                      |
| Bulk Data          | ❌    | ✅   | ❌                      |
| Push Notifications | ❌    | ❌   | ✅                      |
| Create/Update Data | ✅    | ❌   | Limited (trigger only) |
| Reporting          | ❌    | ✅   | ❌                      |

---

Interview Questions

1. What are Oracle ERP REST APIs?
2. What is the difference between GET, POST,         PATCH, and DELETE?
3. Why is the `q` parameter used?
4. What is the purpose of the `fields` parameter?
5. Explain the `expand` parameter with an example.
6. Why do we use `onlyData=true`?
7. How do you implement pagination in Oracle ERP REST APIs?
8. When would you choose the ERP Cloud Adapter over the REST Adapter?
9. How do you authenticate with Oracle ERP REST APIs?
10. What are common HTTP response codes you handle in OIC?

---

Assignment

Design an OIC integration for this requirement:

Every hour:

* Retrieve all AP invoices created in the last hour.
* Fetch only `InvoiceNumber`, `Supplier`, and `InvoiceAmount`.
* Process records in batches of 100.
* Send them to SAP.
* Log failed invoices.
* Retry transient failures.
* Email a processing summary.

---

Quiz

1. Why should you use the ERP Cloud Adapter instead of the REST Adapter for Oracle ERP integrations?

2. Explain the purpose of `q`, `fields`, `expand`, `onlyData`, `limit`, and `offset`.

3. What is the difference between `POST` and `        PATCH`?

4. How would you retrieve 5,000 invoices efficiently from Oracle ERP?

5. A REST API returns HTTP 500 for some invoice updates. How would you design the OIC integration to handle this without stopping the entire process?

---

📁 Save Today's Notes


D:\Nysh_Work\Oracle Integration Architect Roadmap\
└── 01_ERP
    └── 03_ERP_Integration
        └── Day_009_Oracle_ERP_REST_APIs.md


---

🎯 End Goal

After Day 9, you'll understand the three primary Oracle ERP integration patterns:

Business Events → Event-driven integrations
BI Publisher → Bulk data extraction and reporting
REST APIs → Real-time CRUD operations

These three integration methods are the foundation of most Oracle Integration Cloud implementations involving Oracle ERP.
