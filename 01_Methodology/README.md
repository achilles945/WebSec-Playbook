# METHODOLOGY


# 1. RECONNAISSANCE

## Objective

Map the attack surface.

Do not begin exploitation until the application is understood.

---

## Subdomain Discovery

Identify:

* Development environments
* Staging environments
* Administrative interfaces
* API hosts

---

## Content Discovery

Perform:

* Directory fuzzing
* File fuzzing
* Backup file discovery

Check for:

* Directory listing
* Exposed files
* Hidden functionality

---

## JavaScript Analysis

Primary source of information.

Extract:

* Endpoints
* Parameters
* API routes
* Hidden functionality
* Tokens
* Internal references

---

## Application Mapping

Identify:

* Authentication functionality
* Session functionality
* Authorization boundaries
* File upload functionality
* Administrative functionality
* Payment functionality

---

# 2. CONFIGURATION TESTING

## Objective

Identify security misconfigurations.

---

## Review Server Configuration

Check:

* HTTP methods
* Security headers
* TLS configuration
* CORS configuration

---

## Review Exposed Functionality

Check for:

* Admin panels
* Debug functionality
* Source code exposure
* Backup files
* Cloud storage exposure

---

## Review Error Handling

Identify:

* Stack traces
* Verbose errors
* Internal information disclosure

---

# 3. AUTHENTICATION TESTING

## Objective

Identify weaknesses in identity verification.

---

## Test Registration

Review:

* Account creation
* Email verification
* User enumeration

---

## Test Login

Review:

* Credential validation
* Lockout controls
* Authentication bypasses

---

## Test Password Management

Review:

* Password reset
* Password change
* Recovery workflows

---

## Test Federated Authentication

Review:

* OAuth
* OpenID Connect
* SSO

---

# 4. SESSION TESTING

## Objective

Determine how authentication state is maintained.

---

## Review Session Tokens

Identify:

* Cookies
* JWTs
* Session identifiers

---

## Test Session Controls

Review:

* Session fixation
* Session timeout
* Session invalidation
* Concurrent sessions

---

## Test CSRF Protections

Review:

* State-changing requests
* CSRF tokens
* SameSite protections

---

# 5. AUTHORIZATION TESTING

## Objective

Determine whether access controls are enforced correctly.

---

## Horizontal Authorization

Can one user access another user's resources?

Focus:

* IDs
* UUIDs
* References

---

## Vertical Authorization

Can lower-privileged users access higher-privileged functionality?

Focus:

* Administrative actions
* Restricted endpoints
* Sensitive operations

---

## Function-Level Authorization

Test:

* Hidden endpoints
* API functions
* Administrative actions

---

# 6. INPUT TESTING

## Objective

Identify whether user-controlled input reaches sensitive components.

---

## Input Mapping

For every input determine:

```text
Input
 ↓
Processing
 ↓
Sensitive Component
```

---

## Sensitive Components

Database:

* SQL Injection
* NoSQL Injection

Browser:

* XSS

Operating System:

* Command Injection

Template Engines:

* SSTI

XML Parsers:

* XXE

External Requests:

* SSRF

File Systems:

* Path Traversal
* File Inclusion

---

## Modern Web Attacks

Review:

* Host Header Injection
* HTTP Parameter Pollution
* Request Smuggling
* Cache Poisoning
* Cache Deception
* Insecure Deserialization

---

# 7. BUSINESS LOGIC TESTING

## Objective

Identify flaws in application workflows.

---

## Workflow Analysis

Identify:

* Multi-step processes
* State transitions
* Approval flows

---

## Test Logic Controls

Review:

* Step skipping
* State manipulation
* Race conditions
* Rate limiting

---

## Financial Logic

Review:

* Payments
* Coupons
* Discounts
* Referral systems

---

# 8. CLIENT-SIDE TESTING

## Objective

Identify vulnerabilities executed in the browser.

---

## Review Client-Side Logic

Identify:

* JavaScript security controls
* Hidden functionality
* Client-side validation

---

## Test Browser Features

Review:

* Browser storage
* Web messaging
* WebSockets
* CORS

---

## Test Client-Side Vulnerabilities

Review:

* DOM XSS
* Prototype Pollution
* Open Redirects
* Clickjacking

---

# 9. EXPLOITATION & CHAINING

## Confirm

Verify the vulnerability is controllable.

---

## Analyze

Determine:

* Trust boundary crossed
* Security control bypassed
* Exploitation requirements

---

## Escalate

Attempt:

* Account Takeover
* Privilege Escalation
* Internal Access
* Code Execution

---

## Chain Vulnerabilities

Combine findings where possible.

Examples:

```text
IDOR
 ↓
Account Modification
 ↓
Account Takeover
```

```text
SSRF
 ↓
Internal Access
 ↓
RCE
```

---

# 10. VERIFICATION

## Validate Findings

Confirm:

* Consistent reproduction
* Reliable impact
* No false positives

---

## Document Findings

Record:

* Attack path
* Impact
* Evidence
* Reproduction steps

```
```
