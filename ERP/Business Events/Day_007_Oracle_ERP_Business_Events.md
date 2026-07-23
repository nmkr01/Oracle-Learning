📘 Day 7 – Oracle ERP Business Events in Oracle Integration Cloud (OIC)

So far, we've learned file-based (FBDI) integrations. Today we'll learn Event-Driven Integrations, one of the most important topics for Oracle Integration Cloud interviews.

---

🎯 Learning Objectives

By the end of today, you'll understand:

    * What Business Events are.
    * Why they are used.
    * Event-driven architecture.
    * How OIC subscribes to Oracle ERP events.
    * Difference between Scheduled, REST, and Business Event integrations.
    * Real-world business scenarios.

---

What is a Business Event?

    A Business Event is a notification automatically published by Oracle ERP Cloud whenever a business transaction occurs.
    Instead of OIC asking Oracle ERP repeatedly if something changed, Oracle ERP pushes an event to OIC.
    Think of it as a notification mechanism.

---

Real-Life Example

Imagine you order food online.

    Without Business Events:
    You keep opening the app every minute.
        * Is my order ready?
        * Is my order ready?
        * Is my order ready?

    That's like a Scheduled Integration.
    With Business Events:
    The restaurant sends you a notification:
        Your order is ready.

That's exactly how Business Events work.

---

Event-Driven Architecture

            Employee Creates Invoice
                    │
                    ▼
            Oracle ERP
                    │
            Publishes Business Event
                    │
                    ▼
            Oracle Integration Cloud
                    │
                    ▼
            Process Event
                    │
                    ▼
            Notify External System

---

Why Business Events?

    Without Business Events:

            OIC
            │
            │ Every 5 Minutes
            ▼
            Call ERP REST API
            │
            ▼
            Any New Invoice?
            │
            ▼
            No

        This wastes:

        * API calls
        * Processing time
        * Network bandwidth

---

With Business Events:

        Invoice Created
            │
            ▼
        ERP Pushes Event
            │
            ▼
        OIC Triggered Immediately

    No polling required.

---

Common Oracle ERP Business Events

    Examples include:

    | Module      | Event                   |
    | ----------- | ----------------------- |
    | AP          | Invoice Created         |
    | AP          | Invoice Updated         |
    | AP          | Payment Created         |
    | GL          | Journal Posted          |
    | Procurement | Purchase Order Approved |
    | Procurement | Purchase Order Created  |
    | AR          | Customer Created        |
    | Inventory   | Item Created            |
    | HCM         | Employee Created        |

---

OIC Business Event Flow

            Oracle ERP
                │
                ▼
            Business Event
                │
                ▼
            OIC Trigger
                │
                ▼
            Mapper
                │
                ▼
            Invoke REST
                │
                ▼
            External System


---

Real Project Example 1
    AP Invoice Integration
Business Requirement:
    Whenever an AP Invoice is created in Oracle ERP,
Immediately send it to SAP.

Flow:

        Oracle ERP
        Invoice Created
                │
                ▼
        Business Event
                │
                ▼
        OIC Trigger
                │
                ▼
        Map Fields
                │
                ▼
        SAP REST API

    No scheduler.
    No polling.
    Instant processing.

---

Real Project Example 2
    Purchase Order Approval

Requirement:
    Whenever a Purchase Order is approved,
    Notify the Supplier.

Flow:
        PO Approved
            │
            ▼
        Business Event
            │
            ▼
        OIC
            │
            ▼
        Email

---

Business Event vs REST Trigger
        REST Trigger

        External System
            │
        Calls OIC
            ▼
        OIC

External system starts the integration.

---

Business Event

        Oracle ERP
            │
        Publishes Event
            ▼
        OIC

Oracle ERP starts the integration.

---

        Business Event vs Scheduled Integration

        | Scheduled       | Business Event   |
        | --------------- | ---------------- |
        | Polls ERP       | ERP pushes event |
        | Delayed         | Near real-time   |
        | Extra API calls | No polling       |
        | Time-based      | Event-based      |

---

        OIC Components

        | Component         | Purpose                |
        | ----------------- | ---------------------- |
        | ERP Cloud Adapter | Business Event Trigger |
        | Mapper            | Transform payload      |
        | REST Adapter      | Send data              |
        | Logger            | Debug                  |
        | Scope             | Error handling         |
        | Notification      | Email/Slack            |

---

Error Handling
    If the external system is unavailable:

        Business Event
            │
            ▼
        REST Call
            │
            ▼
        Failure
            │
            ▼
        Retry
            │
            ▼
        Still Failed?
            │
            ▼
        Notify Support
---

Best Practices

    * Keep the trigger integration lightweight.
    * Validate mandatory fields.
    * Use retry logic for transient failures.
    * Log event IDs for traceability.
    * Make downstream processing idempotent to avoid duplicate processing.

---

Interview Questions

    1. What is a Business Event?
    2. Why use Business Events instead of polling?
    3. Which adapter is used to subscribe to Business Events?
    4. Give a real-world Business Event example.
    5. What happens if the target system is unavailable?
    6. What is the difference between REST Trigger and Business Event Trigger?
    7. Can Business Events replace Scheduled Integrations?
    8. What are the advantages of Event-Driven Architecture?
    9. How do you prevent duplicate processing?
    10. When would you choose Scheduled Integration over Business Events?

---

Assignment
Design an OIC integration for this requirement:

    Whenever an AP Invoice is created in Oracle ERP:

        * Trigger OIC automatically.
        * Validate the payload.
        * Send the invoice to SAP.
        * If SAP is unavailable, retry three times.
        * If it still fails, send an email to the support team and log the error.

Draw the flow and identify the OIC activities you would use.
---

Quiz

    1. What is a Business Event, and how is it different from a Scheduled Integration?
    2. Why are Business Events more efficient than polling?
    3. Which OIC adapter is used to subscribe to Oracle ERP Business Events?
    4. A Purchase Order is approved in Oracle ERP. Explain the complete event flow until a supplier notification is sent.
    5. If the external system (SAP) is temporarily unavailable when a Business Event is received, how would you design the integration to ensure reliable processing?

This is a very common 3+ years OIC interview question. Let's go through it step by step.

---

Can Business Events be Enabled in Oracle ERP?   Yes.
        There are two ways depending on the business event.
            1. Using Oracle ERP Profile Options (for specific modules like Payables)
            2. Using the ERP Business Events REST API (recommended for events that support REST enablement) 

---

Method 1: Enable Through Profile Options
Some business events are controlled by ERP profile options instead of the Business Events REST API.

    For example:    Payables
    Profile Option: AP: Enable Business Events

If this profile option is set to Yes at the Site level, Payables events such as:

    * Invoice Created
    * Invoice Approved
    * Invoice Validated
    * Payment Created

    can be published. 
---

Method 2: Enable Using REST API (Recommended)
Oracle provides an ERP Business Events REST API to retrieve and enable supported business events. 

Step 1 – Get All Business Events
http    :   GET     /fscmRestApi/resources/11.13.18.05/erpBusinessEvents

Sample Response

    json
        {
            BusinessEventCode:ReceivablesInvoicePaid,
            EnabledFlag:false,
            ErpBusinessEventId:100100104470420
        }

---

Step 2 – Search for a Specific Event

http    GET :   /fscmRestApi/resources/11.13.18.05/erpBusinessEvents?q=BusinessEventCode=ReceivablesInvoicePaid
This returns the ErpBusinessEventId, which you'll use in the next step. 
---

Step 3 – Enable the Event

http    PATCH   :   /fscmRestApi/resources/11.13.18.05/erpBusinessEvents/{ErpBusinessEventId}
Body
    json
        {
        EnabledFlag: true
        }

After a successful PATCH request, the event is enabled. 

---

Required Security Privilege
The user invoking the REST API must have:

    Privilege Name
        Access FSCM Integration Rest Service

    Privilege Code
        FUN_FSCM_REST_SERVICE_ACCESS_INTEGRATION_PRIV

Without this privilege, the REST calls won't succeed. 

---

After Enabling the Event
The sequence is:
                        Oracle ERP
                            │
                            ▼
                        Business Event Enabled
                            │
                            ▼
                        Create OIC Integration
                            │
                            ▼
                        ERP Cloud Adapter
                            │
                            ▼
                        Select Business Event
                            │
                            ▼
                        Activate Integration

When you activate the integration, OIC automatically registers a subscription in Oracle ERP for the selected business event. 

---

How OIC Receives Business Events

                Business Event Occurs
                        │
                        ▼
                Oracle ERP
                        │
                Checks Event Enabled?
                        │
                        ▼
                YES
                        │
                        ▼
                Checks Active Subscription
                        │
                        ▼
                SOAP Callback to OIC
                        │
                        ▼
                OIC Integration Starts


    Oracle ERP verifies that:

        * The event is enabled.
        * A matching subscription exists.
        * The subscription is active.
If all conditions are met, Oracle ERP invokes the OIC endpoint. 

---

Real Project Example
Suppose the client says     :    Whenever a Journal is Posted, notify SAP.

You would:
        1. Check if Journal Posted is already enabled.
        2. If not, enable it using the ERP Business Events REST API (or the appropriate module configuration if applicable).
        3. Create an OIC integration with an ERP Cloud Adapter Business Event Trigger.
        4. Activate the integration.
        5. OIC creates the subscription automatically.
        6. When a journal is posted, Oracle ERP sends the event to OIC, and OIC invokes the SAP API.

---

Interview Answer

        Oracle ERP Business Events can be enabled either through module-specific profile options or through the ERP Business Events REST API, depending on the event. For REST-enabled events, I first retrieve the event using the `erpBusinessEvents` resource, identify the `ErpBusinessEventId`, and then send a PATCH request with `EnabledFlag=true`. After enabling the event, I configure an ERP Cloud Adapter Business Event Trigger in OIC. When the integration is activated, OIC automatically registers the subscription with Oracle ERP, allowing Oracle ERP to push events to OIC in near real time. 

This explanation is the level of detail typically expected from an experienced Oracle Integration Cloud developer in technical interviews.

        [1]: https://docs.oracle.com/en/cloud/paas/integration-cloud/erp-adapter/using-oracle-erp-cloud-adapter-oracle-integration-generation-2.pdf?utm_source=chatgpt.com Oracle® Cloud
        [2]: https://docs.oracle.com/en/cloud/saas/financials/26b/farfa/api-erp-business-events.html?utm_source=chatgpt.com REST API for Oracle Fusion Cloud Financials - ERP Business Events REST Endpoints
        [3]: https://docs.oracle.com/en/cloud/saas/financials/26b/farfa/Enable_Business_Events.html?utm_source=chatgpt.com Enable Business Events
        [4]: https://docs.oracle.com/en/cloud/paas/application-integration/erp-adapter/business-event-issues.html?utm_source=chatgpt.com Business Event Troubleshooting Issues

🎯 Today's Goal

By the end of Day 7, you should be able to explain Event-Driven Architecture confidently and justify when to use Business Events instead of Scheduled or REST-based integrations. This is a high-frequency interview topic for Oracle Integration Cloud developers working with Oracle ERP Cloud.

