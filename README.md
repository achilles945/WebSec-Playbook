# WebSec-Playbook
---


## 1. Recon & Attack Surface Expansion

### Objective

Expand a single web application into its full attack surface and identify all reachable inputs, endpoints, and hidden functionality.

---

### Phase 1 — Domain & Subdomain Discovery

* Identify main domain

* Enumerate subdomains:

  * Passive (certs, archives)
  * Active (bruteforce)
  * Tool : Subfinder

* Look for:

  * dev, staging, test environments
  * admin panels
  * old / unused subdomains

---

### Phase 2 — Endpoint & Content Discovery

* Crawl application (Burp / browser)

* Extract:

  * URLs
  * Parameters
  * API endpoints

* Directory & file discovery:

  * Hidden endpoints
  * Backup files (.bak, .old, .zip)
  * Config files
  * Tool : FUFF,Gobuster,Dirbuster

* Check:

  * HTTP methods (GET, POST, PUT, DELETE)

---

### Phase 3 — JavaScript Analysis

* Collect all JS files

* Look for:

  * API endpoints
  * Hidden routes
  * Hardcoded secrets
  * Tokens / keys
  * Internal URLs

* Extract:

  * Parameters
  * Function names
  * Interesting logic

---

### Phase 4 — Parameter Discovery

* Identify all inputs:

  * GET / POST
  * JSON
  * Headers
  * Cookies

* Find hidden parameters:

  * Fuzz parameters
  * Analyze JS

* Test:

  * Parameter pollution
  * Unused parameters

---

### Phase 5 — API Recon

* Identify API endpoints:

  * `/api/`, `/v1/`, `/v2/`

* Test:

  * Different HTTP methods
  * Hidden fields
  * Mass assignment

* Map:

  * Request → Response structure

---

### Phase 6 — Authentication Surface Mapping

* Identify:

  * Login
  * Register
  * Password reset
  * OTP / 2FA

* Map:

  * Token flow
  * Session handling
  * Cookies

---

### Phase 7 — File & Upload Surface

* Identify:

  * File upload functionality
  * File retrieval endpoints

* Check:

  * File types allowed
  * Storage location
  * Access patterns

---

### Phase 8 — Data Flow Mapping

For every input, identify where it goes:

* Reflection → XSS
* Database → SQLi
* File system → Path Traversal
* System calls → Command Injection
* Templates → SSTI
* External request → SSRF

---

### Phase 9 — Technology & Behavior Analysis

* Identify:

  * Frameworks
  * Backend language
  * WAF / CDN

* Observe:

  * Error messages
  * Response patterns
  * Redirects

---

### Phase 10 — Hidden Attack Surface

* Check:

  * Backup domains
  * Old endpoints
  * Deprecated APIs

* Look for:

  * Admin panels
  * Debug endpoints
  * Internal tools

---

### Output

* Domains & subdomains
* Endpoints & APIs
* Parameters (all types)
* Authentication flows
* File upload points
* Hidden functionality
* Data flow mapping

---

## 2. Input Surface Mapping

### Objective

Identify all user-controlled inputs.

### Methodology

**Identify Inputs**

* URL parameters
* POST / JSON data
* Headers
* Cookies
* File uploads

**Trace Data Flow**

* Reflection → XSS
* Database interaction → SQLi
* System calls → Command Injection
* Template rendering → SSTI

**Micro-Tactics**

* Inject test string → check response
* Check:

  * View source (server-side)
  * DevTools (DOM)
* Identify context (HTML / Attribute / JS)

**API Awareness**

* JSON parameters
* Hidden fields
* Versioned APIs (`/v1/`, `/v2/`)
* Mass assignment

---

## 3. Input-Based Vulnerabilities

### 3.1 XSS

**Methodology**

* Inject payload → confirm reflection
* Identify context → craft payload
* Test execution in browser

**Detection**

* Script execution vs reflection

---

### 3.2 SQL Injection

**Methodology**

* Inject test payloads
* Compare responses
* Try boolean / time-based
* Tool : sqlmap

**Detection**

* Errors
* Response differences
* Time delays

---

### 3.3 SSTI

**Methodology**

* Detect template usage
* Inject expressions
* Confirm evaluation

---

### 3.4 Command Injection

**Methodology**

* Inject OS commands
* Observe output / delay

---

### 3.5 File Upload

**Methodology**

* Upload files
* Try execution / bypass filters

---

### 3.6 Path Traversal

**Methodology**

* Manipulate file paths
* Access sensitive files

---

## 4. Authentication Testing

### Objective

Break authentication mechanisms.

### Methodology

**Login Testing**

* Weak credentials
* Brute force

**Session Testing**

* Token predictability
* Session fixation

**Password Reset**

* Token reuse
* Predictable tokens

**Bypass**

* Direct access to protected resources

---

## 5. Authorization Testing

### Objective

Access unauthorized data or functionality.

### Methodology

**IDOR**

* Modify object identifiers

**Vertical Escalation**

* Access admin functionality

**Horizontal Escalation**

* Access other user data

---

## 6. Server-Side Vulnerabilities

### SSRF

* Identify URL-fetching features
* Test internal endpoints
* Check cloud metadata

### XXE

* Inject external entities
* Read files / trigger SSRF

---

## 7. Business Logic Testing

### Objective

Break application workflows.

### Methodology

* Skip steps
* Repeat actions
* Manipulate sequences
* Abuse functionality

---

## 8. Vulnerability Chaining

### Objective

Combine multiple weaknesses.

### Examples

* IDOR → Account Takeover
* SSRF → Internal Access → RCE
* File Upload → Remote Execution

---

## Core Mental Model

1. Expand attack surface
2. Map inputs
3. Understand data flow
4. Execute targeted attacks
5. Chain vulnerabilities

---
