## 1.Caused by: java.lang.StringIndexOutOfBoundsException: begin 0, end -1, length 0
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
  mysql 未开启gtid_mode=on
- 处理
  
