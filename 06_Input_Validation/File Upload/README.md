# File Upload Vulnerabilities

File upload vulnerabilities occur when a web server allows users to upload files to its filesystem **without sufficiently validating** properties such as:

- File name
- File type
- File contents
- File size

If validation is weak or missing, attackers may upload **arbitrary and potentially dangerous files**.

In some cases, uploaded files may be executed by the server, leading to **Remote Code Execution (RCE)**.

### Impact

The severity of a file upload vulnerability depends on several factors:

- Which aspect of the file the website fails to validate properly (size, type, contents, etc.)
- What restrictions are imposed on the file once it has been successfully uploaded
- Whether uploaded files are stored in a web-accessible directory
- Whether the filename is validated correctly

Possible impacts include:

- Remote Code Execution via web shell upload
- Overwriting critical files if filenames are not validated
- Uploading files to unintended locations if combined with directory traversal
- Access to sensitive application data

---

## File Handling

How uploaded files are handled by the server determines the exploitability of the vulnerability.

### Static File Handling

Files are served directly to users **without execution**.

Example:

- Images
- Documents
- Static assets

The server returns the file contents exactly as stored.

Example request:

```
GET /files/avatar.png
```

---

### Dynamic File Handling

Files are processed or executed by the server before being returned.

Examples:

- PHP files
- ASP files
- JSP files

If an attacker uploads executable code (such as a **web shell**), the server may execute it.

---

## Remote Code Execution via Web Shell Upload

If the server allows executable files to be uploaded and accessed, an attacker can upload a **web shell**.

Example PHP payload:

```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

After uploading the malicious file, the attacker accesses it through the browser.

Example request:

```
GET /files/avatars/exploit.php
```

If the server executes the file, the attacker can run arbitrary commands or read sensitive data.

---

## Web Shell Upload via Content-Type Restriction Bypass

Some applications attempt to restrict uploads by checking the **MIME type** of the file.

Example restriction:

```
image/jpeg
image/png
```

However, attackers can modify the request to bypass this check.

In the upload request:

```
Content-Type: image/jpeg
```

Even though the file is actually a **PHP script**, the server may accept it because it trusts the client-supplied MIME type.

If the file is later executed by the server, this results in **remote code execution**.

---

## Web Shell Upload via Path Traversal

Some upload mechanisms allow attackers to manipulate the **file path** during upload.

Example modified request header:

```
Content-Disposition: form-data; name="avatar"; filename="../exploit.php"
```

This attempts to upload the file outside the intended directory.

If the application performs insufficient validation, the file may be written to a higher directory such as:

```
/files/exploit.php
```

If the server executes files in that location, the attacker gains code execution.

Traversal sequences may be obfuscated using encoding.

Example:

```
filename="..%2fexploit.php"
```

---

## Web Shell Upload via Extension Blacklist Bypass

Some applications block dangerous extensions such as:

```
.php
.jsp
.asp
```

Attackers may bypass this by uploading configuration files that redefine how the server handles file extensions.

Example using `.htaccess` on Apache:

```
AddType application/x-httpd-php .l33t
```

This instructs the server to execute `.l33t` files as PHP.

The attacker can then upload a file such as:

```
exploit.l33t
```

which will be executed by the server.

---

## Web Shell Upload via Obfuscated File Extension

Applications may validate file extensions such as:

```
.jpg
.png
```

Attackers can bypass this using techniques such as **null byte injection**.

Example filename:

```
exploit.php%00.jpg
```

The application may interpret the filename differently during validation and execution, allowing the PHP file to be uploaded and executed.

---

## Remote Code Execution via Polyglot File Upload

A **polyglot file** is a file that is valid in multiple formats simultaneously.

For example, an attacker can create an image that contains a PHP payload in its metadata.

Example using ExifTool:

```
exiftool -Comment="<?php echo file_get_contents('/home/carlos/secret'); ?>" image.jpg -o polyglot.php
```

The file appears to be a valid image but contains executable PHP code.

If the server executes the uploaded file, the embedded payload will run.