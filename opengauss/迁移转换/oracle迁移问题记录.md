# 1.gsql:./schema/tables/table.sql:38: ERROR:  syntax error at or near ";" LINE 6: ) PARTITION BY RANGE (dates) ;
- 报错信息
```
Running: gsql --single-transaction  -h 192.168.131.128 -p 15400 -U root -W root@@123 -d oraclemode_db -f ./schema/tables/table.sql
SET
SET
CREATE TABLE
CREATE TABLE
CREATE TABLE
CREATE TABLE
gsql:./schema/tables/table.sql:38: ERROR:  syntax error at or near ";"
LINE 6: ) PARTITION BY RANGE (dates) ;
                                     ^
total time: 19  ms
ERROR: an error occurs when importing file ./schema/tables/table.sql
```
- 报错原因  
分区表导入时，ora2pg会将语法转为postgresql语法而不是兼容oracle的opengauss语法。
- 处理  
手动调整建表语句
