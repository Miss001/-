## 1 下载安装包
地址：https://yum.postgresql.org/rpmchart/ <br />
![image](https://github.com/Miss001/postgresql/assets/100106330/2a0dba5f-affd-40c1-9509-135e3770df1b) <br />
需要下载的安装包 <br />
postgresql12-12.12-1PGDG.rhel7.x86_64.rpm <br />
postgresql12-contrib-12.12-1PGDG.rhel7.x86_64.rpm <br />
postgresql12-libs-12.12-1PGDG.rhel7.x86_64.rpm <br />
postgresql12-server-12.12-1PGDG.rhel7.x86_64.rpm <br />
postgresql12-devel-12.12-1PGDG.rhel7.x86_64.rpm <br />

## 2 下载依赖包
```
yum install --downloadonly --downloaddir=/home/''pg12'' postgis3_12
```
## 3 安装
安装依赖
```
yum install -y perl-ExtUtils-Embed readline-devel zlib-devel pam-devel libxml2-devel libxslt-devel openldap-devel python-devel gcc-c++ openssl-devel cmake
```
安装postgresql
```
rpm -ivh postgresql12*.rpm
```
创建数据目录
```
mkdir /home/pg12data

chown -R postgres:postgres /home/pg12data/
```
配置
<code>systemctl edit postgresql-12.service</code>
```
[Service]
Environment=PGDATA=/home/pg12data
```
该命令会创建一个文件：/etc/systemd/system/postgresql-12.service.d/override.conf

重新加载配置
```
systemctl daemon-reload
```
初始化
```
su postgres -c“/usr/pgsql-12/bin/postgresql-12-setup initdb”
```
配置开机自启动
```
systemctl enable postgresql-12
```
启动
```
systemctl start postgresql-12
```
## 4 配置文件修改
首次密码登录需要修改pg_hba.conf,把里面的ident全部换成md5 <br />
<code>vi pg_hba.conf</code>
```
# TYPE  DATABASE  USER  CIDR-ADDRESS  METHOD

# "local" is for Unix domain socket connections only
local    all   all                 ident
# IPv4 local connections:
host     all      all   127.0.0.1/32  md5
# IPv6 local connections:
host     all      all   ::1/128       md5
```
允许全网段访问 <br />
<code>vi pg_hba.conf</code>
```
#增加下面一行
# TYPE TABASE   USER   ADDRESS                 METHOD
host    all             all             0.0.0.0/0          md5
```

监听所有ip,修改listen_address <br />
<code>vi postgresql.conf</code>
```
listen_address = '*'
```

重启
```
service postgersql restart
```

## 5 创建用户
切换到postgres超级管理员
```
su postgres
```
创建一个初始数据库
```
createdb tempdb
```
创建超级用户pgdbo
```
createuser -s -P pgdbo
```
使用普通用户登录
```
psql -U pgdbo -h localhost tempdb
```
登录后修改密码
```
alter user postgres with password '123456'
```
