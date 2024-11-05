# 迁移工具
通过gs_rep_portal工具包安装各个迁移工具  
切换用户进行操作root用户无法成功进行迁移  
```
su - omm
```
## 全量迁移-chameleon（基础工具）
vi /home/omm/.pg_chameleon/configuration/default_1.yml
```
pid_dir: /home/omm/portal/workspace/1/pid/
log_dir: ~/.pg_chameleon/logs/
log_dest: file
log_level: info
log_days_keep: 10
rollbar_key: ''
rollbar_env: ''
dump_json: 'yes'
type_override: null
compress_properties:
  compresstype: 0
  compress_level: 0
  compress_chunk_size: 4096
  compress_prealloc_chunks: 0
  compress_byte_convert: false
  compress_diff_convert: false
pg_conn:
  host: 192.168.131.128
  port: '15400'
  user: root
  password: root@@123
  database: mysqlmode_db
  charset: utf8
  params: null
sources:
  mysql:
    readers: 4
    writers: 4
    retry: 3
    db_conn:
      host: 192.168.131.130
      port: '3306'
      user: root
      password: '123456'
      charset: utf8
      connect_timeout: 10
      database: test
    schema_mappings:
      delphis_mediterranea: loxodonta_africana
      test: test #不能是public
    limit_tables:
    - delphis_mediterranea.foo
    skip_tables:
    - delphis_mediterranea.bar
    enable_compress: false
    compress_tables:
    - delphis_mediterranea.foo
    grant_select_to:
    - usr_readonly
    lock_timeout: 120s
    my_server_id: 100
    replica_batch_size: 10000
    replay_max_rows: 10000
    batch_retention: 1 day
    copy_max_memory: 300M
    copy_mode: file
    out_dir: /home/omm/portal/workspace/1/tmp/
    csv_dir: /home/omm/portal/workspace/1/tmp/
    contain_columns: false
    column_split: ','
    sleep_loop: 1
    on_error_replay: continue
    on_error_read: continue
    auto_maintenance: disabled
    index_parallel_workers: 2
    gtid_enable: false
    type: mysql
    skip_events:
      insert:
      - delphis_mediterranea.foo
      delete:
      - delphis_mediterranea
      update: null
    keep_existing_schema: false
    migrate_default_value: true
    mysql_restart_config: false
    is_create_index: true
    index_dir: ~/.pg_chameleon/index/
    is_skip_completed_tables: false
    with_datacheck: false
    slice_size: 100000
    csv_files_threshold: null
    csv_dir_space_threshold: null
  pgsql:
    db_conn:
      host: localhost
      port: '5432'
      user: usr_replica
      password: never_commit_passwords
      database: db_replica
      charset: utf8
      connect_timeout: 10
      schema_mappings:
        loxodonta_africana: elephas_maximus
      limit_tables:
      - loxodonta_africana.foo
      skip_tables:
      - loxodonta_africana.bar
      copy_max_memory: 300M
      grant_select_to:
      - usr_readonly
      lock_timeout: 10s
      my_server_id: 100
      replica_batch_size: 3000
      replay_max_rows: 10000
      sleep_loop: 5
      batch_retention: 1 day
      copy_mode: file
      out_dir: /tmp
      type: pgsql
```
执行迁移任务  
```
# 创建schema
/home/omm/portal/tools/chameleon/chameleon-6.0.0/venv/bin/chameleon create_replica_schema --config default_1
# 添加schema源
/home/omm/portal/tools/chameleon/chameleon-6.0.0/venv/bin/chameleon add_source --config default_1 --source mysql
# 执行全量迁移
/home/omm/portal/tools/chameleon/chameleon-6.0.0/venv/bin/chameleon init_replica --config default_1 --source mysql
# 清除迁移临时数据
/home/omm/portal/tools/chameleon/chameleon-6.0.0/venv/bin/chameleon detach_replica --config default_1 --source mysql
```

## 增量迁移-confluent（基础工具）
启动zookeeper
```
/home/omm/portal/tools/debezium/confluent-5.5.1/bin/zookeeper-server-start -daemon /home/omm/portal/tools/debezium/confluent-5.5.1/etc/kafka/zookeeper.properties
```
启动kafka
```
/home/omm/portal/tools/debezium/confluent-5.5.1/bin/kafka-server-start -daemon /home/omm/portal/tools/debezium/confluent-5.5.1/etc/kafka/server.properties
```
注册schema
```
/home/omm/portal/tools/debezium/confluent-5.5.1/bin/schema-registry-start -daemon /home/omm/portal/tools/debezium/confluent-5.5.1/etc/schema-registry/schema-registry.properties
```
修改source端配置文件  
vi /home/omm/portal/workspace/1/config/debezium/mysql-source.properties
```
# name: source端连接器名称，debezium的原生参数，无默认值，可自定义，不同连接器需保证名称的唯一性
name=mysql-source-1
# connector.class: 连接器的启动类，debezium的原生参数，无默认值，示例中的值为mysql source connector对应的类名，不可修改
connector.class=io.debezium.connector.mysql.MySqlConnector
# database.hostname: mysql数据库主机ip，debezium的原生参数，无默认值，根据实际自定义修改
database.hostname=192.168.131.130
# database.port: mysql数据库端口，debezium的原生参数，默认值为3306，可根据实际自定义修改
database.port=3306
# database.user: mysql数据库用户，debezium的原生参数，无默认值，根据实际自定义修改
database.user=root
# database.password: mysql数据库用户密码，debezium的原生参数，无默认值，根据实际自定义修改
database.password=123456
# database.server.id: mysql数据库实例id，debezium的原生参数，取值为一个随机数，作为数据库的客户端id
# 连接器以该id加入MySQL集群，以便读取binlog，默认情况下，会生成5400到6400之间的一个随机数，建议显式设置该值
# 针对在当前MySQL集群指定的客户端id，该值必须唯一
database.server.id=12260
# database.server.name: mysql数据库实例名称，debezium的原生参数，无默认值，可自定义
database.server.name=mysql_server_1
# database.history.kafka.bootstrap.servers: kafka服务器地址，debezium的原生参数，无默认值，根据实际自定义修改
database.history.kafka.bootstrap.servers=127.0.0.1:9092
# database.history.kafka.topic: kafka历史topic名称，debezium的原生参数，无默认值，可自定义
database.history.kafka.topic=mysql_server_1_history
# include.schema.changes: 指定连接器是否将变更发布到kafka主题中，debezium的原生参数，boolean类型，默认值为true，不可修改
include.schema.changes=true
# tasks.max: 连接器创建的最大任务数，debezium的原生参数，默认值为1，MySQL connector通常为单任务，不建议修改
tasks.max=1
# snapshot.mode：快照模式，debezium的原生参数，默认值为initial，此处需设置为schema_only，不可修改
snapshot.mode=schema_only
transforms=route
# transforms.route.type: kafka topic路由转发类型，debezium提供topic路由能力，不可修改
transforms.route.type=org.apache.kafka.connect.transforms.RegexRouter
# transforms.route.regex: kafka topic路由转发正则匹配表达式，正则匹配按照前缀匹配，debezium提供topic路由能力，不可修改
# 将ddl, dml及事务topic均路由转发至同一topic，其中正则表达式中的mysql_server与配置项database.server.name相对应
transforms.route.regex=^mysql_server_1(.*)
# transforms.route.replacement: kafka topic路由转发后的topic名称，debezium提供topic路由能力，可自定义
# 该参数与mysql-sink.properties的配置项topics相对应
transforms.route.replacement=mysql_server_1_topic
# snapshot.offset.binlog.filename: 新增参数，自定义配置快照点的binlog文件名
# 跟全量迁移chameleon配合时，取决于全量迁移后从sch_chameleon.t_replica_batch表中列t_binlog_name中查询的binlog文件名
snapshot.offset.binlog.filename=binlog.000009
# snapshot.offset.binlog.position: 新增参数，自定义配置快照点的binlog位置
# 跟全量迁移chameleon配合时，取决于全量迁移后从sch_chameleon.t_replica_batch表中列i_binlog_position中查询的binlog位置
snapshot.offset.binlog.position=94254
# 跟全量迁移chameleon配合时，取决于全量迁移后从sch_chameleon.t_replica_batch表中列executed_gtid_set中查询的gtid set
# 需注意最大事务号需减1
snapshot.offset.gtid.set=
```
启动source端
```
sh /home/omm/portal/tools/debezium/confluent-5.5.1/bin/connect-standalone  /home/omm/portal/workspace/1/config/debezium/connect-avro-standalone.properties  /home/omm/portal/workspace/1/config/debezium/mysql-source.properties >source.log &
```
修改sink端配置文件
```
# name: source端连接器名称，debezium的原生参数，无默认值，可自定义，不同连接器需保证名称的唯一性
connector.class=io.debezium.connector.mysql.sink.MysqlSinkConnector
# tasks.max: 连接器创建的最大任务数，debezium的原生参数，默认值为1，MySQL connector通常为单任务，不建议修改
tasks.max=1
# topics: sink端从kafka抽取数据的topic，新增参数，String类型，无默认值
# 与mysql-source.properties的配置项transforms.route.replacement相对应
topics=mysql_server_1_topic
# opengauss.driver: openGauss驱动名，新增参数，String类型，无默认值，与JDBC驱动相对应，不可修改
opengauss.driver=org.opengauss.Driver
# opengauss.username: openGauss用户名，新增参数，String类型，无默认值，根据实际自定义修改
opengauss.username=root
# opengauss.password: openGauss用户密码，新增参数，String类型，无默认值，根据实际自定义修改
opengauss.password=root@@123
# opengauss.url: openGauss连接url，新增参数，String类型，无默认值，根据实际自定义修改，包括数据库ip，端口号，连接的数据库
opengauss.url=jdbc:opengauss://192.168.131.128:15400/mysqlmode_db?loggerLevel=OFF
# parallel.replay.thread.num: 并行回放默认线程数量，新增参数，int类型，默认为30，可自定义修改，取值需大于0
parallel.replay.thread.num=30
# xlog.location: 增量迁移停止时openGauss端lsn的存储文件路径，新增参数，String类型，无默认值，根据实际自定义修改，需保证文件有读写权限
xlog.location=/home/omm/portal/workspace/1/status/xlog.txt
# schema.mappings: mysql和openGauss的schema映射关系，与全量迁移chameleon配置相对应，新增参数，String类型，无默认值
# 用；区分不同的映射关系，用：区分mysql的database和openGauss的schema
# 例如chameleon的配置
# schema_mappings:
#   mysql_database1: opengauss_schema1
#   mysql_database2: opengauss_schema2
# 则sink端的schema.mappings参数需配置为
# schema.mappings=mysql_database1:opengauss_schema1;mysql_database2:opengauss_schema2
schema.mappings=test:test
# commit.process.while.running：布尔值，默认为false，通过该配置项选择是否上报迁移进度
commit.process.while.running=true
# sink.process.file.path：迁移进度文件的输出路径，只有commit.process.while.running=true时才起作用，默认在迁移插件同一目录下
sink.process.file.path=/home/omm/portal/workspace/1/status/incremental/
# commit.time.interval：迁移进度上报的时间间隔，取int型整数，默认为1，单位：s
commit.time.interval=1
# fail.sql.path：回放失败的sql语句输出路径，默认在迁移插件同一目录下
fail.sql.path=/home/omm/portal/workspace/1/status/incremental/
# create.count.info.path：记录源端日志生产起始点的文件读取路径，需与source端的此配置项相同，默认与迁移插件在同一目录下
create.count.info.path=/home/omm/portal/workspace/1/status/incremental/
# process.file.count.limit：进度目录下文件数目限制值，如果进度目录下的文件数目超过该值，工具启动时会按时间从早到晚删除多余的文件，默认值为
10
process.file.count.limit=10
# process.file.time.limit：进度文件保存时长，超过该时长的文件会在工具下次启动时删除，默认值为168，单位：小时
process.file.time.limit=168
# append.write：进度文件写入方式，true表示追加写入，false表示覆盖写入，默认值为false
append.write=false
# file.size.limit：文件大小限制，超过该限制值工具会另启新文件写入，默认为10，单位：兆
file.size.limit=10
# queue.size.limit: 存储kafka记录的队列的最大长度，新增参数，int类型，默认值为1000000，可自定义
queue.size.limit=1000000
# open.flow.control.threshold: 用于流量控制，新增参数，double类型，默认值为0.8，可自定义
# 当存储kafka记录的队列长度>最大长度queue.size.limit*该门限值时，将启用流量控制，暂停从kafka抽取数据
open.flow.control.threshold=0.8
# close.flow.control.threshold: 用于流量控制，新增参数，double类型，默认值为0.7，可自定义
# 当存储kafka记录的队列长度<最大长度queue.size.limit*该门限值时，将关闭流量控制，继续从kafka抽取数据
close.flow.control.threshold=0.7
#record.breakpoint.kafka.topic:断点记录kafka消息topic,可根据实际情况修改，默认值为bp_topic
record.breakpoint.kafka.topic=mysql_bp_1_topic
#record.breakpoint.kafka.attempts:从kafka读取断点topic的最大重试次数
record.breakpoint.kafka.attempts=3
#record.breakpoint.kafka.bootstrap.servers:kafka服务器地址，可根据实际情况修改，默认值为localhost:9092
record.breakpoint.kafka.bootstrap.servers=127.0.0.1:9092
#record.breakpoint.kafka.size.limit：断点记录Kafka的条数限制，超过该限制会触发删除Kafka的断点清除策略，删除无用的断点记录数据，单位：事务>万条数
record.breakpoint.kafka.size.limit=3000
#record.breakpoint.kafka.clear.interval：断点记录Kafka的时间限制，超过该限制会触发删除Kafka的断点清除策略，删除无用的断点记录数据，单位：>小时
record.breakpoint.kafka.clear.interval=1
# record.breakpoint.repeat.count.limit：断点续传时，查询待回放数据是否已在断点之前备回放的数据条数，默认值：50000
record.breakpoint.repeat.count.limit=50000
# wait.timeout.second：sink端数据库停止服务后迁移工具等待数据库恢复服务的最大时长，默认值：28800，单位：秒
wait.timeout.second=28800
# database.standby.hostnames：sink端数据库是主备部署时的备机ip列表，用逗号隔开，需与port列表一一对应，此配置项只对sink端是openGauss时起作>用，默认值：""，不配置此项时sink端只连接主节点
database.standby.hostnames=""
# database.standby.ports：sink端数据库是主备部署时的备机port列表，用逗号隔开，需与ip列表一一对应，此配置项只对sink端是openGauss时起作用，>默认值：""，不配置此项时sink端只连接主节点
database.standby.ports=""
```
启动sink端
```
/home/omm/portal/tools/debezium/confluent-5.5.1/bin/connect-standalone /home/omm/portal/workspace/1/config/debezium/connect-avro-standalone-1.properties  /home/omm/portal/workspace/1/config/debezium/mysql-sink.properties > sink.log &
```

**增量推送数据查看**
```
#查看创建的topic
sh /home/omm/portal/tools/debezium/confluent-5.5.1/bin/kafka-topics --list --bootstrap-server 127.0.0.1:9092
#删除创建的topic
sh /home/omm/portal/tools/debezium/confluent-5.5.1/bin/kafka-topics --delete --bootstrap-server 127.0.0.1:9092 --topic topic_name
#查看推送的数据
sh /home/omm/portal/tools/debezium/confluent-5.5.1/bin/kafka-console-consumer --bootstrap-server 127.0.0.1:9092  --topic __consumer_offsets --from-beginning --max-messages 10
```

## 集成工具gs_rep_portal迁移
修改任务（1）配置的数据库连接信息    
vi /home/omm/portal/workspace/1/config/migrationConfig.properties  
```
mysql.user.name=root
mysql.user.password=***
mysql.database.host=127.0.0.1
mysql.database.port=3306
mysql.database.name=test123
#mysql.database.table=

opengauss.user.name=test
opengauss.user.password=***
opengauss.database.host=127.0.0.1
opengauss.database.port=5432
opengauss.database.name=test1234
opengauss.database.schema=test123
# opengauss数据库是否为集群，可选择true/false，选择true时，需配置opengauss.database.standby.hostnames和opengauss.database.standby.ports
opengauss.database.iscluster=false
# opengauss数据库备机ip1,ip2,...，多个备机ip间用英文逗号隔开
# opengauss.database.standby.hostnames=127.0.0.2,127.0.0.3
# opengauss数据库备机端口port1,port2,...，多个备机port间用英文逗号隔开，注意需要与备机ip1,ip2,...保持对应
# opengauss.database.standby.ports=5432,5432
```
启动kafka
```
cd /home/omm/portal
sh gs_rep_portal.sh start_kafka 1
```
执行迁移任务
```
cd /home/omm/portal
#全量迁移
sh gs_rep_portal.sh start_mysql_full_migration 1 &
#全量校验
sh gs_rep_portal.sh start_mysql_full_migration_datacheck 1 &
#增量迁移
sh gs_rep_portal.sh start_mysql_incremental_migration 1 &
#增量校验
sh gs_rep_portal.sh start_mysql_incremental_migration_datacheck 1 &
```

## 全量迁移-gs_mysync
- 迁移完成后会将sch_chameleon清除
前提：启动kafka
```
sh gs_rep_portal.sh start_kafka
```
创建迁移任务
```
sh gs_mysync.sh install 1
```
执行迁移任务
```
sh gs_mysync.sh start 1
```

## 增量迁移-gs_replicate
- 实现原理：
  debezium mysql connector     
  source端：监控mysql数据库的binlog日志，并将数据（DDL和DML操作）以AVRO格式写入到kafka  
  sink端：从kafka读取AVRO格式数据（DDL和DML操作），并组装为事务，在openGauss端按照事务粒度并行回放
   
- 要求：  
  不支持skip_event, limit_table, skip_table等设置   
  MySQL参数设置要求为：log_bin=ON, binlog_format=ROW, binlog_row_image=FULL, gtid_mode = ON。若gtid_mode为off，则sink端按照事务顺序串行回放，会降低在线迁移性能  
  
前提：启动kafka
```
sh gs_rep_portal.sh start_kafka 1
```
创建迁移任务
```
sh gs_replicate.sh install mysql-opengauss 1
```
执行迁移任务
```
sh gs_replicate.sh start mysql-opengauss 1 
```
