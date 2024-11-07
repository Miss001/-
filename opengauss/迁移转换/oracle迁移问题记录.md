# 1.分区表导入语法错误
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
分区表导出时，ora2pg会将语法转为postgresql语法而不是兼容oracle的opengauss语法。
- 处理  
手动调整建表语句

# 2.导入表数据表不存在，实际数据库存在表
- 报错信息
```
[2024-11-06 20:17:49] Isolation level: SET TRANSACTION ISOLATION LEVEL SERIALIZABLE
[2024-11-06 20:17:49] Retrieving partitions information...
[2024-11-06 20:18:07] Truncating table EMP_BONUS...
[2024-11-06 20:18:07] Looking how to retrieve data from EMP_BONUS...
[2024-11-06 20:18:07] DEGUG: Query sent to Oracle: SELECT "EMPLOYEE_ID","BONUS_YEAR","SALARY","BONUS" FROM "ROOT"."EMP_BONUS" a
[2024-11-06 20:18:07] Fetching all data from EMP_BONUS tuples...
[2024-11-06 20:18:07] DEBUG: number of rows 0 extracted from table EMP_BONUS
[2024-11-06 20:18:07] Dumping data from table EMP_BONUS into PostgreSQL table EMP_BONUS...
[2024-11-06 20:18:07] Setting client_encoding to UTF8...
[2024-11-06 20:18:07] Disabling synchronous commit when writing to PostgreSQL...
[2024-11-06 20:18:07] DEBUG: Creating output for 10000 tuples
[2024-11-06 20:18:07] DEBUG: Sending COPY bulk output directly to PostgreSQL backend
DBD::Pg::db do failed: ERROR:  relation "emp_bonus" does not exist at /root/perl5/lib/perl5/Ora2Pg.pm line 16296.
[2024-11-06 20:18:07] FATAL: ERROR:  relation "emp_bonus" does not exist
```
- 报错原因
- 处理
