
# 语法差异转换

|                          | opengauss6.0.0(oracle兼容环境)                               | oracle11          |
| ------------------------ | ------------------------------------------------------------ | ----------------- |
| rowid                    |  无                                                          | rowid             |
| >= ,<=                   | 不允许包含空格                                                | 可包含空格，如（> =,< =） |
| dual                     | select 1                                                     | select 1 from DUAL      |
| rownum                   | SELECT rownum,a.* FROM test a;<br/>SELECT * FROM test  WHERE rownum<=2; | SELECT rownum,a.* FROM test a;<br/>SELECT * FROM test  WHERE rownum < =2 ; |
| sys_guid                 | 无                                                           | select sys_guid() from dual     |
| nvl                      | NVL(NULL,'1')                                                | NVL(NULL,'1')                   |
| instr                    | INSTR('APP', 'P',1,1)                                        | INSTR('APP', 'P',1,1)            |
| decode                   | SELECT<br/>  DECODE(id,1,'Pending',2,'Paid',3 ,'Shipped','Unknown')as name<br/>FROM test | SELECT<br/>  DECODE(id,1,'Pending',2,'Paid',3 ,'Shipped','Unknown')as name<br/>FROM test |
| syddate                  | SYSDATE                                                      | SYSDATE                       |
| trunc(date)              | TRUNC(sysdate)                                               | TRUNC(sysdate)                |
| months_between           | SELECT EXTRACT(YEAR FROM AGE('2024-01-17 14:45:30'::TIMESTAMP, '2023-10-01 00:00:00'::TIMESTAMP)) * 12 +<br/>       EXTRACT(MONTH FROM AGE('2024-01-17 14:45:30'::TIMESTAMP, '2023-10-01 00:00:00'::TIMESTAMP))+<br/>       EXTRACT(DAY FROM AGE('2024-01-17 14:45:30'::TIMESTAMP, '2023-10-01 00:00:00'::TIMESTAMP)) /30<br/>       AS months_difference; | select MONTHS_BETWEEN(to_date('2024-01-17 14:45:30','yyyy-mm-dd hh24:mi:ss'),<br/>                      to_date('2023-10-01 00:00:00','yyyy-mm-dd hh24:mi:ss')) <br/>  from dual |
| add_months               | select ADD_MONTHS(sysdate, 1)                                | select ADD_MONTHS(sysdate, 1) from dual      |
| last_day                 | select LAST_DAY(trunc(sysdate))                              | select LAST_DAY(trunc(sysdate)) from dual    |
| 日期相减（天）             | select extract(EPOCH  from sysdate - to_date('2023-01-01','yyyy-mm-dd'))/86400 | select sysdate - to_date('2023-01-01','yyyy-mm-dd') from dual |
| 时间加减（天）            | select sysdate+1                                             | select sysdate+1 from dual                      |
| chr(0)                   | 读取为NULL字符                                               | 读取为空字符(chr(0))                                   |
| 空字符串（''）            | 读取为 NULL                                                  | 读取为 NULL                                        |
| 尾部空格字符串（' '）      | 读取为空格字符串（' '）                                      | 读取为空格字符串（' '）                                   |
| NULL值与非NULL值字符拼接  | 结果为：非NULL值                                             | 结果为：非NULL值                                         |
| 递归查询                 | select id, parent_id ,level as recursion_level<br/>from tmp3  start with parent_id IS NULL<br/>  connect by prior id = parent_id | select id, parent_id ,level as recursion_level<br/>from tmp3  start with parent_id IS NULL<br/>  connect by prior id = parent_id |
| 分层查询                 | SELECT "DATE"<br/>  FROM generate_series(timestamp '2010-01-01',<br/>                       timestamp '2010-02-01',<br/>                       interval '1 day') s("DATE"); | SELECT to_date('2010-01-01','yyyy-mm-dd') + level - 1<br/>FROM dual<br/>CONNECT BY LEVEL <= to_date('2010-02-01','yyyy-mm-dd') - to_date('2010-01-01','yyyy-mm-dd') + 1; |
| (+)进行外连接            | SELECT *<br/>FROM<br/>  tmp1 a,<br/>  tmp2 b<br/>WHERE<br/>  b.id (+)= a.user_id | SELECT *<br/>FROM<br/>  tmp1 a,<br/>  tmp2 b<br/>WHERE<br/>  b.id (+)= a.user_id |
| MERGE 操作               | MERGE INTO users A<br/>USING (<br/>SELECT 'jane_doe' AS username, '111@example.com' AS email, now() AS last_login<br/>)B<br/>ON A.username = B.username<br/>WHEN MATCHED THEN<br/>    UPDATE SET email = B.email, last_login = B.last_login<br/>WHEN NOT MATCHED THEN<br/>    INSERT (username, email, last_login)<br/>        VALUES (B.username, B.email, B.last_login); | MERGE INTO users A<br/>USING (<br/>    SELECT 'jane_doe' AS username, '111@example.com' AS email, SYSDATE AS last_login<br/>    FROM dual<br/>) B<br/>ON (A.username = B.username)<br/>WHEN MATCHED THEN<br/>    UPDATE SET A.email = B.email, A.last_login = B.last_login<br/>WHEN NOT MATCHED THEN<br/>    INSERT (username, email, last_login)<br/>    VALUES (B.username, B.email, B.last_login); |
| 多表关联更新              | UPDATE product p<br/>SET (net_price, segment_name)= <br/>     (select t.segment, p.price - p.price * s.discount<br/>           from product_segment s, segment t <br/>           where s.segment=t.segment and p.segment_id = s.id)<br/>WHERE EXISTS(select 1<br/>           from product_segment s, segment t <br/>           where s.segment=t.segment and p.segment_id = s.id) | UPDATE product p<br/>SET (net_price, segment_name)= <br/>     (select t.segment, p.price - p.price * s.discount<br/>           from product_segment s, segment t <br/>           where s.segment=t.segment and p.segment_id = s.id)<br/>WHERE EXISTS(select 1<br/>           from product_segment s, segment t <br/>           where s.segment=t.segment and p.segment_id = s.id) |
| listagg distinct         | with  <br/>t as(  <br/>select 1 as i ,'a' as id  <br/>union ALL  <br/>select 1 as i ,'a' as id  <br/>union ALL  <br/>select null as i,'a' as id  <br/>union ALL  <br/>select 2 as i,'b' as id  <br/>union ALL  <br/>select 3 as i,'b' as id  <br/>union ALL  <br/>select 10 as i,'b' as id  <br/>)     <br/>select listagg(i,',') within group (order by i asc)    <br/>from (select distinct * from t) group by id | with<br/>t as(<br/>select 1 as i ,'a' as id from dual<br/>union ALL<br/>select 1 as i ,'a' as id from dual<br/>union ALL<br/>select null as i,'a' as id from dual<br/>union ALL<br/>select 2 as i,'b' as id from dual<br/>union ALL<br/>select 3 as i,'b' as id from dual<br/>union ALL<br/>select 10 as i,'b' as id from dual<br/>)<br/>select listagg(i,',') within group (order by i asc) <br/>from (select distinct * from t) group by id |
| wm_concat(distinct)      | with<br/>t as(<br/>select 1 as i ,'a' as id<br/>union ALL<br/>select 1 as i ,'a' as id<br/>union ALL<br/>select null as i,'a' as id<br/>union ALL<br/>select 2 as i,'b' as id<br/>union ALL<br/>select 3 as i,'b' as id<br/>union ALL<br/>select 10 as i,'b' as id<br/>)<br/>select string_agg(distinct i::text,',' order by i::text asc )  from t  group by id | with<br/>t as(<br/>select 1 as i ,'a' as id from dual<br/>union ALL<br/>select 1 as i ,'a' as id from dual<br/>union ALL<br/>select null as i,'a' as id from dual<br/>union ALL<br/>select 2 as i,'b' as id from dual<br/>union ALL<br/>select 3 as i,'b' as id from dual<br/>union ALL<br/>select 10 as i,'b' as id from dual<br/>)<br/>select wm_concat(distinct i) <br/>from t group by id |
| nologging                | create unlogged table test1 as<br/>select * from test where rownum<=10 | create table test1 nologging as<br/>select * from test where rownum<=10 |


# 迁移工具
## ora2og
- 原理：基于Ora2Pg开发扩展，通过连接Oracle数据库，自动扫描并提取其中的对象结构及数据，产生SQL脚本，通过手动或自动的方式将其应用到opengauss
- 只适合做简单的语法转换对象迁移，对于复杂语法的转换需要人工校验，增量迁移困难
- 部分语法实际未转换成opengauss兼容的语法而是postgresql语法
### 全量迁移
root用户执行，omm无权限执行perl
- 创建迁移项目
```
cd /home/omm
ora2pg --init_project ora2og
```
- 修改配置文件  
vi /home/omm/ora2og/config/ora2pg.conf
```
#------------------------------------------------------------------------------
# INPUT SECTION (Oracle connection or input file)
#------------------------------------------------------------------------------
# Set the Oracle home directory
ORACLE_HOME     /usr/lib/oracle/21/client64
# Set Oracle database connection (datasource, user, password)
ORACLE_DSN      dbi:Oracle:host=192.168.131.135;sid=helowin;port=1521
ORACLE_USER     root
ORACLE_PWD      123456
# Trace all to stderr
DEBUG           1

#------------------------------------------------------------------------------
# SCHEMA SECTION (Oracle schema to export and use of schema in PostgreSQL)
#------------------------------------------------------------------------------
# Export Oracle schema to PostgreSQL schema
EXPORT_SCHEMA	1
# Oracle schema/owner to use
SCHEMA	ROOT
CREATE_SCHEMA  0
COMPILE_SCHEMA	1
#PG_SCHEME  test

#------------------------------------------------------------------------------
# OUTPUT SECTION (Control output to file or PostgreSQL database)
#------------------------------------------------------------------------------
PG_DSN          dbi:Pg:dbname=oraclemode_db;host=192.168.131.128;port=15400
PG_USER         root
PG_PWD          root@@123
OUTPUT          output.sql
OUTPUT_DIR      /home/omm/ora2og

#------------------------------------------------------------------------------
# POSTGRESQL FEATURE SECTION (Control which PostgreSQL features are available)
#------------------------------------------------------------------------------
PG_VERSION	9.2

```
- 测试连接
```
cd /home/omm/ora2og
ora2pg -t SHOW_VERSION -c config/ora2pg.conf
```
- 修改导出对象
cd /home/omm/ora2og  
vi  export_schema.sh
```
EXPORT_TYPE="SEQUENCE TABLE VIEW TRIGGER FUNCTION PROCEDURE PARTITION"
SOURCE_TYPE="SEQUENCE TABLE VIEW TRIGGER FUNCTION PROCEDURE PARTITION"
```
- 批量导出对象结构:生成的迁移报告在 reports目录下
```
cd /home/omm/ora2og
sh export_schema.sh
#导出序列
#ora2pg -p -t SEQUENCE -o sequence.sql -b ./schema/sequences -c ./config/ora2pg.conf
#导出表、索引
#ora2pg -p -t TABLE -o table.sql -b ./schema/tables -c ./config/ora2pg.conf
#导出包
#ora2pg -p -t PACKAGE -o package.sql -b ./schema/packages -c ./config/ora2pg.conf
#导出视图
#ora2pg -p -t VIEW -o view.sql -b ./schema/views -c ./config/ora2pg.conf
#导出权限
#ora2pg -p -t GRANT -o grant.sql -b ./schema/grants -c ./config/ora2pg.conf
#导出触发器
#ora2pg -p -t TRIGGER -o trigger.sql -b ./schema/triggers -c ./config/ora2pg.conf
#导出函数
#ora2pg -p -t FUNCTION -o function.sql -b ./schema/functions -c ./config/ora2pg.conf
#导出存储过程
#ora2pg -p -t PROCEDURE -o procedure.sql -b ./schema/procedures -c ./config/ora2pg.conf
#导出表空间
#ora2pg -p -t TABLESPACE -o tablespace.sql -b ./schema/tablespaces -c ./config/ora2pg.conf
#导出分区
#ora2pg -p -t PARTITION -o partition.sql -b ./schema/partitions -c ./config/ora2pg.conf
#导出type
#ora2pg -p -t TYPE -o type.sql -b ./schema/types -c ./config/ora2pg.conf
#导出物化视图
#ora2pg -p -t MVIEW -o mview.sql -b ./schema/mviews -c ./config/ora2pg.conf
#导出dblink
#ora2pg -p -t DBLINK -o dblink.sql -b ./schema/dblinks -c ./config/ora2pg.conf
#导出同义词
#ora2pg -p -t SYNONYM -o synonym.sql -b ./schema/synonyms -c ./config/ora2pg.conf
#导出字典
#ora2pg -p -t DIRECTORY -o directorie.sql -b ./schema/directories -c ./config/ora2pg.conf
```
- 修改import_all.sh 导入参数
```
EXPORT_TYPE="SEQUENCE TABLE VIEW TRIGGER FUNCTION PROCEDURE PARTITION"
OPENGAUSS=1
```
- 批量执行导入对象
```
cd /home/omm
chown -R omm:dbgrp ora2og/
#使用omm 运行gsql导入对象
su - omm
#此命令会通过交互式按顺序导入表结构索引等，导入数据需要切换用户执行ora2pg
sh import_all.sh -h 192.168.131.128 -p 15400 -o root -w root@@123 -d oraclemode_db -n test -f
```
- 批量导入数据
```
ora2pg -c config/ora2pg.conf -t COPY --pg_dsn "dbi:Pg:dbname=oraclemode_db;host=192.168.131.128;port=15400" --pg_schema root --pg_user root
```

- 单表结构导出至本地文件
```
ora2pg -c config/ora2pg.conf  -a test -b ./data -o output.sql
```
- 单表结构从本地文件导入数据库
```
cd /home/omm
chown -R omm:dbgrp ora2og/
su - omm
gsql --single-transaction  -h 192.168.131.128 -p 15400 -U root -W root@@123 -d oraclemode_db -c "SET search_path TO test; \i ./data/output.sql"
```
-单表数据导入数据库
```
#数据(无法导出成文件),-n为oracle的schema,-a 为表名
ora2pg -t COPY -c config/ora2pg.conf -n root -a test
```
# 增量迁移
原理：通过表时间字段查询至全量迁移后的变更数据导入  
      ora2pg21.1版本不支持通过--scn 增量迁移数据  
- 修改配置文件
vi  config/ora2pg.conf
```
# Support for include a WHERE clause filter when dumping the contents
# of tables. Value is construct as follow: TABLE_NAME[WHERE_CLAUSE], or
# if you have only one where clause for each table just put the where
# clause as value. Both are possible too. Here are some examples:
#WHERE  1=1	# Apply to all tables
WHERE	test[id>=11]	# Apply only on table TABLE_TEST
#WHERE	TABLE_TEST[ID1='001' OR ID1='002] DATE_CREATE > '2001-01-01' TABLE_INFO[NAME='test']
# The last applies two different where clause on tables TABLE_TEST and
# TABLE_INFO and a generic where clause on DATE_CREATE to all other tables
```
- 执行迁移
```
ora2pg  -t COPY -c config/ora2pg.conf -n root -a test 
```
