# 迁移工具
通过gs_rep_portal工具包安装各个迁移工具  
切换用户进行操作root用户无法成功进行迁移  
```
su - omm
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
cd /opt/portal
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

## 全量迁移-chameleon
vi /home/omm/.pg_chameleon/configuration/default_1.yml
```
pid_dir: /opt/portal/workspace/1/pid/
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
    out_dir: /opt/portal/workspace/1/tmp/
    csv_dir: /opt/portal/workspace/1/tmp/
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
su omm -c"/opt/portal/tools/chameleon/chameleon-6.0.0/venv/bin/chameleon create_replica_schema --config default_1"
# 添加schema源
su omm -c"/opt/portal/tools/chameleon/chameleon-6.0.0/venv/bin/chameleon add_source --config default_1 --source mysql"
# 执行全量迁移
su omm -c"/opt/portal/tools/chameleon/chameleon-6.0.0/venv/bin/chameleon init_replica --config default_1 --source mysql"
```
## 增量迁移-gs_replicate


# DataKit工具
