# 安装gs_rep_portal
**安装java**
```
yum search java|grep jdk
yum install -y java-11-openjdk*
java -version
```
**portal**
#下载解压
```
cd /opt
wget https://opengauss.obs.cn-south-1.myhuaweicloud.com/6.0.0/tools/centos7/PortalControl-6.0.0-x86_64.tar.gz
tar -zxvf PortalControl-6.0.0-x86_64.tar.gz
cd portal
```
#修改工具安装包路径  
vi config/toolspath.properties
```
chameleon.install.path=/opt/portal/tools/chameleon/
chameleon.venv.path=/ops/portal/tools/chameleon/chameleon-${tools.version}/
chameleon.pkg.path=/opt/portal/pkg/chameleon/

debezium.path=/opt/portal/tools/debezium/
confluent.path=/opt/portal/tools/debezium/confluent-5.5.1/
confluent.install.path=/opt/portal/tools/debezium/
connector.path=/opt/portal/tools/debezium/plugin/
connector.mysql.path=/opt/portal/tools/debezium/plugin/debezium-connector-mysql/
connector.opengauss.path=/opt/portal/tools/debezium/plugin/debezium-connector-opengauss/

debezium.pkg.path=/opt/portal/pkg/debezium/

datacheck.install.path=/opt/portal/tools/
datacheck.path=/opt/portal/tools/gs_datacheck-${tools.version}/
datacheck.pkg.path=/opt/portal/pkg/datacheck/                                                              
```
# 安装工具
```
sh gs_rep_portal.sh install_mysql_full_migration_tools_offline 1 &
sh gs_rep_portal.sh install_mysql_incremental_migration_tools_offline 1v&
sh gs_rep_portal.sh install_mysql_reverse_migration_tools_offline 1 &
sh gs_rep_portal.sh install_mysql_datacheck_tools_offline	1 &
```

