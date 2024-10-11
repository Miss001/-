# 单节点部署
## 下载源码包
地址：https://www.postgresql.org/ftp/source/v17.0/
![1728552063267](https://github.com/user-attachments/assets/9a7eee04-b180-40e8-a252-d278c33706c0)

## 安装依赖
```
sudo yum update -y
sudo yum groupinstall "Development Tools" -y
sudo yum install readline-devel zlib-devel openssl-devel libxml2-devel libxslt-devel -y
```
## 编译
```
sudo mkdir /opt/postgresql
tar -xvf postgresql-17.0.tar.gz
cd postgresql-17.0
./configure --prefix=/opt/postgresql --without-icu
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
创建日志目录
sudo mkdir -p /opt/postgresql/log
sudo chown postgres:postgres /opt/postgresql/log
切换用户
sudo -i -u postgres
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
vi pg_hba.conf
```
# Allow replication connections from localhost, by a user with the
host    all             all             0.0.0.0/0          trust
```
vi postgresql.conf
```
listen_address = '*'
```

## 设置快捷脚本
vi stop-postgres.sh
```
#! /bin/bash

su postgres -c"/opt/postgresql/bin/pg_ctl -D /opt/postgresql/data -l /opt/postgresql/log/postgresql.log stop"

```
vi start-postgres.sh
```
#! /bin/bash

su postgres -c"/opt/postgresql/bin/pg_ctl -D /opt/postgresql/data -l /opt/postgresql/log/postgresql.log start"

```
vi restart-postgres.sh
```
#! /bin/bash

su postgres -c"/opt/postgresql/bin/pg_ctl -D /opt/postgresql/data -l /opt/postgresql/log/postgresql.log restart"

```
