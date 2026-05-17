# METHODOLOGY

---

## 0. RULES

* Do not test everything. Focus on impact.
* If no progress in 20 minutes, change direction.
* Always ask: can this lead to account takeover or remote code execution.

---

## 1. TARGET TRIAGE

### Identify high-value areas first

* Authentication (login, reset, register)
* Account settings
* File upload functionality
* Payments and transactions
* Admin panels

---

### Immediate attack indicators

* `user_id`, `account_id` → test for IDOR
* File upload → attempt execution
* URL input → test for SSRF
* Multi-step workflows → test for logic flaws

---

## 2. ATTACK SURFACE EXPANSION

### JavaScript analysis (primary source)

* Extract endpoints, parameters, hidden functionality, tokens
* Identify internal logic and unused features

---

### Application mapping

* Crawl application
* Identify:

  * Endpoints
  * Parameters
  * Forms
  * Hidden functionality

---

### Parameter discovery

* Identify all inputs:

  * GET / POST
  * Headers
  * Cookies

* Find:

  * Hidden parameters
  * Unused parameters
  * Parameter pollution opportunities

---

### Avoid

* Blind brute force without context
* Low-value static content

---

## 3. INPUT → DATA FLOW → EXPLOIT

### For every input

1. Where does it go
2. What processes it
3. What can be influenced

---

### Data flow mapping

* Reflection → XSS
* Database → SQL Injection
* File system → Path Traversal
* System calls → Command Injection
* External requests → SSRF

---

### Rule

If input reaches a sensitive component, prioritize testing it.

---

## 4. ATTACK PRIORITY

### 1. Authorization

* Test access to other users’ data
* Modify identifiers
* Direct access to restricted functionality

Focus:

* IDOR
* Privilege escalation

---

### 2. Authentication

* Test password reset logic
* Test token reuse and predictability
* Identify missing validation

Goal:

* Account takeover

---

### 3. Business logic

* Identify workflow weaknesses
* Test step skipping and state manipulation

---

### 4. File handling and SSRF

* File upload execution
* Internal network access

---

### 5. Input-based vulnerabilities

* XSS
* SQL injection
* SSTI

---

## 5. EXPLOITATION PROCESS

### Step 1 — Confirm

* Verify the behavior is controllable

---

### Step 2 — Analyze

* Identify context and processing logic

---

### Step 3 — Bypass

* Modify payload structure
* Use alternate encodings
* Break context

---

### Step 4 — Escalate

* Access other users
* Gain higher privileges
* Reach code execution

---

## 6. CHAINING

### Core question

What can this vulnerability lead to

---

### Typical paths

* IDOR → account modification → account takeover
* SSRF → internal access → further exploitation
* File upload → stored XSS → admin interaction → escalation

---

## 7. MODERN ATTACK SURFACE

* Request smuggling
* Cache poisoning
* JWT weaknesses
* Race conditions
* Prototype pollution

---

## 8. TOOL USAGE

### Workflow

1. Capture traffic
2. Test manually
3. Fuzz selectively
4. Automate when needed

---

### Rule

Tools support decisions. They do not make decisions.

---

## 9. EFFICIENCY

* Prioritize high-impact areas
* Avoid spending too long on one vector
* Move quickly between attack paths

---

## 10. VERIFICATION

* Identify what changed in the response
* Confirm consistency
* Eliminate false positives

---

