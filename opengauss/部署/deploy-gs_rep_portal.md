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
su - omm
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

chameleon.install.path=/home/omm/portal/tools/chameleon/
chameleon.venv.path=/home/omm/portal/tools/chameleon/chameleon-${tools.version}/
chameleon.path=/home/omm/.pg_chameleon/
chameleon.pkg.url=https://opengauss.obs.cn-south-1.myhuaweicloud.com/latest/tools/${system.name}/chameleon-${tools.version}-${system.arch}.tar.gz
chameleon.pkg.path=/home/omm/portal/pkg/chameleon/
chameleon.pkg.name=chameleon-${tools.version}-${system.arch}.tar.gz

debezium.path=/home/omm/portal/tools/debezium/
confluent.path=/home/omm/portal/tools/debezium/confluent-5.5.1/
confluent.install.path=/home/omm/portal/tools/debezium/
connector.path=/home/omm/portal/tools/debezium/plugin/
connector.mysql.path=/home/omm/portal/tools/debezium/plugin/debezium-connector-mysql/
connector.opengauss.path=/home/omm/portal/tools/debezium/plugin/debezium-connector-opengauss/

confluent.pkg.url=https://packages.confluent.io/archive/5.5/confluent-community-5.5.1-2.12.zip
connector.mysql.pkg.url=https://opengauss.obs.cn-south-1.myhuaweicloud.com/latest/tools/replicate-mysql2openGauss-${tools.version}.tar.gz
connector.opengauss.pkg.url=https://opengauss.obs.cn-south-1.myhuaweicloud.com/latest/tools/replicate-openGauss2mysql-${tools.version}.tar.gz

debezium.pkg.path=/home/omm/portal/pkg/debezium/
confluent.pkg.name=confluent-community-5.5.1-2.12.zip
connector.mysql.pkg.name=replicate-mysql2openGauss-${tools.version}.tar.gz
connector.opengauss.pkg.name=replicate-openGauss2mysql-${tools.version}.tar.gz

datacheck.pkg.url=https://opengauss.obs.cn-south-1.myhuaweicloud.com/latest/tools/gs_datacheck-${tools.version}.tar.gz
datacheck.install.path=/home/omm/portal/tools/
datacheck.path=/home/omm/portal/tools/gs_datacheck-${tools.version}/
datacheck.pkg.path=/home/omm/portal/pkg/datacheck/
datacheck.pkg.name=gs_datacheck-${tools.version}.tar.gz
datacheck.extract.jar.name=datachecker-extract-${tools.version}.jar
datacheck.check.jar.name=datachecker-check-${tools.version}.jar                                                        
```
**安装迁移工具**  
```
sh gs_rep_portal.sh install_mysql_full_migration_tools_offline  1 &
sh gs_rep_portal.sh install_mysql_incremental_migration_tools_offline  1 &
sh gs_rep_portal.sh install_mysql_reverse_migration_tools_offline  1 &
sh gs_rep_portal.sh install_mysql_datacheck_tools_offline	 1 &
```

