# 安装uuid-ssop：不兼容
```
# 编译插件
yum -y install uuid-devel

wget https://ftp.postgresql.org/pub/source/v9.2.4/postgresql-9.2.4.tar.gz
./configure --prefix=/opt/postgresql  --with-ossp-uuid

ln -s /opt/postgresql-9.2.4/src/backend/utils/errcodes.h /opt/postgresql-9.2.4/src/include/utils/errcodes.h
ln -s /opt/postgresql-9.2.4/src/backend/utils/fmgroids.h /opt/postgresql-9.2.4/src/include/utils/fmgroids.h
cd contrib/uuid-ossp/
make
make install

#将插件拷贝到opengauss目录下

#创建插件
CREATE EXTENSION uuid-ossp 
```
