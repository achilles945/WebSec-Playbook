# Race Conditions

Race condition vulnerabilities occur when an application processes multiple requests simultaneously and security checks or state changes do not happen atomically. Attackers exploit timing issues by sending concurrent requests to cause unintended behavior.

Race conditions can allow:

* Authentication bypass
* Multiple coupon redemption
* Double spending
* Privilege escalation
* Rate limit bypass
* Business logic abuse

Race conditions commonly occur in:

* Payment functionality
* Coupon systems
* OTP verification
* Account registration
* Balance transfers
* Inventory management

---

## Detection

Race condition detection involves identifying functionality where multiple requests processed simultaneously can cause inconsistent application state.

### Common Targets

* Payments
* Coupon redemption
* Gift cards
* OTP verification
* Password reset
* Rate limits
* Wallet/balance systems
* Inventory purchase
* Account linking

### Signs of Race Conditions

* State changes after request processing
* Delayed backend updates
* Lack of locking mechanisms
* Duplicate actions succeeding
* Inconsistent application behavior

### Notes

* Race conditions usually require concurrent requests.
* Business logic functionality is commonly affected.
* Applications using asynchronous processing are higher risk.

---

## Single Endpoint Race Conditions

Occurs when sending multiple requests simultaneously to the same endpoint causes unintended behavior.

### Example

A coupon endpoint:

```http
POST /redeem-coupon HTTP/1.1
```

If multiple requests are sent simultaneously:

* Coupon may be redeemed multiple times
* Balance may increase repeatedly

### Notes

* Most common race condition type.
* Often affects financial functionality.
* Burp Repeater group send is useful for testing.

---

## Multi-Step Race Conditions

Occurs when application workflows rely on multiple sequential actions and backend state updates are delayed.

### Example Flow

1. Add item to cart
2. Apply coupon
3. Complete payment

Simultaneous requests may:

* Reuse discounts
* Bypass purchase restrictions
* Trigger inconsistent states

### Notes

* Common in checkout workflows.
* State synchronization issues are often exploitable.
* Applications may validate only initial request state.

---

## Limit Overrun Race Conditions

Occurs when applications enforce limits incorrectly under concurrent execution.

### Common Examples

* OTP brute force
* Coupon redemption
* Referral abuse
* Free trial abuse
* Vote manipulation

### Example

Application allows:

```text
Maximum 1 coupon redemption
```

Attacker sends:

```text
20 concurrent requests
```

Multiple requests may succeed before limit updates.

### Notes

* Common in rate limiting logic.
* Backend validation timing is critical.
* Often leads to business logic abuse.

---

## TOCTOU (Time-of-Check to Time-of-Use)

Occurs when security checks happen before resource usage and application state changes between check and execution.

### Example

1. Application checks user balance
2. Application processes transfer
3. Balance updates later

Multiple transfers may execute before balance updates.

### Notes

* Common in banking/payment functionality.
* State changes must happen atomically.
* Often difficult to detect manually.

---

## Race Conditions in OTP Systems

OTP systems commonly suffer from timing issues.

### Common Issues

* OTP reuse
* OTP brute force
* Multiple OTP validations
* Expired OTP acceptance

### Example

Simultaneous OTP submissions:

```http
POST /verify-otp HTTP/1.1
```

Multiple requests may validate before OTP invalidation occurs.

### Notes

* Frequently affects MFA implementations.
* Short OTP expiration alone is insufficient protection.

---

## Race Conditions in File Uploads

Occurs when uploaded files are validated and processed separately.

### Attack Flow

1. Upload malicious file
2. Replace file before validation completes
3. Trigger execution

### Notes

* Common in antivirus scanning workflows.
* Temporary file handling may be vulnerable.

---

## Race Conditions in Account Registration

Occurs when uniqueness checks are not atomic.

### Examples

* Duplicate usernames
* Duplicate emails
* Multiple free trial creation

### Notes

* Simultaneous registration requests are required.
* Often affects referral systems.

---

## Race Conditions in Rate Limiting

Occurs when rate limits update after request processing.

### Attack Flow

1. Send many concurrent requests
2. Requests process before counters update
3. Rate limit bypass occurs

### Notes

* Common in login endpoints.
* Can assist brute force attacks.
* Often combined with credential stuffing.

---

## Testing Methodology

### Basic Workflow

1. Identify state-changing functionality
2. Capture request in Burp
3. Send multiple concurrent requests
4. Observe inconsistent behavior

### Burp Repeater Group Send

Burp Suite supports parallel request sending.

Useful for:

* Coupon testing
* OTP testing
* Payment races
* Limit overrun testing

### Turbo Intruder

Useful for:

* High-speed concurrent requests
* Advanced race testing
* Timing attacks

### Notes

* Single requests often will not expose race conditions.
* Timing differences are critical.
* Repeat testing multiple times.

---

## Common Indicators

* Duplicate transactions
* Multiple successful responses
* Negative balances
* Duplicate coupon usage
* Multiple OTP acceptance
* Inconsistent account state

---

## Tools

### Burp Suite Professional

* Repeater
* Turbo Intruder
* Logger

### Command Line

```bash
ffuf
curl
```

Turbo Intruder is commonly used for advanced race condition testing.

---

## Prevention

* Use atomic operations
* Implement locking mechanisms
* Synchronize state changes
* Validate actions server-side
* Apply proper transaction handling
* Invalidate OTPs immediately
* Enforce backend concurrency controls
