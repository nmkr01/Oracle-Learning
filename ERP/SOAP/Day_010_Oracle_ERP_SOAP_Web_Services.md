📘 Day 10 – Oracle ERP SOAP Web Services in Oracle Integration Cloud (OIC)
---------
Today you'll learn the fourth major Oracle ERP integration mechanism: SOAP Web Services.
Even though REST APIs are widely used, many Oracle ERP operations—especially legacy, enterprise, and bulk processes—still rely on SOAP services.

---

🎯 Learning Objectives
By the end of Day 10, you'll understand:

    * What SOAP Web Services are.
    * SOAP Architecture.
    * WSDL structure.
    * SOAP Envelope.
    * SOAP Adapter in OIC.
    * SOAP vs REST.
    * Security (WS-Security & OAuth).
    * Importing WSDL into OIC.
    * Real project examples.
    * Best practices.

---

What is SOAP?
SOAP (Simple Object Access Protocol) is an XML-based messaging protocol used to exchange structured information between systems over HTTP or HTTPS.
Unlike REST, SOAP follows a strict contract defined by a WSDL (Web Services Description Language).

---

SOAP Architecture

        External System / OIC
                │
                ▼
        SOAP Request (XML)
                │
                ▼
        Oracle ERP SOAP Service
                │
                ▼
        Business Logic Execution
                │
                ▼
        SOAP Response (XML)

---

SOAP Components

1. WSDL
A WSDL defines:
    * Available Operations
    * Input Messages
    * Output Messages
    * Endpoint URL
    * XML Schema (XSD)

Example:    https://erp-host/fscmService/ErpIntegrationService?WSDL

OIC imports this WSDL to generate request and response schemas automatically.
---

2. SOAP Envelope
Every SOAP request follows this structure:

        xml
            <soapenv:Envelope>
            <soapenv:Header>
            </soapenv:Header>

            <soapenv:Body>

            </soapenv:Body>

            </soapenv:Envelope>

Header
Contains:
    * Security
    * Authentication
    * WS-Security Tokens

Body
Contains:
    * Request Payload
    * Business Data
---

SOAP Request Example
xml
    <soapenv:Envelope>
    <soapenv:Body>
        <submitESSJobRequest>
            <jobPackageName>/oracle/apps/ess</jobPackageName>
            <jobDefinitionName>Import Journals</jobDefinitionName>
        </submitESSJobRequest>
    </soapenv:Body>
    </soapenv:Envelope>
---

SOAP Response Example

xml
    <submitESSJobResponse>
        <requestId>300000123456789</requestId>
        <status>SUCCESS</status>
    </submitESSJobResponse>
---

Using SOAP in OIC

        SOAP Adapter
                │
                ▼
            Import WSDL
                │
                ▼
        Generate Request Schema
                │
                ▼
            Mapper
                │
                ▼
        Invoke Oracle ERP SOAP Service
                │
                ▼
        Receive XML Response
                │
                ▼
            Mapper
                │
                ▼
            Next System
---

Importing a WSDL
Steps:
        Create Integration
                │
                ▼
        SOAP Adapter
                │
                ▼
        Invoke
                │
                ▼
        Provide WSDL URL
                │
                ▼
        Select Operation
                │
                ▼
            Finish

OIC automatically generates:
    * Request XSD
    * Response XSD
    * Mapper structure

---

SOAP Adapter Configuration
Required:
    * WSDL URL
    * Security Policy
    * Authentication
    * Operation Selection
---

Authentication
Common methods: Basic Authentication

    Username
    Password
---

WS-Security
    Uses:

    * Username Token
    * Timestamp
    * Digital Signature
---

OAuth
Supported by some Oracle SOAP services through configured policies.
---

SOAP vs REST

| SOAP                    | REST              |
| ----------------------- | ----------------- |
| XML only                | JSON / XML        |
| Contract-first (WSDL)   | Resource-based    |
| Strict schema           | Flexible          |
| WS-Security             | OAuth, Basic, JWT |
| Heavier payload         | Lightweight       |
| Enterprise transactions | Modern APIs       |

---

ERP SOAP Services Commonly Used

1. ErpIntegrationService
    Used for:

    * Upload File to UCM
    * Submit ESS Job
    * Download Output
    * Get ESS Status
---

2. Journal Import
        Upload ZIP
            │
            ▼
        Submit ESS Job
            │
            ▼
        Receive Request ID
            │
            ▼
        Poll Status
---

3. Journal Approval

    SOAP Service
        ↓
    Approve Journal
---

4. ESS Job Submission

Example:
    Import Journals
    Create Accounting
    Post Journals
    Import Payables


---

Real Project Example
Journal Import

        FTP
        │
        ▼
    Stage File
        │
        ▼
    Generate CSV
        │
        ▼
        ZIP
        │
        ▼
    Upload to UCM (SOAP)
        │
        ▼
    Submit ESS Job (SOAP)
        │
        ▼
    Receive Request ID
        │
        ▼
    Poll ESS Status
        │
        ▼
    Completed
        │
        ▼
    Email
---

SOAP Fault Example

xml
<soap:Fault>
    <faultcode>soap:Server</faultcode>
    <faultstring>Authentication Failed</faultstring>
</soap:Fault>
---

Handling SOAP Faults in OIC
        SOAP Invoke
            │
            ▼
        Scope
            │
        ┌────┴─────┐
        │          │
        Success   Fault Handler
        │          │
        ▼          ▼
        Next     Log Error
        Step      Send Email
---

Best Practices

    * Always use the latest WSDL version.
    * Validate request payloads before invocation.
    * Handle SOAP Faults gracefully.
    * Use Scope and Fault Handler.
    * Secure credentials using OIC Connections.
    * Avoid hardcoding endpoints.
    * Log Request ID and Fault Messages.
---

SOAP or REST?
--------
| Requirement     | Preferred          |
| --------------- | ------------------ |
| Create Invoice  | REST               |
| Upload FBDI     | SOAP               |
| Submit ESS Job  | SOAP               |
| Get Report      | REST / ERP Adapter |
| Business Events | ERP Adapter        |
| Bulk Import     | SOAP               |
| Real-time CRUD  | REST               |
---

Interview Questions

    1. What is SOAP?
    2. What is WSDL?
    3. Explain the structure of a SOAP Envelope.
    4. What is the purpose of the SOAP Header?
    5. What is the difference between SOAP and REST?
    6. How do you import a SOAP service into OIC?
    7. What is a SOAP Fault?
    8. How do you handle SOAP Faults in OIC?
    9. Why is ErpIntegrationService commonly used?
    10. When would you choose SOAP over REST?
---

Assignment
    Design an OIC integration for this requirement:

Every night:
    * Read Journal CSV files from FTP.
    * Generate a ZIP file.
    * Upload it to UCM using `ErpIntegrationService`.
    * Submit the Import Journals ESS Job.
    * Poll the job status every 30 seconds.
    * If successful, send a confirmation email.
    * If failed, capture the SOAP Fault, log the error, and notify support.
---

Quiz
---
    1. What is the difference between SOAP and REST?
    2. What is a WSDL, and why is it important?
    3. Explain the structure of a SOAP Envelope.
    4. What is the purpose of `ErpIntegrationService` in Oracle ERP?
    5. How would you design an OIC integration to upload an FBDI ZIP file, submit an ESS Job, and handle SOAP Faults without stopping the integration?

    Ans : 
                                    FTP Trigger
                                        │
                                        ▼
                                    Read Journal CSV
                                        │
                                        ▼
                                    Stage File
                                        │
                                        ▼
                                    Generate ZIP
                                        │
                                        ▼
                                    SOAP Adapter
                                    UploadFileToUCM
                                        │
                                        ▼
                                    Receive File ID
                                        │
                                        ▼
                                    SOAP Adapter
                                    submitESSJob
                                        │
                                        ▼
                                    Receive Request ID
                                        │
                                        ▼
                                    Wait (30 sec)
                                        │
                                        ▼
                                    getESSJobStatus
                                        │
                                        ▼
                                    Completed?
                                    ┌────┴─────┐
                                    │          │
                                    Yes         No
                                    │          │
                                    ▼          ▼
                                    Email   Scope Fault Handler
                                            │
                                            ├── Log SOAP Fault
                                            ├── Retry (if applicable)
                                            ├── Notify Support
                                            └── Archive Failed File
---------------------

⭐ Senior Interview Scenario
Imagine you're importing 1 million journal lines into Oracle ERP.
The interviewer asks:
    "What if the submitESSJob SOAP service succeeds, but the ESS Job fails due to validation errors in the FBDI file? How would you design the integration?"

A strong answer would be:
        Upload the ZIP to UCM.
        Submit the ESS Job.
        Poll the job status until completion.
        If the job fails:
        Retrieve the ESS job log or output file.
        Parse validation errors.
        Archive the failed ZIP and log files.
        Send a detailed email with the Request ID, error summary, and failed file name.
        Allow the integration to complete gracefully instead of terminating unexpectedly.
        If successful:
        Archive the processed file.
        Send a success notification with the ESS Request ID.

This kind of answer demonstrates that you understand not only OIC activities but also enterprise-grade error handling and operational support, which interviewers value for experienced Oracle Integration developers.
---

🚀 What's Coming Next
Day 11 – Oracle ERP Security & Authentication

We'll cover:
    * Oracle Identity Domains
    * OAuth 2.0
    * Basic Authentication
    * JWT Bearer Token
    * WS-Security
    * Certificates (JKS/PKCS12)
    * Security Policies in OIC
    * Connection authentication
    * Real-world authentication scenarios

This topic will connect directly with your previous work on OIC connections, ERP authentication, and integration security, making it highly relevant for enterprise Oracle Integration projects.
