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

## 指定扩展包路径
vi postgresql.conf
```
dynamic_library_path = '/opt/postgresql/share/extension'
```

## 重启加载扩展包
```

```
