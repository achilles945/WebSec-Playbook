# Cross-Site Scripting (XSS)

Cross-Site Scripting (XSS) is a vulnerability that allows attackers to inject malicious scripts into web pages viewed by other users. These scripts execute in the victim’s browser.

Impact:

* Steal cookies / session tokens
* Account takeover
* Perform actions on behalf of user
* Deface websites

---

## Detection

XSS detection involves injecting payloads and observing whether they are executed in the browser.

Basic payloads:

```id="p9k3zs"
<script>alert(1)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
```

Detection techniques:

* Inject payload and check execution
* Observe reflection in response
* Check where input appears (HTML / attribute / JS)
* Use browser dev tools to inspect DOM

---

## XSS Types

### 1. Reflected XSS

Payload is reflected in the response and executed immediately.

Practical Notes:

* Found in search parameters, error messages, URL params
* Payload appears in response HTML
* Requires user interaction (clicking link)

---

### 2. Stored XSS

Payload is stored in the application and executed when other users view it.

Practical Notes:

* Found in comments, profiles, chat messages
* High impact because it affects multiple users
* No need to resend payload

---

### 3. DOM-Based XSS

Payload is executed in the browser via client-side JavaScript.

Practical Notes:

* Does NOT appear in server response
* Found in JavaScript code (`document.write`, `innerHTML`)
* Triggered by URL fragments, JS variables

---

## XSS Contexts

XSS depends on where the input is placed in the response.

---

### HTML Context

User input is placed directly inside HTML content between tags.

```id="6b3kzn"
<script>alert(1)</script>
<svg onload=alert(1)>
<img src=x onerror=alert(1)>
```

---

### Attribute Context

User input is inside an HTML tag attribute value.

```id="9w2hxr"
" onmouseover=alert(1)
" autofocus onfocus=alert(1)
' onerror=alert(1) x='
```

---

### JavaScript Context

User input is embedded inside JavaScript code.

```id="m4q8ty"
';alert(1);//
";alert(1);//
</script><script>alert(1)</script>
```

---

### URL Context

User input is used inside a URL (e.g., href, src).

```id="3v7kpa"
javascript:alert(1)
data:text/html,<script>alert(1)</script>
```

---

### Inside Tag Context

User input is inserted directly within a tag without needing to break out.

```id="h2p6rs"
<iframe src=javascript:alert(1)>
<object data=javascript:alert(1)>
```

---

## Exploitation

Steps:

1. Identify reflection point
2. Determine context
3. Break out of context if needed
4. Inject payload
5. Execute JavaScript

---

## Filter Bypass

Applications may block common payloads.

Techniques:

* Use alternative tags
* Encode payloads
* Break keywords
* Use event handlers instead of `<script>`

---

### Common Bypass Payloads

```id="u8c3mf"
<svg/onload=alert(1)>
<img src=x onerror=alert(1)>
<iframe src=javascript:alert(1)>

<ScRiPt>alert(1)</ScRiPt>
<scr<script>ipt>alert(1)</script>
```

---

## Notes

* Context is everything in XSS
* Same payload will not work everywhere
* Always identify where input is placed
* Try breaking out of quotes or tags
* Check JavaScript sinks (`innerHTML`, `eval`)
* Use DevTools to inspect DOM

---
