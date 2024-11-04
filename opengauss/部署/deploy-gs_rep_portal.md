# 安装gs_rep_portal
**安装java**
```
yum search java|grep jdk
yum install -y java-11-openjdk*
java -version
```
**安装portal**  
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
tools.version=6.0.0
system.name=centos7
system.arch=x86_64

chameleon.install.path=/opt/portal/tools/chameleon/
chameleon.venv.path=/opt/portal/tools/chameleon/chameleon-${tools.version}/
chameleon.path=/home/omm/.pg_chameleon/
chameleon.pkg.url=https://opengauss.obs.cn-south-1.myhuaweicloud.com/latest/tools/${system.name}/chameleon-${tools.version}-${system.arch}.tar.gz
chameleon.pkg.path=/opt/portal/pkg/chameleon/
chameleon.pkg.name=chameleon-${tools.version}-${system.arch}.tar.gz

debezium.path=/opt/portal/tools/debezium/
confluent.path=/opt/portal/tools/debezium/confluent-5.5.1/
confluent.install.path=/opt/portal/tools/debezium/
connector.path=/opt/portal/tools/debezium/plugin/
connector.mysql.path=/opt/portal/tools/debezium/plugin/debezium-connector-mysql/
connector.opengauss.path=/opt/portal/tools/debezium/plugin/debezium-connector-opengauss/

confluent.pkg.url=https://packages.confluent.io/archive/5.5/confluent-community-5.5.1-2.12.zip
connector.mysql.pkg.url=https://opengauss.obs.cn-south-1.myhuaweicloud.com/latest/tools/replicate-mysql2openGauss-${tools.version}.tar.gz
connector.opengauss.pkg.url=https://opengauss.obs.cn-south-1.myhuaweicloud.com/latest/tools/replicate-openGauss2mysql-${tools.version}.tar.gz

debezium.pkg.path=/opt/portal/pkg/debezium/
confluent.pkg.name=confluent-community-5.5.1-2.12.zip
connector.mysql.pkg.name=replicate-mysql2openGauss-${tools.version}.tar.gz
connector.opengauss.pkg.name=replicate-openGauss2mysql-${tools.version}.tar.gz

datacheck.pkg.url=https://opengauss.obs.cn-south-1.myhuaweicloud.com/latest/tools/gs_datacheck-${tools.version}.tar.gz
datacheck.install.path=/opt/portal/tools/
datacheck.path=/opt/portal/tools/gs_datacheck-${tools.version}/
datacheck.pkg.path=/opt/portal/pkg/datacheck/
datacheck.pkg.name=gs_datacheck-${tools.version}.tar.gz
datacheck.extract.jar.name=datachecker-extract-${tools.version}.jar
datacheck.check.jar.name=datachecker-check-${tools.version}.jar                                                         
```
# 安装工具
```
sh gs_rep_portal.sh install_mysql_full_migration_tools_offline  1 &
sh gs_rep_portal.sh install_mysql_incremental_migration_tools_offline  1 &
sh gs_rep_portal.sh install_mysql_reverse_migration_tools_offline  1 &
sh gs_rep_portal.sh install_mysql_datacheck_tools_offline	 1 &
```
# 权限更改
```
chown -R omm:dbgrp /home/omm
```

