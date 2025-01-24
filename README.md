# xunruicms_rce
The RCE is connected through the CMS installation interface
# Xunrui CMS Installation Vulnerability

## Affected Range:
- Xunrui CMS Website Edition/Popular Edition v4.6.3 (2024-12-17)

## Payload:
```php ;-- ---'];phpinfo();['```

Vulnerability Reproduction:
Normal installation of .database.php
During installation, the process immediately jumps to step 3.
File After Successful Exploitation:
The file after exploitation shows how the vulnerability is exploited.
Code Analysis:
In tracing the code, it is found that the issue occurs when writing to . The variable is set to the database name provided by the user, which is controllable.database.php$data['db_name']

However, bypassing the database creation query is required.

Code Flow:
The code checks for the support of the MySQLi component and attempts to connect to MySQL.
The second checks if the entered database name exists. If not, it executes an unfiltered SQL statement. At this point, entering a new database name can bypass the detection, allowing an SQL query to be executed.elseifCREATE TABLE
Thus, the exploit is:
