# gs_rep_portal工具
**全量迁移**  
修改plan配置的数据库连接信息    
vi /opt/portal/workspace/1/config/migrationConfig.properties  
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
执行迁移任务
```
cd /opt/portal
启动准备动作时的命令：
sh gs_rep_portal.sh start_kafka a
sh gs_rep_portal.sh start_mysql_full_migration 1 &
```

# DataKit工具
