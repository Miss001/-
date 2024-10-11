## 检查已安装的扩展包
```
/opt/postgresql/bin/psql
\dx
```

## 编译安装指定扩展包
进入源码编译
```
cd /opt/postgresql-17.0/contrib
make
sudo make install DESTDIR=/opt/postgresql/
```
## 编译指定扩展包
```
安装依赖
sudo yum install libuuid-devel openssl-devel

```

## 指定扩展包路径
vi postgresql.conf
```
dynamic_library_path = '/opt/postgresql/share/extension'
```

## 重启加载扩展包
```
重启
sh /opt/postgresql/bin/restart-postgre.sh
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

```
