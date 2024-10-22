# 内置扩展包安装
## 检查已安装的扩展包
```
sudo -i -u postgres
/opt/postgresql/bin/psql
\dx
或
SELECT * FROM pg_available_extensions 
```

## 编译安装指定扩展包
进入源码编译
```
cd /opt/postgresql-17.0/contrib/uuid-ossp
make & make install
```

## 指定扩展包路径
vi /opt/postgresql/data/postgresql.conf
```
dynamic_library_path = '/opt/postgresql/share/extension'
```

## 重启加载扩展包
```
重启
sh /opt/postgresql/bin/restart-postgres.sh

切换用户
sudo -i -u postgres
连接postgres
/opt/postgresql/bin/psql
创建扩展方法
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
```

## orafce 兼容扩展包安装
## pgstattuple  扩展包安装
