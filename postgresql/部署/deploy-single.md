# 单节点部署
## 下载源码包
地址：https://www.postgresql.org/ftp/source/v17.0/
![1728552063267](https://github.com/user-attachments/assets/9a7eee04-b180-40e8-a252-d278c33706c0)

## 安装依赖
```
sudo yum update -y
sudo yum groupinstall -y "Development Tools"
sudo yum install -y readline-devel zlib-devel libxml2-devel libxslt-devel uuid-devel gcc-c++ openssl-devel flex bison libcurl-devel libicu-devel
```

## 编译
```
sudo mkdir /opt/postgresql
tar -xvf postgresql-17.0.tar.gz
cd postgresql-17.0
./configure --prefix=/opt/postgresql --with-libxml --with-libxslt --with-uuid=ossp  #若无icu 可增加 --without-icu
make
sudo make install
```

## 创建用户组
```
sudo useradd postgres
sudo chown postgres:postgres /opt/postgresql
```

## 初始化数据库
```
sudo mkdir -p /opt/postgresql/data
sudo chown postgres:postgres /opt/postgresql/data
sudo -i -u postgres
/opt/postgresql/bin/initdb -D /opt/postgresql/data
```

## 启动数据库
```
切换用户
sudo -i -u postgres
创建日志目录
sudo mkdir -p /opt/postgresql/log
sudo chown postgres:postgres /opt/postgresql/log
启动
/opt/postgresql/bin/pg_ctl -D /opt/postgresql/data -l /opt/postgresql/log/postgresql.log start
```

## 修改密码
```
/opt/postgresql/bin/psql
alter user postgres password 'root@@123';
\q
```

## 配置文件修改（navicate远程连接）
vi /opt/postgresql/data/pg_hba.conf
```
# Allow replication connections from localhost, by a user with the
host    all             all             0.0.0.0/0          trust
```
vi /opt/postgresql/data/postgresql.conf
```
listen_address = '*'
```

## 设置快捷脚本
vi /opt/postgresql/bin/stop-postgres.sh
```
#! /bin/bash

su postgres -c"/opt/postgresql/bin/pg_ctl -D /opt/postgresql/data -l /opt/postgresql/log/postgresql.log stop"

```
vi /opt/postgresql/bin/start-postgres.sh
```
#! /bin/bash

su postgres -c"/opt/postgresql/bin/pg_ctl -D /opt/postgresql/data -l /opt/postgresql/log/postgresql.log start"

```
vi /opt/postgresql/bin/restart-postgres.sh
```
#! /bin/bash

su postgres -c"/opt/postgresql/bin/pg_ctl -D /opt/postgresql/data -l /opt/postgresql/log/postgresql.log restart"

```
授权可执行权限
```
chmod u+x stop-postgres.sh 
chmod u+x start-postgres.sh 
chmod u+x restart-postgres.sh
```
