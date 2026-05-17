# STATIC WEBSITE METHODOLOGY

---

## 0. RULES

* Treat JavaScript as exposed source code
* Focus on information disclosure and client-side attack surface
* Prioritize exposed files and hidden functionality
* Do not waste time testing backend vulnerabilities without backend functionality

---

## 1. INITIAL RECON

### Identify website type

Check whether the target is:

* Pure static HTML site
* Documentation site
* Portfolio site
* Marketing website
* CDN-hosted frontend

---

### Identify technologies

* JavaScript libraries
* Frontend frameworks
* CDN providers
* Analytics integrations

Useful tools:

* Wappalyzer
* BuiltWith
* Burp Suite
* Browser DevTools

---

## 2. APPLICATION MAPPING

### Crawl the entire application

Identify:

* HTML pages
* JavaScript files
* CSS files
* Images
* Documents
* Hidden paths

---

### Look For

* Backup files
* Old pages
* Hidden directories
* Development files
* Configuration files

---

### Common Targets

```text id="wqyt54"
robots.txt
sitemap.xml
.git/
.env
backup.zip
old/
dev/
test/
```

---

## 3. JAVASCRIPT ANALYSIS

### Primary Objective

Extract sensitive information and hidden functionality from JavaScript files.

### Look For

* API endpoints
* Hidden routes
* Admin paths
* Secrets
* API keys
* Tokens
* Email addresses
* Internal URLs
* Debug functionality

---

### Important Files

```text id="gqv8x9"
main.js
app.js
bundle.js
config.js
```

---

### Notes

JavaScript is usually the most valuable attack surface in static websites.

---

## 4. SOURCE MAP ANALYSIS

### Check for exposed source maps

```text id="l7qv3s"
main.js.map
bundle.js.map
```

### Extract

* Original source code
* Internal comments
* Hidden functionality
* Sensitive information

---

### Notes

Source maps often expose developer information unintentionally.

---

## 5. CLIENT-SIDE TESTING

### Focus Areas

* URL parameters
* Search functionality
* Hash fragments
* DOM manipulation
* postMessage usage

---

### Primary Vulnerabilities

* DOM XSS
* Client-side redirects
* postMessage vulnerabilities
* DOM clobbering

---

### Dangerous Sinks

```javascript id="sdxk4r"
innerHTML
document.write
eval
setTimeout
```

---

### Dangerous Sources

```javascript id="z7zv8l"
location
document.URL
document.referrer
postMessage
```

---

## 6. FILE DISCLOSURE

### Look For

* Public documents
* Exposed backups
* Configuration files
* Log files
* Temporary files

---

### Common Extensions

```text id="q9yo2m"
.zip
.bak
.old
.log
.txt
```

---

## 7. THIRD-PARTY SCRIPT ANALYSIS

### Identify

* Analytics scripts
* Chat widgets
* External JavaScript
* CDN libraries

---

### Risks

* Outdated libraries
* Exposed API keys
* Supply chain exposure

---

### Notes

Third-party scripts execute with full frontend privileges.

---

## 8. SECURITY MISCONFIGURATIONS

### Check For

* Directory listing
* Missing security headers
* Exposed admin panels
* Open cloud storage buckets
* Public development files

---

### Important Headers

```http id="o4wy9o"
Content-Security-Policy
X-Frame-Options
X-Content-Type-Options
```

---

## 9. TESTING WORKFLOW

### Workflow

1. Crawl the website
2. Collect JavaScript files
3. Analyze source maps
4. Search for secrets and endpoints
5. Test client-side functionality
6. Check exposed files
7. Check security misconfigurations

---
