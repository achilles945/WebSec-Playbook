# WebSec Playbook

## Testing Flow

```text
Recon
 ↓
Configuration
 ↓
Authentication
 ↓
Session
 ↓
Authorization
 ↓
Input Validation
 ↓
Business Logic
 ↓
Client-Side
 ↓
Chaining
```

---

## Recon

### Discover

* Subdomains
* Directories
* Files
* APIs
* Parameters
* JavaScript Endpoints
* GraphQL
* WebSockets

### Map

* Authentication
* Session Management
* Authorization Boundaries
* File Uploads
* Administrative Functionality
* Payment Functionality

---

## Configuration

### Review

* HTTP Methods
* Security Headers
* TLS Configuration
* CORS Configuration
* Admin Panels
* Debug Functionality
* Source Code Exposure
* Backup Files
* Cloud Storage Exposure
* Default Credentials

---

## Authentication

### Test

* Registration
* Login
* Password Reset
* Password Change
* MFA
* OAuth
* OpenID Connect
* SSO

### Focus

* Account Takeover
* Authentication Bypass
* User Enumeration

---

## Session

### Test

* Session Fixation
* Session Timeout
* Session Invalidation
* Session Reuse
* JWT Security
* Cookie Security
* CSRF Protection

### Focus

* Session Hijacking
* Session Abuse

---

## Authorization

### Test

* Horizontal Access Control
* Vertical Access Control
* IDOR
* Forced Browsing
* Function-Level Authorization
* Object-Level Authorization

### Focus

* Privilege Escalation
* Unauthorized Access

---

## Input Validation

### Map

```text
Input
 ↓
Processing
 ↓
Sensitive Component
```

### Test

| Component        | Vulnerability     |
| ---------------- | ----------------- |
| Browser          | XSS               |
| Database         | SQL Injection     |
| Template Engine  | SSTI              |
| Operating System | Command Injection |
| XML Parser       | XXE               |
| URL Fetcher      | SSRF              |
| File System      | Path Traversal    |

### Modern Attacks

* Host Header Injection
* HTTP Parameter Pollution
* Request Smuggling
* Cache Poisoning
* Cache Deception
* Insecure Deserialization

---

## Business Logic

### Test

* Workflow Bypass
* Step Skipping
* State Manipulation
* Race Conditions
* Rate Limiting
* Payments
* Coupons
* Referrals
* Transactions

---

## Client-Side

### Test

* DOM XSS
* Open Redirect
* Prototype Pollution
* Web Messaging
* Browser Storage
* WebSockets
* Clickjacking

---

## Chaining

* IDOR → Account Takeover
* SSRF → Internal Access
* File Upload → RCE
* XSS → Session Theft
* Request Smuggling → Cache Poisoning
* SSRF → Cloud Metadata → Credential Theft

---

## Priorities

```text
Account Takeover
 ↓
Privilege Escalation
 ↓
Sensitive Data Exposure
 ↓
Remote Code Execution
```

---

## Core Mental Model

```text
Attack Surface
 ↓
Input
 ↓
Data Flow
 ↓
Trust Boundary
 ↓
Exploit
 ↓
Impact
```
