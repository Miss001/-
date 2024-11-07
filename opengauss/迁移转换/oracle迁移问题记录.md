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

# 2.分区表导入错误
- 报错信息
```
gsql:./schema/partitions/partition.sql:27: ERROR:  syntax error at or near "MAXVALUE"
LINE 2: ...1 00:00:00','SYYYY-MM-DD HH24:MI:SS') AND DATES < MAXVALUE A...
                                                             ^
total time: 26  ms
ERROR: an error occurs when importing file ./schema/partitions/partition.sql.
```
- 报错原因
分区表导出时，ora2pg转换为继承表，不存在maxvalue值
- 处理
手动调整建表语句

# 3.导入表数据表不存在，实际数据库存在表
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
ora2pg默认从public 中查询表，配置文件中的PG_SCHEMA不生效
- 处理
修改配置文件ora2pg.conf
```
EXPORT_SCHEMA	1
SCHEMA	ROOT
CREATE_SCHEMA  0
COMPILE_SCHEMA	1
PG_SCHEME  test
```

# 4.导入数据字符集错误
- 报错信息
```
Wide character in subroutine entry at /root/perl5/lib/perl5/Ora2Pg.pm line 16300.
Issuing rollback() due to DESTROY without explicit disconnect() of DBD::Oracle::db handle (DESCRIPTION=(ADDRESS=(HOST=192.168.131.135)(PROTOCOL=tcp)(PORT=1521))(CONNECT_DATA=(SID=helowin))) at /root/perl5/lib/perl5/Ora2Pg.pm line 16300
```
- 报错原因  
oracle 字符集为：NLS_NCHAR = AL32UTF8
opengauss 数据库字符集为：SQL_ASCII
Perl 处理 UTF-8 字符时未正确设置编码，导致宽字符（如中文或其他非 ASCII 字符）在程序中被错误处理
- 处理
创建数据库时指定编码为UTF8
```
CREATE DATABASE dbname WITH ENCODING = 'UTF8';
```
