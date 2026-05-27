# Web Cache Deception

Web Cache Deception (WCD) occurs when an attacker tricks a web cache into storing sensitive dynamic content as if it were static content.

The vulnerability exists because:

* Cache server and backend server interpret URLs differently
* Cache rules incorrectly classify sensitive responses as cacheable

Impact:

* Sensitive data exposure
* Account takeover
* API key leakage
* Session leakage
* User information disclosure

Unlike Web Cache Poisoning:

* Web Cache Poisoning injects malicious content into cache
* Web Cache Deception steals sensitive cached content

---

## Understanding Web Caches

Web caches exist to improve performance and reduce server load.

Instead of generating the same response repeatedly:

* cache stores the response
* future users receive cached copy directly

Example infrastructure:

```text id="0bl7lr"
Client → CDN / Cache → Origin Server
```

---

## Static vs Dynamic Content

### Static Content

Usually identical for all users.

Examples:

```text id="6qj8ur"
style.css
app.js
logo.png
```

These files are commonly cached.

---

### Dynamic Content

Generated specifically for each user.

Examples:

```text id="xv1by6"
/account
/profile
/dashboard
```

These pages may contain:

* usernames
* emails
* API keys
* session data

Dynamic content should normally NOT be cached.

---

## Why Web Cache Deception Happens

Cache and backend server process the URL differently.

Example request:

```http id="q8h58n"
GET /account/profile.css HTTP/1.1
Host: vulnerable.com
```

Cache interprets:

```text id="7tnbzh"
Static CSS file
```

because request ends with:

```text id="nsl17m"
.css
```

Backend interprets:

```text id="88g7yo"
/account/profile
```

and returns sensitive account data.

Result:

* cache stores dynamic response
* attacker later retrieves victim data from cache

---

## How Cache Rules Work

Caches commonly decide what to cache using:

* file extensions
* directories
* cache headers
* URL patterns

Common cacheable extensions:

```text id="jlwmw5"
.css
.js
.png
.jpg
.ico
```

Caches often assume these are static resources.

---

## Detection

Web Cache Deception detection involves identifying:

* cache behavior
* URL parsing discrepancies
* backend routing behavior

---

### Step 1 — Identify Cache Presence

Look for cache-related headers:

```http id="jlwmw4"
X-Cache
CF-Cache-Status
Via
Age
```

Example:

```http id="jlwmw3"
X-Cache: HIT
```

or:

```http id="jlwmw2"
CF-Cache-Status: HIT
```

These indicate cached responses.

---

### Step 2 — Identify Sensitive Dynamic Endpoints

Look for:

* account pages
* profile pages
* API responses
* dashboards
* settings pages

Examples:

```text id="jlwmw1"
/account
/profile
/dashboard
/settings
```

Sensitive responses often contain:

* usernames
* emails
* tokens
* API keys

---

### Step 3 — Add Cacheable Extensions

Append static extensions to dynamic endpoints.

Original request:

```http id="jlwmw0"
GET /account HTTP/1.1
Host: vulnerable.com
```

Modified request:

```http id="jlwmv9"
GET /account/test.css HTTP/1.1
Host: vulnerable.com
```

or:

```http id="jlwmv8"
GET /profile/random.js HTTP/1.1
Host: vulnerable.com
```

Goal:

* trick cache into treating response as static content

---

### Step 4 — Check Backend Behavior

Determine how backend interprets modified path.

Possible backend behavior:

```text id="jlwmv7"
/account/test.css → /account
```

If backend ignores suffix:

* sensitive content may still be returned

---

### Step 5 — Check Cache Behavior

Reload request multiple times.

Look for:

```http id="jlwmv6"
X-Cache: HIT
```

or:

* faster responses
* identical responses
* cache headers

If dynamic content becomes cached:

* vulnerability may exist

---

## Path Mapping Discrepancies

Occurs when cache and backend map paths differently.

Example:

```http id="jlwmv5"
GET /profile/test.css HTTP/1.1
Host: vulnerable.com
```

Cache interprets:

```text id="jlwmv4"
Static CSS file
```

Backend route interprets:

```text id="jlwmv3"
/profile/*
```

Result:

* sensitive response cached

---

## Delimiter Discrepancies

Cache and backend may interpret delimiters differently.

Common delimiters:

```text id="jlwmv2"
;
?
#
%23
%2F
```

Example:

```http id="jlwmv1"
GET /profile;test.css HTTP/1.1
Host: vulnerable.com
```

Cache interprets:

```text id="jlwmv0"
/profile;test.css
```

Backend interprets:

```text id="jlwmu9"
/profile
```

Result:

* cache stores sensitive response

---

## Delimiter Decoding Discrepancies

Different systems decode encoded characters differently.

Example:

```http id="jlwmu8"
GET /my-account%23%2f%2e%2e%2fresources?wcd
Host: vulnerable.com
```


```http id="jlwmu8"
GET /profile%2Ftest.css HTTP/1.1
Host: vulnerable.com
```

One system decodes:

```text id="jlwmu7"
/profile/test.css
```

Another interprets path differently.

Result:

* path confusion
* cache deception

---

## Normalization Discrepancies

Some systems normalize paths differently.

Common normalization:

* removing `../`
* resolving slashes
* decoding encodings

Example:

```http id="jlwmu6"
GET /static/../account HTTP/1.1
Host: vulnerable.com
```

Cache interprets:

```text id="jlwmu5"
/static/../account
```

Backend normalizes:

```text id="jlwmu4"
/account
```

Result:

* dynamic content cached

---

## Static Directory Cache Rules

Some caches automatically trust specific directories.

Common directories:

```text id="jlwmu3"
/static/
/assets/
/images/
```

Example:

```http id="jlwmu2"
GET /static/../profile HTTP/1.1
Host: vulnerable.com
```

Cache trusts:

```text id="jlwmu1"
/static/
```

Backend resolves:

```text id="jlwmu0"
/profile
```

Result:

* sensitive content cached

---

## File Name Cache Rules

Some caches only inspect filename patterns.

Example:

```http id="jlwmt9"
GET /profile/random.js HTTP/1.1
Host: vulnerable.com
```

Cache sees:

```text id="jlwmt8"
JavaScript file
```

Backend serves:

```text id="jlwmt7"
/profile
```

Result:

* dynamic response cached

---

## Cache Busters

Cache busters prevent interference from existing cached responses.

Example:

```http id="jlwmt6"
GET /profile/test.css?cb=12345 HTTP/1.1
Host: vulnerable.com
```

Useful because:

* forces fresh cache key
* avoids false positives

---

## Exploitation Flow

### Step 1 — Find Sensitive Endpoint

Example:

```text id="jlwmt5"
/account
/profile
/dashboard
```

---

### Step 2 — Add Cacheable Path

Example:

```text id="jlwmt4"
/account/test.css
```

---

### Step 3 — Victim Visits URL

Victim loads:

```text id="jlwmt3"
/account/test.css
```

Response contains victim data.

---

### Step 4 — Cache Stores Response

Cache incorrectly stores sensitive response.

---

### Step 5 — Attacker Retrieves Cached Response

Attacker requests:

```text id="jlwmt2"
/account/test.css
```

and receives victim data.

---

## Common Indicators

* Dynamic pages returning cache headers
* Sensitive responses with `HIT`
* Backend ignoring path suffixes
* CDN caching authenticated responses
* URL parsing inconsistencies

---

## Common Targets

* CDNs
* Reverse proxies
* Cloudflare
* Akamai
* Fastly
* CloudFront

---

## Testing Methodology

### Basic Workflow

1. Identify cache presence
2. Find sensitive dynamic pages
3. Append static extensions
4. Test delimiters
5. Test normalization behavior
6. Observe cache headers
7. Confirm cached sensitive responses

---

### Things To Observe

* Response headers
* Cache hits
* Path behavior
* Redirect behavior
* Response consistency
* Backend routing differences

---

## Tools


### Useful Extensions

```text id="jlwmt1"
Web Cache Deception Scanner
```

---

## Mental Model

Cache thinks:

```text id="jlwmt0"
Static content
```

Backend thinks:

```text id="jlwms9"
Dynamic sensitive content
```

Attacker exploits this disagreement to cache victim data.

---

## Prevention

* Disable caching for authenticated content

* Use:

  * `Cache-Control: no-store`
  * `Cache-Control: private`

* Normalize URL parsing consistently

* Configure cache rules securely

* Avoid caching dynamic responses

* Validate path handling consistently
