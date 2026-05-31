# Host Header Injection

The HTTP `Host` header specifies which domain the client wants to access. Web servers, reverse proxies, and load balancers use it to route requests to the correct application or backend service.

Host Header Injection occurs when an application trusts the user-controlled `Host` header and uses it in security-sensitive functionality without proper validation.

Impact:

* Password reset poisoning
* Web cache poisoning
* Authentication bypass
* Routing-based SSRF
* Virtual host brute-forcing
* Business logic abuse

Applications commonly use the `Host` header for:

* URL generation
* Password reset links
* Routing requests
* Multi-tenant applications
* Reverse proxy handling


---

## Detection

Host Header Injection detection involves modifying the `Host` header and checking whether the application trusts and uses the supplied value.

Original Request:

```http id="jlwm3s"
GET / HTTP/1.1
Host: example.com
```

Modified Request:

```http
GET / HTTP/1.1
Host: attacker.com
```

### Check For

* Reflected Host value in response
  → Application trusts Host header

* Generated links using attacker domain
  → Unsafe URL generation

* Password reset links using attacker domain
  → Possible account takeover

* Redirects pointing to attacker domain
  → Redirect manipulation

* Absolute URLs containing attacker domain
  → Host header used in backend logic

* Different responses based on Host value
  → Virtual hosts or backend routing

* Internal application exposure
  → Possible auth bypass or SSRF

* Cache poisoning behavior
  → Shared cache impact

### Common Headers to Test

```http
Host:
X-Forwarded-Host:
X-Host:
Forwarded:
X-Forwarded-Server:
```

### Notes

* `X-Forwarded-Host` is commonly trusted by reverse proxies.
* Compare status code, redirects, content length, and response body.
* Multi-tenant and reverse proxy setups are higher risk.


---

## Password Reset Poisoning

Occurs when password reset functionality uses the `Host` header to generate reset links.

### Original Request

```http
POST /forgot-password HTTP/1.1
Host: victim.com
```

### Malicious Request

```http
POST /forgot-password HTTP/1.1
Host: attacker.com
```

If vulnerable, reset email may contain:

```text
https://attacker.com/reset?token=TOKEN
```

### Notes

* Victim receives attacker-controlled reset link.
* Token leakage can lead to account takeover.
* Common in applications generating absolute URLs dynamically.

---

## Web Cache Poisoning

Occurs when the application or cache server stores responses generated using attacker-controlled Host headers.

Example:

```http id="l9cqzd"
GET / HTTP/1.1
Host: attacker.com
```

If the application reflects the Host header in responses:

```html id="4jw04w"
<script src="https://attacker.com/script.js"></script>
```

the poisoned response may become cached and served to other users.

Check for:

* Reflected Host values
* Cache headers
* CDN behavior
* Cached redirects
* Cached malicious content

Common in:

* CDN environments
* Reverse proxies
* Applications generating absolute URLs

Impact:

* Stored XSS
* Redirect poisoning
* Large-scale user impact

---

## Authentication Bypass

Some applications trust specific Host values for internal access control decisions.

Example:

```http id="k0otm4"
GET /admin HTTP/1.1
Host: localhost
```

or:

```http id="axg4a4"
GET /admin HTTP/1.1
Host: admin.internal
```

If the backend treats these as internal requests, protected functionality may become accessible.

Check for:

* Access to admin panels
* Missing authentication
* Different responses
* Internal debug functionality

Common in:

* Reverse proxy setups
* Internal virtual hosts
* Misconfigured admin applications

Impact:

* Admin access
* Authentication bypass
* Internal application exposure

---

## Routing-Based SSRF

Some reverse proxies use the `Host` header to decide which backend server receives the request.

Example:

```http id="p0q6l7"
GET / HTTP/1.1
Host: internal-api.local
```

If the proxy forwards requests internally based on the Host header, attackers may access:

* Internal APIs
* Admin panels
* Cloud services

Check for:

* Different responses
* Internal errors
* Internal headers
* Unexpected functionality

Common in:

* Reverse proxy setups
* Microservices
* Internal virtual hosts

Impact:

* Internal network access
* SSRF
* Sensitive data exposure

---

## Absolute URL Bypass

Some applications validate the `Host` header but trust the absolute URL in the request line.

Example:

```http
GET https://victim.com/ HTTP/1.1
Host: attacker.com
```

If the backend validates only the absolute URL:
- modified `Host` header may bypass validation
- attacker-controlled Host value may still be processed internally

Check for:

* Different behavior when using absolute URLs
* Host validation bypass
* Internal routing behavior
* SSRF behavior
* Backend requests to attacker-controlled domains

### Collaborator Testing

Example:

```http
GET https://victim.com/ HTTP/1.1
Host: BURP-COLLABORATOR
```

If Burp Collaborator receives:
- DNS interaction
- HTTP interaction

backend is likely issuing requests using attacker-controlled Host value.

### Internal Routing Discovery

Example:

```http
GET https://victim.com/ HTTP/1.1
Host: 192.168.0.10
```

Check for:

* Internal applications
* Admin panels
* Internal headers
* Different responses
* Unexpected functionality

Common in:

* Reverse proxies
* Middleware routing
* Load balancers

Impact:

* Routing-based SSRF
* Internal application access
* Host validation bypass

---

## Connection State Attacks

Some front-end servers incorrectly reuse backend connection state across multiple requests.

If backend routing state is reused:
- requests may inherit previous routing behavior
- internal applications may become accessible

Example:

First request:

```http
GET / HTTP/1.1
Host: victim.com
Connection: keep-alive
```

Second request on same connection:

```http
GET /admin HTTP/1.1
Host: 192.168.0.1
Connection: keep-alive
```

If backend incorrectly reuses connection state:
- internal admin panels may become accessible
- backend routing behavior may change

Check for:

* Different responses on reused connections
* Internal application exposure
* Admin panel access
* Routing inconsistencies
* Authentication inconsistencies

### Testing Method

Use:
- Burp Repeater
- Single connection mode
- `Connection: keep-alive`

Send requests sequentially over same TCP connection.

Common in:

* Reverse proxy environments
* Backend connection reuse setups
* Persistent HTTP connections

Impact:

* Internal application access
* Authentication bypass
* Routing abuse
* Admin panel exposure

---

## Virtual Host Brute-Forcing

Applications hosted on the same server may expose hidden virtual hosts.

Example:

```http id="o3pwnm"
GET / HTTP/1.1
Host: admin.example.com
```

Attack Method:

* Fuzz Host header values
* Observe:

  * Status code changes
  * Content length differences
  * Redirect changes
  * Different page content

Useful for discovering:

* Admin panels
* Staging environments
* Internal applications

Common in:

* Shared hosting
* Reverse proxy environments
* Multi-tenant applications

Impact:

* Hidden functionality exposure
*

---

## Host Override Headers

Some applications use alternate headers instead of `Host`.

### Common Headers

```http
X-Forwarded-Host
X-Host
Forwarded
X-Forwarded-Server
```

### Example

```http
GET / HTTP/1.1
Host: victim.com
X-Forwarded-Host: attacker.com
```

### Notes

* Reverse proxies commonly trust these headers.
* Applications may validate `Host` but trust override headers.

---

## Common Indicators

* Reflected host values
* Attacker-controlled links
* Different responses based on Host header
* Internal application exposure
* Cache anomalies
* Password reset poisoning

---

## Testing Methodology

### Basic Workflow

1. Capture request in Burp
2. Modify `Host`
3. Test override headers
4. Observe response differences
5. Test password reset functionality
6. Test cache behavior

### Things to Check

* Generated URLs
* Redirects
* Emails
* API responses
* HTML source
* Cache headers

### Notes

* Applications behind proxies are common targets.
* Always test password reset functionality.
* Compare responses carefully.

---

## Prevention

* Validate Host header strictly
* Use whitelist-based validation
* Avoid trusting user-controlled headers
* Use fixed application domains
* Validate reverse proxy headers
* Reject untrusted Host values
