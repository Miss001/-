# 安装gs_rep_portal
**安装java**
```
yum search java|grep jdk
yum install -y java-11-openjdk*
java -version
```
**portal**
```
wget https://opengauss.obs.cn-south-1.myhuaweicloud.com/6.0.0/tools/centos7/PortalControl-6.0.0-x86_64.tar.gz
tar -zxvf PortalControl-6.0.0-x86_64.tar.gz
cd portal
# 安装工具
sh gs_rep_portal.sh install_mysql_full_migration_tools_online 1 &
sh gs_rep_portal.sh install_mysql_incremental_migration_tools_online 1 &
-- sh gs_rep_portal.sh install_mysql_reverse_migration_tools_online	 1 &
-- sh gs_rep_portal.sh install_mysql_datacheck_tools_online	 1 &
```

