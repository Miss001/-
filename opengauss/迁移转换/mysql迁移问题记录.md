# 1.Caused by: java.lang.StringIndexOutOfBoundsException: begin 0, end -1, length 0
- 报错信息
```
  [2024-11-04 21:40:46,455] ERROR Producer failure (io.debezium.pipeline.ErrorHandler:31)  
io.debezium.DebeziumException: java.lang.StringIndexOutOfBoundsException: begin 0, end -1, length 0  
	at io.debezium.pipeline.source.AbstractSnapshotChangeEventSource.execute(AbstractSnapshotChangeEventSource.java:87)  
	at io.debezium.pipeline.ChangeEventSourceCoordinator.doSnapshot(ChangeEventSourceCoordinator.java:153)  
	at io.debezium.pipeline.ChangeEventSourceCoordinator.executeChangeEventSources(ChangeEventSourceCoordinator.java:135)  
	at io.debezium.pipeline.ChangeEventSourceCoordinator.lambda$start$0(ChangeEventSourceCoordinator.java:108)  
	at java.base/java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:515)  
	at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264)  
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128)  
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)  
	at java.base/java.lang.Thread.run(Thread.java:834)  
Caused by: java.lang.StringIndexOutOfBoundsException: begin 0, end -1, length 0  
	at java.base/java.lang.String.checkBoundsBeginEnd(String.java:3319)  
	at java.base/java.lang.String.substring(String.java:1874)  
	at io.debezium.connector.mysql.GtidSet.getUuid(GtidSet.java:255)  
	at io.debezium.connector.mysql.GtidSet.needModifiedMaxTransactionId(GtidSet.java:259)  
	at io.debezium.connector.mysql.GtidSet.modifiedGtidSet(GtidSet.java:212)  
	at io.debezium.connector.mysql.MySqlSnapshotChangeEventSource.lambda$determineSnapshotOffset$4(MySqlSnapshotChangeEventSource.java  
:303)  
	at io.debezium.jdbc.JdbcConnection.query(JdbcConnection.java:577)
	at io.debezium.jdbc.JdbcConnection.query(JdbcConnection.java:518)
	at io.debezium.connector.mysql.MySqlSnapshotChangeEventSource.determineSnapshotOffset(MySqlSnapshotChangeEventSource.java:289)
	at io.debezium.connector.mysql.MySqlSnapshotChangeEventSource.determineSnapshotOffset(MySqlSnapshotChangeEventSource.java:49)
	at io.debezium.relational.RelationalSnapshotChangeEventSource.doExecute(RelationalSnapshotChangeEventSource.java:119)
	at io.debezium.pipeline.source.AbstractSnapshotChangeEventSource.execute(AbstractSnapshotChangeEventSource.java:78)
	... 8 more
```
- 原因  
  在配置文件中配置binlog name和position,gtid为空.同时mysql 未开启gtid_mode=on。程序读取t_gtid_set为空报错
- 处理
  设置开启mysql的gtid
```
#开启gtid
#查看状态
show global variables like '%gtid_mode%';
show global variables like '%enforce_gtid_consistency%';

#修改状态（主从分别执行完才能进行下一步）
set global enforce_gtid_consistency =warn;

#查看日志确认无警告后执行
set global enforce_gtid_consistency = on;
set global gtid_mode = off_permissive;
set global gtid_mode=on_permissive;

#检查
show status like '%ongoing_anonymous_transaction_count%'; 都为0才可执行下一步
flush logs;

#开启gtid
set global gtid_mode=on;

#修改配置my.cnf
gtid_mode = on
enforce-gtid-consistency=on
```

# 2.存储过程视图函数迁移报错：ERROR:  role "root@%" does not exist
- 报错信息   
```
2024-11-08 05:12:43.828 672de41b.5060 mysqlmode_db 139936769210112 dn_6001 0 dn_6001 42704  3096224743818599 [BACKEND] STATEMENT:  CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`%` SQL SECURITY DEFINER VIEW `test`.`v_tmp1` AS select `test`.`tmp1`.`user_id` AS `user_id`,`test`.`tmp1`.`username` AS `username`,`test`.`tmp1`.`price` AS `price`,`test`.`tmp1`.`createdtime` AS `createdtime` from `test`.`tmp1`
2024-11-08 05:12:44.702 672de41b.5060 mysqlmode_db 139936769210112 dn_6001 0 dn_6001 42704  3096224743818601 [BACKEND] ERROR:  role "root@%" does not exist
```
- 报错原因   
  迁移存储过程视图函数时定义的用户角色@语法不兼容   
- 处理
```
1.database中执行：set b_compatibility_user_host_auth to on;
2.创建用户：create user `root`@`%` with password 'root@@123';
3.授权：grant all privileges to `root`@`%`;
```

# 3.增量迁移报错：
- 报错信息
```
2024-11-08 06:24:44.632 672df4fc.5060 mysqlmode_db 139936621655808 PostgreSQL JDBC Driver 0 dn_6001 3F001  0 [BACKEND] ERROR:  schema "sch_chameleon" does not exist at character 56
2024-11-08 06:24:44.632 672df4fc.5060 mysqlmode_db 139936621655808 PostgreSQL JDBC Driver 0 dn_6001 3F001  0 [BACKEND] STATEMENT:  select t_binlog_name,i_binlog_position,t_gtid_set from sch_chameleon.t_replica_batch
```
- 报错原因
  执行完`sh gs_mysync.sh start workid`后默认会删除`sch_chameleon`模式
- 处理

## 遗留问题
### 未启用gtid时增量迁移失败
### 全量迁移前才开启gtid,全量迁移正常运行,增量迁移时consumer消费时一直无法读取到topic中的数据写入opengauss;将表数据全清空,重建表测试后consumer消费正常   
