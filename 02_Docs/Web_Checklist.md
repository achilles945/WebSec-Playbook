# Web Pentesting Checklist

# Recon

## Subdomain Enumeration

- Development subdomains
- Staging subdomains
- Admin panels
- API subdomains
- Internal applications
- Forgotten domains
- Subdomain takeover

---

## Directory and File Fuzzing

- Hidden directories
- Hidden endpoints
- Backup files
- Debug files
- Configuration files
- Admin panels
- API routes
- Old API versions

---

## Directory Listing

### Common Targets

```text
/uploads/
/backup/
/assets/
/logs/
/images/
```

### Check For

- Exposed files
- Backup archives
- Uploaded files
- Source code exposure
- Log exposure

---

## Technology Fingerprinting

- Backend framework
- Frontend framework
- CMS
- WAF
- Reverse proxy
- API technologies
- Cloud infrastructure

---

## JavaScript Analysis

- Hidden endpoints
- API routes
- Tokens
- API keys
- Secrets
- Internal URLs
- GraphQL queries
- Hidden functionality

---

## Metafiles

- `robots.txt`
- `sitemap.xml`
- `security.txt`
- `humans.txt`

Check for:
- hidden paths
- admin routes
- internal URLs
- sensitive information

---

## Endpoint Mapping

- Authentication endpoints
- Password reset functionality
- File upload functionality
- Payment functionality
- Search functionality
- APIs
- GraphQL
- WebSockets
- Admin functionality

---

## Parameter Discovery

- GET parameters
- POST parameters
- JSON parameters
- Headers
- Cookies
- Multipart parameters
- Hidden parameters
- HTTP parameter pollution

---

# Authentication Testing

## Login Testing

- Username enumeration
- Weak passwords
- Missing rate limiting
- MFA bypass
- Default credentials
- CAPTCHA bypass
- Response manipulation

---

## Password Reset Testing

- Host header poisoning
- Token leakage
- Token reuse
- Token predictability
- User enumeration
- Expired token reuse
- Weak reset workflows

---

## Session Testing

- Session fixation
- Session reuse
- Logout invalidation
- Session expiration
- Predictable sessions
- Concurrent sessions
- Session hijacking

---

## Cookie Testing

- Missing `HttpOnly`
- Missing `Secure`
- Missing `SameSite`
- Persistent cookies
- Predictable cookie values

---

## JWT Testing

- `alg:none`
- Weak HMAC secrets
- Signature validation bypass
- Key confusion
- Expiration validation
- Role manipulation
- JWT information disclosure

---

## 2FA Testing

- OTP brute force
- OTP reuse
- Response manipulation
- Status code manipulation
- Null OTP bypass
- Session-based bypass

---

# Authorization Testing

## Horizontal Privilege Escalation

- IDOR
- Unauthorized object access
- Parameter tampering
- Access to other users’ data
- Access to other users’ actions

---

## Vertical Privilege Escalation

- Admin functionality access
- Hidden endpoints
- Function-level authorization
- Role validation bypass
- Forced browsing

---

## Forced Browsing

### Common Targets

```text
/admin
/internal
/manage
/debug
/dashboard
```

### Check For

- Hidden functionality
- Unauthenticated access
- Weak authorization
- Internal applications

---

## HTTP Method Testing

### Test

```http
GET
POST
PUT
PATCH
DELETE
OPTIONS
TRACE
```

### Check For

- Unauthorized methods
- Hidden functionality
- Method override
- Access control bypass

---

# Injection Testing

# XSS

## Reflected XSS

- Search functionality
- URL parameters
- Error messages
- Headers

---

## Stored XSS

- Comments
- Profiles
- Ticket systems
- Chat systems
- File names

---

## DOM XSS

### Sources

```javascript
location
document.URL
document.referrer
postMessage
```

### Sinks

```javascript
innerHTML
eval
document.write
```

---

## SQL Injection

- Error-based SQLi
- Boolean SQLi
- Time-based SQLi
- UNION injection
- Header-based SQLi
- Cookie-based SQLi

---

## SSTI

- Email templates
- PDF generators
- Template rendering
- Error pages

---

## Command Injection

- Ping functionality
- File processing
- Diagnostic tools
- OS command execution

---

## XXE

- XML APIs
- SOAP APIs
- SVG upload
- XML parsers

---

## SSRF

### Targets

```text
127.0.0.1
localhost
169.254.169.254
```

### Common Sources

- URL import functionality
- Webhooks
- PDF generators
- Image fetchers

---

## Path Traversal

- File download functionality
- Image retrieval
- Export functionality
- Template loading

---

## LDAP Injection

- Login forms
- Search functionality
- Directory services

---

## XPath Injection

- XML-based applications
- Authentication functionality

---

## Host Header Injection

- Password reset poisoning
- Cache poisoning
- Routing-based SSRF
- Host override headers
- Virtual host brute forcing

---

# File Upload Testing

- Extension validation
- MIME validation
- Double extensions
- SVG upload
- File overwrite
- Path traversal
- File execution
- Large file upload
- Polyglot files

---

# Business Logic Testing

## Workflow Testing

- Step skipping
- State manipulation
- Workflow bypass
- Replay attacks
- Transaction abuse

---

## Payment Testing

- Price manipulation
- Quantity manipulation
- Currency manipulation
- Payment bypass
- Duplicate transactions

---

## Coupon and Referral Testing

- Multiple redemption
- Referral abuse
- Token reuse
- Race conditions

---

## Race Conditions

### Common Targets

- Coupon redemption
- Wallet transfers
- OTP verification
- Balance updates
- Stock purchases

---

## Rate Limiting

- Login brute force
- OTP brute force
- Password reset abuse
- API abuse
- Race condition bypass

---

# API Testing

## API Recon

- Hidden APIs
- Deprecated APIs
- Internal APIs
- GraphQL endpoints
- Swagger/OpenAPI

---

## Method Testing

- Unauthorized methods
- Hidden methods
- Admin functionality

---

## Content-Type Testing

```http
application/json
application/xml
multipart/form-data
application/x-www-form-urlencoded
```

Check for:
- parser differences
- validation bypass
- XXE exposure

---

## Mass Assignment

- Hidden parameters
- Role manipulation
- Internal flags
- Privilege escalation

---

## Excessive Data Exposure

- Hidden fields
- Sensitive attributes
- Internal properties
- Debug information

---

## GraphQL Testing

- Introspection
- Nested query abuse
- Authorization issues
- Excessive data exposure
- Batch queries

---

## WebSocket Testing

- Authentication
- Authorization
- Message tampering
- Subscription abuse
- Hidden actions

---

# Advanced HTTP Attacks

## HTTP Request Smuggling

- CL.TE
- TE.CL
- TE.TE
- HTTP desynchronization

---

## Web Cache Poisoning

- Reflected headers
- Unkeyed inputs
- Cache key behavior
- Cache poisoning gadgets

---

## Web Cache Deception

- Static extension confusion
- Cache rule abuse
- Sensitive response caching

---

# Security Misconfiguration

- CORS
- Verbose errors
- Debug endpoints
- Default credentials
- Directory listing
- Exposed `.git`
- Exposed backups
- Missing security headers

---

# Sensitive Data Exposure

- API keys
- Tokens
- Password hashes
- Internal endpoints
- Source maps
- Stack traces
- Cloud credentials

---

# Client-Side Testing

## Open Redirect

- Redirect parameters
- OAuth redirects
- Post-login redirects

---

## CORS Testing

- Wildcard origins
- Credentialed requests
- Reflection-based origins

---

## Clickjacking

- Missing `X-Frame-Options`
- Missing CSP frame protections

---

## Prototype Pollution

- Client-side pollution
- DOM-based pollution
- Gadget discovery

---

# Response Analysis

- Status code differences
- Response length differences
- Error messages
- Authorization behavior
- Hidden fields
- Stack traces
- Backend information disclosure

---
