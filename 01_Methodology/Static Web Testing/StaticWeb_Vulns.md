## STATIC WEBSITE VULNERABILITIES

### Vulnerable Dependencies

Detection:

* Identify outdated JavaScript libraries
* Check framework versions
* Analyze third-party scripts

Common Targets:

```text id="g41wna"
jQuery
Bootstrap
AngularJS
React
Vue
```

---

### Security Headers Missing

Detection:

* Inspect response headers
* Identify missing browser protections

Important Headers:

```http id="hf7vt8"
Content-Security-Policy
X-Frame-Options
X-Content-Type-Options
Strict-Transport-Security
Referrer-Policy
```

---

### Sensitive Data Exposure

Detection:

* Analyze JavaScript files
* Check source maps
* Search exposed files

Look For:

* API keys
* Tokens
* Email addresses
* Internal URLs
* Credentials

---

### Verbose Error Messages

Detection:

* Trigger invalid requests
* Observe frontend/debug errors

Look For:

* Stack traces
* Internal paths
* Framework errors
* Debug information

---

### Open Redirect

Detection:

* Test redirect parameters
* Modify redirect targets

Example:

```text id="yvk4kr"
?redirect=https://attacker.com
```

---

### DOM XSS

Detection:

* Identify client-side sinks
* Trace controllable input

Dangerous Sinks:

```javascript id="jlwmtr"
innerHTML
document.write
eval
```

Common Sources:

```javascript id="0m84mp"
location
document.URL
document.referrer
```

---

### postMessage Vulnerabilities

Detection:

* Identify `postMessage` usage
* Check origin validation

Look For:

* Missing origin checks
* Wildcard origins
* Unsafe message handling

---

### Client-Side Prototype Pollution

Detection:

* Identify unsafe object merging
* Test controllable object properties

Common Parameters:

```javascript id="9f7x8n"
__proto__
constructor
prototype
```

---

### Source Map Exposure

Detection:

* Check for `.map` files

Examples:

```text id="kdr3r2"
main.js.map
bundle.js.map
```

---

### Directory Listing

Detection:

* Access directories directly
* Check for indexed file listings

Common Targets:

```text id="u3u0tm"
/assets/
/uploads/
/backup/
```

---

### Backup File Exposure

Detection:

* Search common backup extensions

Examples:

```text id="c0mdw9"
.zip
.bak
.old
.tar
```

---

### Clickjacking

Detection:

* Check iframe protections
* Test page framing

Missing Headers:

```http id="5l1j4n"
X-Frame-Options
Content-Security-Policy
```

---

### Information Disclosure

Detection:

* Analyze comments
* Inspect JavaScript
* Review exposed documents

Look For:

* Internal endpoints
* Developer notes
* Hidden functionality
* Test environments

---

### CORS Misconfiguration

Detection:

* Inspect CORS headers
* Test arbitrary origins

Important Headers:

```http id="7qjlwm"
Access-Control-Allow-Origin
Access-Control-Allow-Credentials
```

---

### CDN / Third-Party Script Risks

Detection:

* Identify external scripts
* Check outdated third-party resources

Risks:

* Supply chain compromise
* Malicious external scripts
* Exposed API keys

---
