# 安装依赖
root用户安装
- 安装基础依赖
```
yum install -y perl-ExtUtils-CBuilder perl-ExtUtils-MakeMaker perl-Time-HiRes perl-devel perl-DBI perl-Compress-Zlib perl-open 
yum install -y perl-YAML
yum install -y perl-CPAN
perl -MCPAN -e 'install JSON'
perl -MCPAN -e 'install DBI'
```
- 安装依赖： DBD::Pg
```
-------配置-----
ln -s /opt/openGauss/install/app/include/postgresql/server/libpq/libpq-fe.h /opt/openGauss/install/app/include/postgresql/server/libpq-fe.h

#配置环境变量
vi /root/.bash_profile
export LD_LIBRARY_PATH=/opt/openGauss/install/app/lib/:$LD_LIBRARY_PATH
#使变量生效
source /root/.bash_profile

-------安装依赖-----
perl -MCPAN -e 'install DBD::Pg'
#pg_config: /opt/openGauss/install/app/bin/
#verions: 9
#verions: 2
#verions: 4
#bin path: /opt/openGauss/install/app/bin/
#include path: /opt/openGauss/install/app/include/postgresql/server/
#library path: /opt/openGauss/install/app/lib/
```
- 安装依赖：DBD::Oracle
```
-------安装oracle客户端-----
#rpm包下载地址
https://yum.oracle.com/repo/OracleLinux/OL7/oracle/instantclient21/x86_64/
#rpm安装
rpm -ivh oracle-instantclient-basic-21.1.0.0.0-1.x86_64.rpm 
rpm -ivh oracle-instantclient-devel-21.1.0.0.0-1.x86_64.rpm 
rpm -ivh oracle-instantclient-jdbc-21.1.0.0.0-1.x86_64.rpm 
rpm -ivh oracle-instantclient-sqlplus-21.1.0.0.0-1.x86_64.rpm

#配置环境变量
vi /root/.bash_profile
export ORACLE_HOME=/usr/lib/oracle/21/client64
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:$LD_LIBRARY_PATH
export PATH=$ORACLE_HOME/bin:$PATH
#使变量生效
source /root/.bash_profile

-------安装依赖-----
perl -MCPAN -e 'install Test::NoWarnings'
perl -MCPAN -e 'install DBD::Oracle'
```

# 安装包下载编译
```
#下载解压
wget https://gitee.com/opengauss/openGauss-tools-ora2og/repository/archive/master.zip
unzip master.zip
cd openGauss-tools-ora2og-master
#编译安装
perl Makefile.PL
make && make install
```

# 设置环境变量
```
vi /root/.bash_profile
export PERL5LIB=/root/perl5/lib/perl5
export PATH=$PATH:/root/perl5/bin/ora2pg/bin
#使变量生效
source /root/.bash_profile
```
# 测试使用
```
ora2pg --help
```
