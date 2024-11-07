
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
- 原理：基于Ora2Pg优化，通过连接Oracle数据库，自动扫描并提取其中的对象结构及数据，产生SQL脚本，通过手动或自动的方式将其应用到opengauss
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

# Set this directive to a file containing PL/SQL Oracle Code like function,
# procedure or a full package body to prevent Ora2Pg from connecting to an
# Oracle database end just apply his conversion tool to the content of the
# file. This can only be used with the following export type: PROCEDURE,
# FUNCTION or PACKAGE. If you don't know what you do don't use this directive.
#INPUT_FILE     ora_plsql_src.sql

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

# Enable/disable the CREATE SCHEMA SQL order at starting of the output file.
# It is enable by default and concern on TABLE export type.
CREATE_SCHEMA  0

# Enable this directive to force Oracle to compile schema before exporting code.
# When this directive is enabled and SCHEMA is set to a specific schema name, 
# only invalid objects in this schema will be recompiled. If SCHEMA is not set
# then all schema will be recompiled. To force recompile invalid object in a
# specific schema, set COMPILE_SCHEMA to the schema name you want to recompile.
# This will ask to Oracle to validate the PL/SQL that could have been invalidate
# after a export/import for example. The 'VALID' or 'INVALID' status applies to
# functions, procedures, packages and user defined types.
COMPILE_SCHEMA	1

# By default if you set EXPORT_SCHEMA to 1 the PostgreSQL search_path will be
# set to the schema name exported set as value of the SCHEMA directive. You can
# defined/force the PostgreSQL schema to use by using this directive.
#
# The value can be a comma delimited list of schema but not when using TABLE
# export type because in this case it will generate the CREATE SCHEMA statement
# and it doesn't support multiple schema name. For example, if you set PG_SCHEMA
# to something like "user_schema, public", the search path will be set like this
#        SET search_path = user_schema, public;
# forcing the use of an other schema (here user_schema) than the one from Oracle
# schema set in the SCHEMA directive. You can also set the default search_path
# for the PostgreSQL user you are using to connect to the destination database
# by using:
#        ALTER ROLE username SET search_path TO user_schema, public;
#in this case you don't have to set PG_SCHEMA.
PG_SCHEME  test


#------------------------------------------------------------------------------
# OUTPUT SECTION (Control output to file or PostgreSQL database)
#------------------------------------------------------------------------------

# Define the following directive to send export directly to a PostgreSQL
# database, this will disable file output. Note that these directives are only
# used for data export, other export need to be imported manually through the
# use og psql or any other PostgreSQL client.
PG_DSN          dbi:Pg:dbname=oraclemode_db;host=192.168.131.128;port=15400
PG_USER         root
PG_PWD          root@@123

# By default all output is dump to STDOUT if not send directly to postgresql
# database (see above). Give a filename to save export to it. If you want
# a Gzip'd compressed file just add the extension .gz to the filename (you
# need perl module Compress::Zlib from CPAN). Add extension .bz2 to use Bzip2
# compression.
OUTPUT          output.sql

# Base directory where all dumped files must be written
OUTPUT_DIR      /home/omm/ora2og

#------------------------------------------------------------------------------
# POSTGRESQL FEATURE SECTION (Control which PostgreSQL features are available)
#------------------------------------------------------------------------------

# Set the PostgreSQL major version number of the target database. Ex: 9.6 or 10
# Default is current major version at time of a new release. This replace the
# old PG_SUPPORTS_* configuration directives.
PG_VERSION	9.2
```
- 测试连接
```
cd /home/omm/ora2og
ora2pg -t SHOW_VERSION -c config/ora2pg.conf
```
- 导出对象结构:生成的迁移报告在 reports目录下
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
- 导入对象结构至opengauss中  
修改import_all.sh
```
EXPORT_TYPE="SEQUENCE TABLE VIEW GRANT TRIGGER FUNCTION PROCEDURE PARTITION "
AUTORUN=0
NAMESPACE=.
NO_CONSTRAINTS=0
IMPORT_INDEXES_AFTER=0
DEBUG=0
IMPORT_SCHEMA=0
IMPORT_DATA=0
IMPORT_CONSTRAINTS=0
NO_DBCHECK=0
OPENGAUSS=1
```
- 执行导入
```
cd /home/omm
chown -R omm:dbgrp ora2og/
#使用omm 运行gsql导入对象
su - omm
#此命令会通过交互式按顺序导入表结构索引等
sh import_all.sh -h 192.168.131.128 -p 15400 -o root -w root@@123 -d oraclemode_db -n test -f
```
- 导出数据
```
ora2pg -p -t DATA -o data.sql -b ./data -c ./config/ora2pg.conf
```
- 导入数据
```
ora2pg -c config/ora2pg.conf -t COPY --pg_dsn "dbi:Pg:dbname=oraclemode_db;host=192.168.131.128;port=15400" --pg_schema test --pg_user root
```

# 增量迁移
```
ora2pg -c ora2pg.conf -t COPY --scn "TO_TIMESTAMP('2021-12-01 00:00:00', 'YYYY-MM-DD HH:MI:SS')"
```
