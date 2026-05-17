# Core Web Fundamentals

## Working

- Client–server architecture
- DNS resolution (A, AAAA, CNAME, MX)
- TCP vs UDP
- Ports & common services (80, 443, 21, 22, 25)
- HTTP vs HTTPS
- TLS/SSL basics (certificates, handshake)

## HTTP Protocol

- HTTP methods: `GET`, `POST`, `PUT`, `DELETE`, `PATCH`, `OPTIONS`
- Request structure:
    - Headers
    - Cookies
    - Body
- Response structure:
    - Status codes (1xx–5xx)
    - Headers
    - Content types
- Status Codes
- Statelessness & sessions
- Keep-Alive, caching, redirects
- Security Headers
    - Content-Security-Policy (CSP)
    - Strict-Transport-Security (HSTS)
    - X-Frame-Options
    - X-Content-Type-Options
    - Referrer-Policy
    - Permission-Policy
    - Cross-Origin-Resource-Policy (CORP)
    - Access-Control-Allow-Origin (CORS Headers)

## Cookie & Sessions

- Session IDs
- Secure, HttpOnly, SameSite flags
- Cookie-based vs token-based sessions
- Session expiration & fixation

---

## Working

---

### Client-Sever Architecture

- Client request services or resources from central server
- **client** sends requests and a **server** processes those requests and sends back responses.
- On the Web
    - Client = browser, mobile app, API consumer
    - Server = web server + application + database
- How it works
    - Client sends a request (usually HTTP)
    - Server receives the request
    - Server processes logic (auth, validation, database)
    - Server sends a response
    - Client renders or uses the response
- Core Components
    - Client
        - Browser (Chrome, Firefox)
        - Mobile app
        - API tool (curl, Postman)
        - Runs on the user’s device
        - **Cannot be trusted**
    - Server
        - Web server (Nginx, Apache)
        - Application logic (PHP, Python, Node.js, Java)
        - Database
        - Runs on remote infrastructure
        - Enforces security
- Characteristics
    - Request-Response Model
        - Client always initiates communication
        - Server responds, does not initiate
    - Stateless by Default
        - Each request is independent
        - Sessions are used to maintain state
    - Separation of Responsibilities
        - Client: UI, display, user interaction
        - Server: Business Logic, data storage, security
    

---

### DNS resolution (A, AAAA, CNAME, MX)

- DNS (Domain Name System)
- Translates Domain names into IP Addresses
- Working of DNS Resolution
    - [example.com](http://example.com) is entered in browser
    - Browser checks local cache
    - OS checks DNS cache
    - Request goes to a recursive resolver (ISP / public DNS)
    - Resolver asks:
        - Root DNS server
        - TLD server (.com)
        - Authoritative DNS server
    - IP address is returned
    - Browser connects to the server
- DNS Records
    - A Records (Address)
        - Maps domain → IPv4 address
    - AAAA Record
        - Maps domain → IPv6 address
    - CNAME Record (Canonical Name)
        - Points one domain to another domain
        - [www.example.com](http://www.example.com) → example.com
    - MX Record (Mail Exchange)
        - Define mail servers for a domain
        - [example.com](http://example.com) → mail.example.com
- DNS Resolution Flow
    - Domain Name → DNS Record (A / AAAA / CNAME / MX) →IP Address or Service → Client connects

---

### TCP vs UDP

### TCP (Transmission Control Protocol)

- A connection-oriented, reliable protocol
- TCP cares about accuracy and order
- TCP Working (3-way handshake)
    1. Client →SYN
    2. Server → SYN ACK
    3. Client → ACK
    - Connection established
- After Connection
    - Data is sent in order
    - Lost packets are retransmitted
    - Connection is closed using (4-way handshake)
- Characteristics
    - Reliable
    - Ordered
    - Error-Checked
    - Slower than UDP
    - Stateful
- Common TCP Services
    - HTTP / HTTPS
    - SHH
    - FTP
    - SMTP
    - IMAP
    - Databases

### UDP ( User Datagram Protocol)

- A connectionless, unreliable protocol
- UDP cares about speed, not guarantees
- UDP Working
    - No handshake
    - Packets sent independently
    - No confirmation of delivery
    - No retransmission
- UDP Characteristics
    - Fast
    - No ordering
    - No reliability
    - Stateless
    - Minimal overhead
- Common UDP Services
    - DNS
    - DHCP
    - SNMP
    - NTP
    - VoIP
    - Streaming

---

### Ports & common services

- A Port is a logical entry point on a machine that tells the OS which service should receive incoming traffic
- With ports a single server can run
    - A web server
    - An SSH service
    - A mail server
- Port Number
    - Range: 0 - 65535
    - Well-known ports: 0 - 1023
    - Registered ports: 1024 - 49151
    - Ephemeral ports: 49152 - 65535
- Port 80 - HTTP
    
    **Service:** Web (unencrypted)
    
    - Plaintext HTTP traffic
    - Data can be sniffed or modified
    - Often redirects to HTTPS
- Port 443 - HTTPS
    
    **Service:** Secure Web (TLS)
    
    - Encrypted HTTP traffic
    - Certificates involved
- Port 21 - FTP
    
    **Service:** File Transfer Protocol
    
    - Plaintext credentials
    - Old and insecure by default
- Port 22 - SSH
    
    **Service:** Secure Shell
    
    - Remote server administration
    - Encrypted
- Port 25 - SMTP
    
    **Service:** Email sending
    
    - Sends outgoing mail
    - Often filtered by ISPs

---

### HTTP vs HTTPS

### HTTP

- **HTTP (Hypertext Transfer Protocol)** is the protocol used to transfer data between a **client** and a **server**.
- HTTP Sends data in plaintext
    - Requests are readable
    - Responses are readable
    - Headers, cookies, credentials are visible
    - Traffic can be modified in transit

```
Client ─── plaintext ───▶Server
Client ◀── plaintext ────Server
```

### HTTPS

- **HTTPS = HTTP + TLS encryption**
- HTTPS encrypts the communication channel, not the application logic.
- HTTPS does not change how HTTP works - it wraps HTTP inside TLS

```
Client ──encrypted (TLS) ─▶Server
Client ◀─encrypted (TLS) ──Server
```

- What TLS Provides
    - Confidentiality
    - Integrity
    - Authentication
- Certificates
    - Issued by Certificate Authorities (CAs)
    - Bind domain name → public key
    - Browser validates:
        - Signature
        - Expiration
        - Domain match
        - Trust chain
    
    If validation fails → browser warning
    

---

### TLS/SSL basics (certificates, handshake)

- SSL ( Secure Sockets Layer) = old deprecated
- TLS ( Transport Layer Security) = modern replacement
- Versions
    - SSLv2 / SSLv3 - Broken
    - TLS 1.0 / 1.1 - Deprecated
    - TLS 1.2 - Widely used
    - TLS 1.3 - Modern, faster, safer
- Asymmetric = slow, used only to agree on keys
- Symmetric = fast, used for data transfer

### TLS

- TLS secures communication
    - Confidentiality
    - Integrity
    - Authentication
    - HTTP → TLS → TCP → IP

### Certificates

- A digital certificate
    - Binds a domain name to public key
    - Is issued by a Certificate Authority (CA)
- Certificate Contents ( Browser uses this to trust server)
    - Domain name(s)
    - Public key
    - Issuer (CA)
    - Expiration date
    - Signature

### TLS Handshake

- **ClientHello**
    - Supported TLS versions
    - Cipher suites
    - Random value
- **ServerHello**
    - Chosen TLS version
    - Cipher suite
    - Certificate sent
- **Certificate Validation**
    - Domain match
    - Expiry
    - Trust chain
    - Signature
- **Key Exchange**
    - Shared secret established
    - Uses asymmetric crypto
- **Secure Channel Established**
    - Switch to symmetric encryption
    - Encrypted HTTP begins

---

## HTTP Protocol

---

### HTTP methods: `GET`, `POST`, `PUT`, `DELETE`, `PATCH`, `OPTIONS`

- Methods define what action the client wants the server to perform on a resource
- Safe
    - Does **not** change server state
    - Examples: `GET`, `HEAD`, `OPTIONS`
- Unsafe
    - Changes server state
    - Examples: `POST`, `PUT`, `DELETE`, `PATCH`
- Idempotent
    - Multiple identical request = same result
    - Example: `GET`, `PUT`, `DELETE`
- Non-Idempotent
    - Repeating changes result
    - Example: `POST`

### GET

### Purpose

- Retrieve a representation of a resource
- Must **not modify server state**

### Characteristics

- **Safe:** Yes (no side effects)
- **Idempotent:** Yes
- **Cacheable:** Yes (by default)
- **Request body:** Not recommended (often ignored)

### Common Uses

- Fetch records
- Search with query parameters
- Pagination, filtering, sorting

### Example

```
GET /products?category=books&page=2
```

### Important Notes

- Multiple identical GET requests → same result
- Browsers prefetch GET requests
- Should not be used for actions like login or payment

### Typical Response Codes

- `200 OK`
- `304 Not Modified`
- `404 Not Found`

### POST

### Purpose

- Create a new resource
- Submit data for processing
- Trigger non-idempotent actions

### Characteristics

- **Safe:** No
- **Idempotent:** No
- **Cacheable:** No (unless explicitly configured)
- **Request body:** Required (usually JSON)

### Common Uses

- Create resources
- Authentication
- File uploads
- Commands/actions

### Example

```
POST /users
{
  "name": "Alice",
  "email": "alice@example.com"
}
```

### Important Notes

- Repeating the same POST may create duplicates
- Server decides the resource ID
- Often returns `Location` header with new resource URL

### Typical Response Codes

- `201 Created`
- `200 OK`
- `400 Bad Request`
- `409 Conflict`

### PUT

### Purpose

- Replace an entire resource at a known URL

### Characteristics

- **Safe:** No
- **Idempotent:** Yes
- **Cacheable:** No
- **Request body:** Full resource representation

### Common Uses

- Full updates
- Upsert (create if not exists)

### Example

```
PUT /users/42
{
  "id": 42,
  "name": "Alice",
  "email": "alice@new.com"
}
```

### Important Notes

- Missing fields may be **deleted**
- Client must know the resource URL
- Same request repeated → same final state

### Typical Response Codes

- `200 OK`
- `204 No Content`
- `201 Created`

### PATCH

### Purpose

- Apply partial modifications to a resource

### Characteristics

- **Safe:** No
- **Idempotent:** Usually (implementation-dependent)
- **Cacheable:** No
- **Request body:** Partial data or patch document

### Common Uses

- Update single or few fields
- Status changes
- Incremental updates

### Example

```
PATCH /users/42
{
  "email": "alice@updated.com"
}
```

### Patch Formats

- **JSON Merge Patch**
- **JSON Patch** (`add`, `remove`, `replace` ops)

### Important Notes

- Preferred over PUT for partial updates
- Must define how conflicts are handled
- Idempotency depends on logic (e.g., increment is not idempotent)

### Typical Response Codes

- `200 OK`
- `204 No Content`
- `400 Bad Request`

### DELETE

### Purpose

- Remove a resource

### Characteristics

- **Safe:** No
- **Idempotent:** Yes
- **Cacheable:** No
- **Request body:** Usually empty

### Common Uses

- Delete records
- Deactivate resources

### Example

```
DELETE /users/42
```

### Important Notes

- Deleting an already-deleted resource should still succeed
- Can be **soft delete** (mark inactive)
- Should not expose delete via GET

### Typical Response Codes

- `200 OK`
- `204 No Content`
- `404 Not Found`

### OPTIONS

### Purpose

- Discover server capabilities
- Used heavily in **CORS preflight**

### Characteristics

- **Safe:** Yes
- **Idempotent:** Yes
- **Cacheable:** Yes

### Common Uses

- Browser checks allowed methods
- API self-discovery

### Example

```
OPTIONS /users
```

### Response Headers

```
Allow: GET, POST, PUT, DELETE
Access-Control-Allow-Methods: GET, POST
```

### Typical Response Codes

- `200 OK`
- `204 No Content`

---

### Request structure:

- A message sent by a client (browser) to a server to request a resource or perform an action

High-Level Structure:

```
<request-line>
<headers>
<blankline>
<body>
```

Example:

```
POST /login HTTP/1.1
Host: example.com
Content-Type: application/json
Cookie: sessionId=abc123

{
  "username": "alice",
  "password": "secret"
}
```

### Headers

Headers are **key–value pairs** that provide **metadata** about the request.

### Purpose

- Describe request format
- Control caching, authentication, and content negotiation
- Provide client and connection details

### Common Request Headers

### **Host**

- Specifies the target domain

```
Host: api.example.com
```

### **Content-Type**

- Describes format of the request body

```
Content-Type: application/json
```

### **Accept**

- Tells server what response formats client can handle

```
Accept: application/json
```

### **Authorization**

- Sends credentials or tokens

```
Authorization: Bearer <JWT>
```

### **User-Agent**

- Identifies the client software

```
User-Agent: Mozilla/5.0
```

### **Content-Length**

- Size of request body (in bytes)

### **Cache-Control**

- Caching behavior

```
Cache-Control: no-cache
```

---

### Custom Headers

- Application-specific headers

```
X-Request-Id: 12345
```

### Header Rules

- Case-insensitive
- Separated by CRLF
- No duplicate meaning headers

### Cookies

Cookies are **small pieces of data** stored in the client and sent automatically to the server with each request to the same domain.

### Purpose

- Session management
- Authentication
- User preferences
- Tracking

### How Cookies Are Sent

Cookies are sent using the **Cookie header**:

```
Cookie: sessionId=abc123; theme=dark
```

### How Cookies Are Set

Server sends cookies via response header:

```
Set-Cookie: sessionId=abc123; HttpOnly; Secure; SameSite=Strict
```

### Cookie Attributes

| Attribute | Description |
| --- | --- |
| `Expires` | Expiration date |
| `Max-Age` | Lifetime in seconds |
| `Domain` | Domain the cookie is sent to |
| `Path` | URL path scope |
| `Secure` | HTTPS only |
| `HttpOnly` | Inaccessible to JavaScript |
| `SameSite` | CSRF protection |

### Body

The body contains the **actual data** being sent to the server.

| Method | Body |
| --- | --- |
| GET | ❌ Usually none |
| POST | ✅ |
| PUT | ✅ |
| PATCH | ✅ |
| DELETE | Optional |

### Common Body Formats

### **JSON (Most Common)**

```
Content-Type: application/json

{
  "name": "Alice",
  "email": "alice@example.com"
}

```

### **Form URL Encoded**

```
Content-Type: application/x-www-form-urlencoded

username=alice&password=123

```

### **Multipart/Form-Data (File Uploads)**

```
Content-Type: multipart/form-data; boundary=----

------123
Content-Disposition: form-data; name="file"; filename="img.png"

(binary data)
```

### **Plain Text**

```
Content-Type: text/plain
```

### Body Rules

- Body format must match `Content-Type`
- Length defined by `Content-Length` or chunked encoding
- Servers may reject unexpected body content

### Response structure:

- Status codes (1xx–5xx)
- Headers
- Content types

---

### Status Codes

HTTP status codes are **3-digit numbers** returned by the server to describe the **result of a client’s request**.

**Format**

```
<status-code><reason-phrase>
```

Example:

```
HTTP/1.1 200 OK
```

---

## **Status Code Classes**

| Range | Class | Meaning |
| --- | --- | --- |
| 1xx | Informational | Request received, continue |
| 2xx | Success | Request succeeded |
| 3xx | Redirection | Further action required |
| 4xx | Client Error | Request problem |
| 5xx | Server Error | Server failed |

---

### **1xx – Informational**

Used rarely in APIs.

### **100 Continue**

- Client may continue sending request body
- Used for large payloads

### **101 Switching Protocols**

- Protocol upgrade (e.g., HTTP → WebSocket)

---

### **2xx – Success**

### **200 OK**

- Request succeeded
- Used for GET, PUT, PATCH

### **201 Created**

- Resource successfully created
- Typically used with POST
- Often includes `Location` header

### **202 Accepted**

- Request accepted but processing not complete
- Used for async operations

### **204 No Content**

- Success, but no response body
- Common with DELETE or PUT

---

### **3xx – Redirection**

### **301 Moved Permanently**

- Resource moved permanently
- Cached by browsers

### **302 Found**

- Temporary redirection
- Browser may change method to GET

### **304 Not Modified**

- Cached resource is still valid
- No response body

---

### **4xx – Client Errors**

### **400 Bad Request**

- Malformed request
- Invalid JSON, missing fields

### **401 Unauthorized**

- Authentication required or failed
- Missing/invalid credentials

### **403 Forbidden**

- Authenticated but not allowed
- Permission issue

### **404 Not Found**

- Resource does not exist

### **405 Method Not Allowed**

- HTTP method not supported on endpoint

### **409 Conflict**

- Request conflicts with current state
- Duplicate entries, version conflicts

### **422 Unprocessable Entity**

- Valid syntax but semantic errors
- Validation failures

---

### **5xx – Server Errors**

### **500 Internal Server Error**

- Generic server failure

### **502 Bad Gateway**

- Invalid response from upstream server

### **503 Service Unavailable**

- Server overloaded or down for maintenance

### **504 Gateway Timeout**

- Upstream server timeout

| Code | Meaning | Typical Use |
| --- | --- | --- |
| 200 | OK | Successful request |
| 201 | Created | POST success |
| 204 | No Content | DELETE success |
| 400 | Bad Request | Invalid input |
| 401 | Unauthorized | Auth missing/invalid |
| 403 | Forbidden | No permission |
| 404 | Not Found | Missing resource |
| 409 | Conflict | Duplicate data |
| 422 | Validation Error | Field issues |
| 500 | Server Error | Backend failure |

---

### Statelessness & sessions

## **1. Statelessness**

### What Is Statelessness?

A system is **stateless** if **each request is independent** and contains **all information needed** to process it.

- The server **does not store client context** between requests.

### HTTP Is Stateless by Design

- Each HTTP request is treated as **new**
- Server does not remember:
    - Who you are
    - What you did previously
    - Whether you are logged in

Example:

```
GET /profile
```

Without credentials → server has **no idea who you are**

Characteristics of Stateless Systems

| Feature | Description |
| --- | --- |
| No memory | Server stores no client state |
| Scalable | Easy to add/remove servers |
| Reliable | One server failure doesn’t break sessions |
| Simple | Easier to debug |
|  |  |

## **2. Sessions**

### What Is a Session?

A **session** is a way to **maintain state** across multiple HTTP requests.

The server **remembers the client** using a session identifier.

### Why Sessions Exist

Because HTTP is stateless, sessions are added to:

- Track logged-in users
- Store user-specific data
- Maintain continuity

### How Sessions Work (Step-by-Step)

1. User logs in
2. Server creates a **session**
3. Server stores session data (memory / DB / cache)
4. Server sends **session ID** to client (cookie)
5. Client sends session ID with every request
6. Server retrieves session data using session ID

### Session Example

### Server Response

```
Set-Cookie: sessionId=abc123; HttpOnly
```

### Client Request

```
Cookie: sessionId=abc123
```

| Location | Notes |
| --- | --- |
| Server memory | Fast but not scalable |
| Database | Persistent but slower |
| Redis / Cache | Fast & scalable |
| Sticky sessions | Load balancer affinity |

## **4. Session-Based Authentication**

### Flow

1. Login → server creates session
2. Session ID stored in cookie
3. Browser auto-sends cookie
4. Server validates session

### Pros

- Simple
- Widely used
- Easy invalidation (logout)

### Cons

- Server memory usage
- CSRF vulnerability
- Harder to scale

## **5. Token-Based Authentication (Stateless)**

### How It Works

- Server issues a token (e.g., JWT)
- Token contains user info
- Client sends token in every request
- Server validates token — no session storage

```
Authorization: Bearer <token>

```

### Pros

- Stateless
- Highly scalable
- Mobile & API-friendly

### Cons

- Token revocation is hard
- Token size overhead
- Security depends on expiration

---

### Keep-Alive, caching, redirects

---

### Security Headers

- Content-Security-Policy (CSP)
- Strict-Transport-Security (HSTS)
- X-Frame-Options
- X-Content-Type-Options
- Referrer-Policy
- Permission-Policy
- Cross-Origin-Resource-Policy (CORP)
- Access-Control-Allow-Origin (CORS Headers)

### Content-Security-Policy

Tell Browser which domains are allowed for script, styles, images, iframes, etc.

Without CSP, any XSS payload can load external JS or execute inline JS

- Prevents
    - XSS
    - Data injection
    - Malicious script loading
    - Clickjacking (via `frame-ancestors`)
- Example : `Content-Security-Policy: default-src 'self'`

### Strict-Transport-Security (HSTS)

HSTS Forces the browser to always use HTTPS — Never HTTP

- Prevents
    - Main-in-the-Middle attacks
    - Protocol downgrade attacks
    - Cookie theft
- Example: `Strict-Transport-Security: max-age=31536000; includeSubDomains; preload`

### X-Frame-Options

Prevents your site from being loaded inside an iframe on another domain.

Protects from **clickjacking attacks**, where the user is tricked into clicking something invisible.

- Example : `X-Frame-Options: DENY`

### X-Content-Type-Options

Stops browsers from guessing MIME types (“MIME sniffing”).

Prevents XSS caused by browsers wrongly interpreting file types.

- Example : `X-Content-Type-Options: nosniff`

### Referrer-Policy

Controls how much information is sent in the `Referer` header.

Prevents sensitive URL exposure to third-party sites.

- Example : `Referrer-Policy: no-referrer`

### Permissions-Policy

Controls access to browser features like:

- Camera
- Microphone
- Geolocation
- Clipboard

Example : `Permissions-Policy: camera=(), microphone=()`

### Cross-Origin Resource Policy (CORP)

Controls which domains can load resources from your website

- Prevents
    - Cross-origin data leakage
    - Unauthorized embedding

### CORS Headers (Access-Control-Allow-Origin)

Manages which domains can call your API

Mistake : `Access-Control-Allow-Origin: *`

---

## Cookie & Sessions

### Session IDs

A **session ID** is a **unique, random identifier** that represents a user’s session on the server.

How Session IDs Work (Flow)

1. User logs in
2. Server creates session object
3. Server generates **session ID**
4. Server stores session data (DB / cache)
5. Session ID sent to browser as cookie
6. Browser sends session ID on every request
7. Server looks up session using ID

Example

```
Set-Cookie: sessionId=9f8a7c...; HttpOnly
```

```
Cookie: sessionId=9f8a7c...
```

### Secure, HttpOnly, SameSite flags

Cookie flags are **attributes added to cookies** to control **how, when, and by whom** they can be accessed or sent.

### **1. Secure**

What It Does

- Cookie is sent **only over HTTPS**
- Never sent over plain HTTP

```
Set-Cookie: sessionId=abc; Secure
```

Why It Exists

- Prevents **Man-in-the-Middle (MITM)** attacks
- Protects cookies on public Wi-Fi

Key Notes

- Mandatory when `SameSite=None`
- Ignored on HTTP sites
- Should be enabled in production always

Without Secure ❌

- Cookie sent in plaintext
- Attacker can sniff session ID

---

### **2. HttpOnly**

What It Does

- Cookie **cannot be accessed by JavaScript**
- Only sent via HTTP requests

```
Set-Cookie: sessionId=abc; HttpOnly
```

Why It Exists

- Prevents **XSS-based cookie theft**

```jsx
document.cookie// ❌ cannot read HttpOnly cookies
```

Key Notes

- Browser still sends cookie automatically
- Does **not** prevent CSRF
- Should always be enabled for auth cookies

Without HttpOnly ❌

- Malicious JS can steal session ID
- Leads to account hijacking

---

### **3. SameSite**

What It Does

Controls **when cookies are sent in cross-site requests**, protecting against **CSRF**.

```
Set-Cookie: sessionId=abc; SameSite=Lax
```

SameSite Values

**Strict**

```
SameSite=Strict`
```

- Cookie **never sent** on cross-site requests
- Maximum security
- May break login flows

**Lax (Recommended Default)**

```
SameSite=Lax

```

- Sent on **top-level navigation** (GET)
- Blocks most CSRF attacks
- Good balance of usability & security

**None**

```
SameSite=None; Secure

```

- Cookie sent in **all contexts**
- Required for:
    - Third-party cookies
    - Embedded content
- Requires `Secure`

### Cookie-based vs token-based sessions

### **Cookie-Based Sessions (Stateful)**

### How It Works

- Session stored on server
- Cookie stores session ID
- Server validates session each request

### Pros

- Easy logout (delete session)
- Small cookies
- Server control

### Cons

- Server memory usage
- Scaling complexity
- CSRF risk

### **Token-Based Sessions (Stateless)**

### How It Works

- Server issues token (JWT)
- Token stored client-side
- Token sent in `Authorization` header
- No server session storage

```
Authorization: Bearer <token>
```

### Pros

- Stateless
- Scales well
- Mobile & API friendly

### Cons

- Hard to revoke
- Larger payload
- Token leakage risk

### Session expiration & fixation

### Why Expire Sessions?

- Security
- Resource cleanup
- Inactive user handling

### Types of Expiration

### **Absolute Expiration**

- Fixed lifetime (e.g., 24 hours)
- Cannot be extended

### **Idle Timeout**

- Expires after inactivity (e.g., 15 min)

### Cookie Expiration

```
Set-Cookie: sessionId=abc; Max-Age=1800
```

| Attribute | Meaning |
| --- | --- |
| Max-Age | Lifetime in seconds |
| Expires | Absolute date |

### Server-Side Expiration

- Session removed from store
- Cookie becomes useless

### S**ession Fixation**

### What Is Session Fixation?

An attack where an attacker **forces a known session ID** on a victim, then hijacks the session after login.

---

### Attack Flow

1. Attacker creates session
2. Attacker gets session ID
3. Victim uses same session ID
4. Victim logs in
5. Attacker reuses session ID → access

---

### Prevention Techniques

**Regenerate session ID after login**

```
oldSessionId → newSessionId
```

Use **HttpOnly + Secure**

Short expiration times

Invalidate old sessions