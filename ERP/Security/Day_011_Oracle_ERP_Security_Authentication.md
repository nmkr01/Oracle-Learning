# 📘 Day 11 – Oracle ERP Security & Authentication in Oracle Integration Cloud (OIC)

Welcome to **Day 11**. Security is one of the most important topics in Oracle Integration Cloud. Many interview questions focus on authentication, authorization, OAuth, JWT, certificates, and secure connections.

---

# 🎯 Learning Objectives

By the end of today, you'll understand:

* Oracle Identity Domains
* Authentication vs Authorization
* Basic Authentication
* OAuth 2.0
* JWT Bearer Token
* Client Credentials Flow
* Authorization Code Flow
* WS-Security
* Certificates (JKS & PKCS12)
* Security in OIC Connections
* Real Project Scenarios

---

# Why Security?

Every request sent to Oracle ERP must be authenticated.

Without authentication:

```text
OIC
 │
 ▼
ERP
 │
 ▼
❌ Unauthorized (401)
```

With authentication:

```text
OIC
 │
 ▼
Authentication
 │
 ▼
ERP
 │
 ▼
✅ Request Accepted
```

---

# Authentication vs Authorization

Authentication

> **Who are you?**

Authorization

> **What are you allowed to access?**

Example:

```text
Login
 │
 ▼
Authentication
 │
 ▼
Permission Check
 │
 ▼
Authorization
```

---

# Oracle Identity Domain

Oracle ERP Cloud uses **Identity Domains** to manage:

* Users
* Groups
* Roles
* OAuth Clients
* Applications

```text
Oracle Identity Domain
        │
        ├── Users
        ├── Roles
        ├── OAuth Clients
        ├── Certificates
        └── Applications
```

---

# Authentication Methods

Oracle ERP supports:

| Method               | Usage                                               |
| -------------------- | --------------------------------------------------- |
| Basic Authentication | Simple integrations (less preferred for production) |
| OAuth 2.0            | Recommended for REST APIs                           |
| JWT Bearer           | Server-to-server integrations                       |
| WS-Security          | SOAP Web Services                                   |
| Mutual TLS (mTLS)    | High-security integrations                          |

---

# 1. Basic Authentication

```text
Username
Password
```

Flow:

```text
OIC
 │
 ▼
Username + Password
 │
 ▼
ERP
```

Pros:

* Easy to configure.

Cons:

* Password management.
* Less secure than OAuth.

---

# 2. OAuth 2.0

Most Oracle ERP REST APIs use OAuth.

Flow:

```text
OIC
 │
 ▼
Request Access Token
 │
 ▼
Identity Domain
 │
 ▼
Access Token
 │
 ▼
ERP REST API
```

---

# OAuth Components

* Client ID
* Client Secret
* Access Token
* Refresh Token (where applicable)
* Authorization Server

---

# OAuth Client Credentials Flow

Used for:

* OIC
* Backend services
* Server-to-server integrations

Flow:

```text
OIC
 │
 ▼
Client ID
Client Secret
 │
 ▼
Identity Domain
 │
 ▼
Access Token
 │
 ▼
ERP REST API
```

No user interaction is required.

---

# Authorization Code Flow

Used for:

* Browser applications
* User login
* Interactive applications

Flow:

```text
User
 │
 ▼
Login Screen
 │
 ▼
Authorization Code
 │
 ▼
Access Token
 │
 ▼
ERP
```

---

# JWT Bearer Token

JWT stands for **JSON Web Token**.

Structure:

```text
Header

↓

Payload

↓

Signature
```

Flow:

```text
OIC
 │
 ▼
Generate JWT
 │
 ▼
Sign Using Private Key
 │
 ▼
Identity Domain
 │
 ▼
Access Token
 │
 ▼
ERP
```

Commonly used in enterprise integrations.

---

# WS-Security (SOAP)

SOAP services use WS-Security.

Header contains:

* Username Token
* Timestamp
* Signature
* Certificate

Example:

```xml
<wsse:Security>

<UsernameToken>

<Timestamp>

<Signature>

</wsse:Security>
```

---

# Certificates

Certificates establish trust.

Common formats:

### JKS

Java KeyStore

Used by:

* OIC
* Java Applications

---

### PKCS12 (.p12)

Contains:

* Private Key
* Public Certificate

Used for:

* OAuth JWT
* Mutual TLS

---

# Certificate Flow

```text
Private Key
      │
      ▼
Generate Signature
      │
      ▼
Identity Domain
      │
      ▼
Verify Signature
      │
      ▼
Issue Token
```

---

# OIC Connection Security

When creating a connection:

```text
Connection
     │
     ▼
Security Policy
```

Examples:

* Basic Authentication
* OAuth Client Credentials
* OAuth JWT
* WS-Security Username Token
* OAuth Authorization Code

---

# Real Project Example 1

ERP REST Integration

```text
OIC
 │
 ▼
OAuth Client Credentials
 │
 ▼
Access Token
 │
 ▼
ERP REST API
 │
 ▼
JSON Response
```

---

# Real Project Example 2

SOAP ESS Job

```text
OIC
 │
 ▼
SOAP Adapter
 │
 ▼
WS-Security
 │
 ▼
submitESSJob
```

---

# Real Project Example 3

JWT Authentication

```text
OIC
 │
 ▼
Private Key (.p12)
 │
 ▼
Generate JWT
 │
 ▼
Identity Domain
 │
 ▼
Access Token
 │
 ▼
ERP REST API
```

---

# Authentication Methods Comparison

| Method      | Security  | Use Case          |
| ----------- | --------- | ----------------- |
| Basic       | Medium    | Simple testing    |
| OAuth       | High      | REST APIs         |
| JWT         | Very High | Server-to-server  |
| WS-Security | High      | SOAP Services     |
| mTLS        | Very High | Financial/Banking |

---

# Common Errors

| Error                | Meaning               |
| -------------------- | --------------------- |
| 401 Unauthorized     | Invalid credentials   |
| 403 Forbidden        | User lacks permission |
| Invalid Token        | Token expired         |
| Invalid Signature    | JWT/certificate issue |
| SSL Handshake Failed | Certificate mismatch  |

---

# Best Practices

* Prefer OAuth over Basic Authentication.
* Store credentials securely in OIC Connections.
* Rotate client secrets and certificates regularly.
* Use least-privilege access.
* Never hardcode passwords or secrets.
* Monitor token expiry and renew automatically.

---

# Interview Questions

1. What is the difference between Authentication and Authorization?
2. Why is OAuth preferred over Basic Authentication?
3. Explain the Client Credentials flow.
4. What is JWT, and when would you use it?
5. What is the purpose of WS-Security?
6. What is the difference between JKS and PKCS12?
7. What causes a 401 vs a 403 error?
8. How do you configure OAuth in an OIC connection?
9. What is an Oracle Identity Domain?
10. Which authentication method would you choose for REST and SOAP integrations?

---

# Assignment

Design an OIC integration that:

* Uses OAuth Client Credentials to call an Oracle ERP REST API.
* Retrieves an access token.
* Calls the `/invoices` REST endpoint.
* Handles expired tokens gracefully.
* Logs authentication failures.
* Sends an email if authentication repeatedly fails.

---

# Quiz

### 1. What is the difference between Authentication and Authorization?

### 2. Why is OAuth Client Credentials commonly used for OIC integrations?

### 3. Explain the JWT authentication flow.

### 4. What is the difference between JKS and PKCS12 certificates?

### 5. An OIC integration receives a **401 Unauthorized** response from Oracle ERP. What steps would you take to troubleshoot and resolve the issue?

---

# 📁 Save Today's Notes

```text
D:\Nysh_Work\Oracle Integration Architect Roadmap\
└── 01_ERP
    └── 03_ERP_Integration
        └── Day_011_Oracle_ERP_Security_Authentication.md
```

---

## 🚀 Tomorrow (Day 12)

We'll move into **Oracle ERP Business Objects & Integration Patterns**, where you'll learn:

* Oracle ERP business objects
* Parent-child relationships
* AP, AR, GL, Procurement object models
* Integration patterns (request-response, publish-subscribe, batch, event-driven)
* Idempotency
* Correlation IDs
* Real enterprise architecture scenarios

This will tie together everything you've learned in Days 1–11 and prepare you for designing end-to-end enterprise OIC integrations.
