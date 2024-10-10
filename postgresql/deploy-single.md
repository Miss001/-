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
sudo mkdir -p /var/log/postgresql
sudo chown postgres:postgres /var/log/postgresql
/usr/local/pgsql/bin/pg_ctl -D /data/postgresql -l /var/log/postgresql/postgresql.log start
```
