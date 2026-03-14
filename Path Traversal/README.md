# Path Traversal

Path Traversal (also known as **Directory Traversal**) is a web security vulnerability that allows an attacker to read arbitrary files on the server that is running an application.

It occurs when an application uses user-supplied input to construct file paths without properly validating or sanitizing the input.

Successful exploitation can allow attackers to access sensitive files on the server.

Possible impacts include:

- Reading arbitrary files on the server
- Accessing application source code and data
- Retrieving credentials for back-end systems
- Accessing sensitive operating system files
- In some cases, writing arbitrary files on the server

---

## Detection

Path Traversal vulnerabilities typically occur when applications allow user input to specify file paths.

Indicators include:

- Parameters that reference files (`filename`, `file`, `path`, `template`, `page`)

• File download functionality

• Image or document loading endpoints

• File preview functionality

Common test payload:

```
../../../etc/passwd
```

If the response returns contents of system files, the application is vulnerable.

---

## Reading Arbitrary Files via Path Traversal

Example application loading an image using a filename parameter.

HTML request:

```
<img src="/loadImage?filename=218.png">
```

The `loadImage` endpoint takes a `filename` parameter.

File is stored on disk at:

```
/var/www/images/218.png
```

If the application does not validate the input properly, an attacker can modify the parameter.

Malicious request:

```
https://insecure-website.com/loadImage?filename=../../../etc/passwd
```

This causes the application to read from:

```
/var/www/images/../../../etc/passwd
```

Traversal sequence:

```
../
```

This sequence moves **one directory up** in the file system.

---

## Path Traversal with Absolute Path Bypass

Some applications block traversal sequences such as `../`.

An attacker may bypass this restriction by providing an **absolute file path** directly.

Example payload:

```
/etc/passwd
```

If the application accepts absolute paths, it may return the contents of the file.

---

## Path Traversal with Non-Recursive Traversal Sequence Removal

Some applications attempt to remove traversal sequences such as `../`.

However, if the removal is **non-recursive**, attackers can bypass the filter using repeated patterns.

Example payload:

```
....//....//....//etc/passwd
```

or

```
....\/....\/etc/passwd
```

After normalization, these patterns resolve to valid traversal sequences.

---

## Path Traversal with Superfluous URL Decoding

Some applications perform **URL decoding multiple times**.

Attackers can use **double encoding** to bypass filters.

Example payload:

```
..%252f..%252f..%252fetc/passwd
```

Encoding examples:

```
../ → %2e%2e%2f
../ → %252e%252e%252f
```

If the application decodes the input twice, the traversal sequence becomes valid.

---

## Path Traversal with Validation of Start of Path

Some applications enforce that the file path must start with a specific directory.

Attackers can bypass this by starting with the allowed path and then traversing outside it.

Example payload:

```
/var/www/images/../../../etc/passwd
```

Even though the path begins with the allowed directory, the traversal sequence allows access to sensitive files.

---

## Path Traversal with File Extension Validation

Some applications validate file extensions such as `.png` or `.jpg`.

Attackers may bypass this validation using **null byte injection**.

Example payload:

```
../../../etc/passwd%00.png
```

The **null byte (`%00`)** terminates the string during backend processing.

As a result, the application may treat the filename as:

```
../../../etc/passwd
```

while the extension check still sees `.png`.