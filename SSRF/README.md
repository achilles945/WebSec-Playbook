# Server-Side Request Forgery (SSRF)

Server-Side Request Forgery (SSRF) is a web security vulnerability that allows an attacker to cause the server-side application to make requests to unintended locations.

By manipulating URLs used by the application, attackers can force the server to send requests to internal or external systems.

Possible targets include:

- Internal-only services within the organization's infrastructure
- Localhost services running on the same server
- Other internal servers on the private network
- External systems controlled by the attacker

### Impact

The impact of SSRF vulnerabilities depends on the network access available to the server.

Possible impacts include:

- Unauthorized access to internal systems
- Access to internal admin interfaces
- Exposure of sensitive internal APIs or databases
- Internal network enumeration
- Privilege escalation within the application
- Using the server to launch attacks on other systems

---

## Detection

SSRF vulnerabilities occur when applications **fetch remote resources based on user-controlled input**.

Indicators that SSRF may exist include:

- Parameters that accept **URLs**
- Features that **fetch remote resources**
- **Webhook functionality**
- **File import features**
- **URL preview functionality**
- **Stock check / API proxy endpoints**

Common parameters to test:

```
url=
uri=
path=
dest=
redirect=
next=
api=
callback=
```

Example test payloads:

```
http://127.0.0.1
http://localhost
http://burpcollaborator.net
```

If the server performs the request, the application may be vulnerable to SSRF.

---

## SSRF Types

SSRF vulnerabilities can be classified based on how the server performs the request and whether the response is returned.

### Basic SSRF

Occurs when the application fetches a user-supplied URL and returns the response.

Example target:

```
http://localhost/admin
```

The attacker receives the response directly in the application output.

---

### Blind SSRF

Occurs when the server makes the request but **does not return the response** to the attacker.

Attackers detect exploitation through **out-of-band interactions**.

Example detection method:

```
http://attacker-controlled-domain
```

If the server performs a DNS or HTTP request to the attacker's server, SSRF is confirmed.

---

### SSRF Against Internal Systems

SSRF can be used to access **internal network resources** that are not accessible from the internet.

Example internal targets:

```
127.0.0.1
192.168.0.1
10.0.0.1
```

Attackers may enumerate internal services using SSRF.

---

### SSRF Against the Local Server

The attacker causes the application to send requests back to itself using the loopback interface.

Common loopback addresses:

```
127.0.0.1
localhost
```

This may expose internal admin interfaces or privileged endpoints.

---

## SSRF Attacks Against the Local Server

An attacker may cause the application to make requests back to the same server using its loopback interface.

Reasons this works:

- Front-end access controls can be bypassed by sending requests directly to the backend
- Applications often trust requests originating from `localhost`
- Admin interfaces may be accessible only internally

Original request:

```
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://stock.weliketoshop.net:8080/product/stock/check%3FproductId%3D6%26storeId%3D1
```

Tampered request:

```
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://localhost/admin
```

---

## Basic SSRF Against Another Internal System

The server may have access to internal network hosts that are not accessible externally.

Example internal target:

```
http://192.168.0.1:8080/admin
```

Attackers can enumerate internal IP ranges.

Example scan range:

```
192.168.0.1 – 192.168.0.255
```

---

## Finding Hidden Attack Surface for SSRF

SSRF vulnerabilities may exist in locations where user input controls backend requests.

### Partial URLs in Requests

Applications may concatenate user input with a trusted base URL.

Attackers may inject alternative hosts or traversal sequences.

---

### URLs Within Data Formats

Applications may fetch URLs embedded in structured data.

Examples:

- JSON
- XML
- Form data

These URLs may be requested automatically by the server.

---

### SSRF via Referer Header

Some applications use the `Referer` header to fetch resources.

Example:

```
Referer: http://localhost/admin
```

If the backend processes the header value, SSRF may occur.

---

## SSRF with Blacklist-Based Input Filters

Some applications attempt to block SSRF using blacklists.

Example blocked values:

```
127.0.0.1
localhost
/admin
```

Attackers can bypass these restrictions using alternative representations.

### IP Address Bypass

Examples:

```
127.1
2130706433
017700000001
```

### Domain-Based Bypass

Register a domain that resolves to localhost.

Example:

```
spoofed.burpcollaborator.net
```

### Encoding Bypass

Example:

```
%2561
```

### Redirect-Based Bypass

Example:

```
http://attacker-site.com → redirect → http://localhost/admin
```

---

## SSRF with Whitelist-Based Input Filters

Some applications allow only specific trusted domains.

Example whitelist:

```
https://expected-host
```

Bypass techniques include:

Credential injection:

```
https://expected-host:fakepassword@evil-host
```

URL fragment trick:

```
https://evil-host#expected-host
```

Subdomain injection:

```
https://expected-host.evil-host
```

Encoding tricks may also confuse URL validation logic.

---

## Bypassing SSRF Filters via Open Redirection

Open redirect vulnerabilities can be used to bypass SSRF filters.

Example redirect endpoint:

```
/product/nextProduct?currentProductId=6&path=http://evil-user.net
```

Example SSRF payload:

```
stockApi=http://weliketoshop.net/product/nextProduct?currentProductId=6&path=http://192.168.0.68/admin
```

The application trusts the initial domain but eventually requests the internal target.

---

## Blind SSRF Vulnerabilities

Blind SSRF occurs when the application performs the backend request but does not return the response to the attacker.

Detection usually relies on **out-of-band interaction servers**.

Example technique:

1. Intercept the request
2. Insert an interaction server domain
3. Send the request

Example header modification:

```
Referer: <collaborator-domain>
```

If the server performs the request, the attacker will observe:

- DNS interactions
- HTTP interactions

These interactions confirm the SSRF vulnerability.