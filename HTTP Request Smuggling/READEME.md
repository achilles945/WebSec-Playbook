# HTTP Request Smuggling

HTTP Request Smuggling occurs when frontend and backend servers interpret HTTP requests differently.

The vulnerability exists because multiple HTTP systems disagree on where a request starts or ends.

This usually occurs between:
- Reverse proxies
- Load balancers
- CDNs
- Backend application servers

Example infrastructure:

```text
Client → CDN → Reverse Proxy → Backend Server
```

The frontend server processes the request first and forwards it to the backend server.

If frontend and backend parse the request differently:
- Hidden requests may be smuggled
- Backend request queues may desynchronize
- Attackers may interfere with other users' requests

Impact:
- Authentication bypass
- Web cache poisoning
- Session hijacking
- Internal API access
- Access control bypass
- Response queue poisoning
- Internal header exposure

---

## Core HTTP Concepts

Understanding Request Smuggling requires understanding how HTTP requests are parsed.

---

### Content-Length Header

`Content-Length` specifies the exact body size.

Example:

```http
POST / HTTP/1.1
Host: example.com
Content-Length: 5

HELLO
```

The server reads exactly:

```text
5 bytes
```

which becomes:

```text
HELLO
```

---

### Transfer-Encoding Header

`Transfer-Encoding: chunked` changes how the request body is parsed.

Instead of fixed length:
- Body is split into chunks
- Each chunk starts with chunk size

Example:

```http
POST / HTTP/1.1
Host: example.com
Transfer-Encoding: chunked

5
HELLO
0
```

Meaning:

```text
5 bytes → HELLO
0 → request ends
```

---

### Why Smuggling Happens

If frontend and backend disagree about:
- `Content-Length`
- `Transfer-Encoding`
- request boundaries

then hidden requests can remain in the backend connection.

Example:

Frontend:
```text
Uses Content-Length
```

Backend:
```text
Uses Transfer-Encoding
```

Result:
```text
Frontend and backend process different request boundaries
```

This creates desynchronization.

---

## Detection

Request Smuggling detection involves identifying parsing inconsistencies.

---

### Common Indicators

- Request timeouts
- Backend delays
- Connection hangs
- Unexpected redirects
- Different responses for malformed requests
- Queue poisoning behavior
- Strange backend errors

---

### Common Vulnerable Infrastructure

- Reverse proxies
- Load balancers
- CDNs
- API gateways
- HTTP/2 downgrading systems

---

### Important Headers

```http
Content-Length
Transfer-Encoding
```

These headers are the core of most Request Smuggling attacks.

---

## CL.TE Vulnerability

Frontend uses:

```text
Content-Length
```

Backend uses:

```text
Transfer-Encoding
```

---

### Attack Logic

Frontend:
- reads request using `Content-Length`
- believes request ends early

Backend:
- processes `Transfer-Encoding`
- treats remaining data as a second request

Result:
- hidden request gets smuggled

---

### Example

```http
POST / HTTP/1.1
Host: vulnerable.com
Content-Length: 13
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
Host: vulnerable.com
```

---

### Frontend Interpretation

Frontend reads:

```text
0
```

and thinks request ended.

---

### Backend Interpretation

Backend processes chunked body:

```text
0 = chunked body end
```

Remaining request:

```http
GET /admin HTTP/1.1
Host: vulnerable.com
```

becomes a second hidden request.

---

### Common Indicators

- backend timeouts
- delayed responses
- unexpected admin responses
- strange redirects

---

## TE.CL Vulnerability

Frontend uses:

```text
Transfer-Encoding
```

Backend uses:

```text
Content-Length
```

---

### Attack Logic

Frontend:
- processes chunked body

Backend:
- trusts `Content-Length`
- interprets remaining bytes differently

Result:
- request desynchronization

---

### Example

```http
POST / HTTP/1.1
Host: vulnerable.com
Content-Length: 4
Transfer-Encoding: chunked

5c
GPOST / HTTP/1.1
Content-Length: 15

x=1
0
```

---

### Notes

- Harder to exploit reliably
- Common in complex infrastructures
- Often discovered using automated tooling

---

## TE.TE Vulnerability

Both frontend and backend support:

```text
Transfer-Encoding
```

but process it differently.

---

### Common Obfuscation Techniques

```http
Transfer-Encoding: chunked
Transfer-Encoding : chunked
Transfer-Encoding:[tab]chunked
Transfer-Encoding: xchunked
```

---

### Why This Works

Some servers:
- ignore malformed headers
- normalize headers differently
- prioritize different values

Frontend and backend therefore disagree.

---

## HTTP/2 Request Smuggling

Occurs when HTTP/2 requests are downgraded incorrectly to HTTP/1.

---

### Why HTTP/2 Matters

HTTP/2 handles:
- streams
- framing
- multiplexing

very differently from HTTP/1.

Improper downgrading may create:
- ambiguous request boundaries
- hidden request injection
- desynchronization

---

### Common Targets

- CDNs
- API gateways
- reverse proxies
- cloud infrastructure

---

## Request Queue Poisoning

One of the most dangerous Request Smuggling impacts.

Occurs when backend request queues become desynchronized.

---

### Attack Flow

1. attacker smuggles hidden request
2. backend queue becomes poisoned
3. victim request receives attacker-controlled response

---

### Example Impact

Victim may receive:
- attacker response
- attacker redirect
- attacker-controlled HTML
- session data leakage

---

### Why It Happens

Backend connections are often reused between users.

When requests become desynchronized:
- requests and responses mismatch

This can affect other users.

---

## Web Cache Poisoning

Smuggled requests may poison cache systems.

---

### Example Impact

- cached XSS
- cached redirects
- cached malicious content

---

### Common Targets

- CDNs
- reverse proxies
- shared caches

---

## Authentication Bypass

Frontend security controls may process requests differently than backend servers.

---

### Example

Frontend blocks:

```http
/admin
```

Smuggled request reaches backend directly.

---

### Why This Happens

Frontend:
- applies filtering

Backend:
- processes hidden request separately

Result:
- backend functionality becomes accessible

---

## Internal Header Exposure

Smuggled requests may expose internal headers added by proxies.

---

### Common Headers

```http
X-Forwarded-For
X-Internal-IP
X-Backend-Server
```

---

### Impact

Useful for:
- infrastructure mapping
- internal host discovery
- identifying proxy behavior

---

## Testing Methodology

### Step 1 — Identify Intermediaries

Look for:
- CDNs
- reverse proxies
- load balancers

Indicators:

```http
Via:
X-Cache:
CF-Cache-Status:
Server:
```

---

### Step 2 — Send Malformed Requests

Test:
- CL.TE
- TE.CL
- TE.TE

Observe:
- timeouts
- delayed responses
- desynchronization

---

### Step 3 — Confirm Desync

Check for:
- backend confusion
- poisoned responses
- connection hangs
- inconsistent responses

---

### Step 4 — Escalate

Attempt:
- queue poisoning
- cache poisoning
- auth bypass
- internal access

---

## Common Indicators

- frontend timeout
- backend timeout
- strange redirects
- malformed responses
- inconsistent behavior
- poisoned responses

---

## Tools

### Burp Extension

```text
HTTP Request Smuggler
```

Useful for:
- automated detection
- desync probing
- HTTP/2 testing

---

### Notes

- Manual verification is critical
- Automated findings often contain false positives
- Understanding parsing behavior matters more than payload memorization

---

## Mental Model

Frontend and backend disagree about:

```text
Where request ends
```

Attacker exploits this disagreement to:
- smuggle hidden requests
- desynchronize connections
- interfere with backend processing

---

## Prevention

- Normalize HTTP parsing
- Use consistent frontend/backend configurations
- Reject ambiguous requests
- Disable unsupported transfer encodings
- Keep proxies updated
- Use modern HTTP parsers

