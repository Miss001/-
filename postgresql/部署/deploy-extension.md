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

# orafce 兼容扩展包安装
实现oracle函数的使用

# pgstattuple  扩展包安装
实现查询表膨胀的功能

# postgis 扩展包安装
实现地理空间功能的使用
- 依赖安装
```
# sudo yum install proj-devel geos-devel gdal-devel -y
# 手动安装
sudo yum install libxml2-devel libtiff libtiff-devel -y

# 编译安装依赖
1.sqlite
wget https://www.sqlite.org/2024/sqlite-autoconf-3470000.tar.gz
tar -zxvf sqlite-autoconf-3470000.tar.gz
cd sqlite-autoconf-3410000
./configure --prefix=/usr/local
make & make install
echo "/usr/local/lib" | sudo tee -a /etc/ld.so.conf.d/sqlite3.conf
sudo ldconfig
export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig

2.proj
wget https://download.osgeo.org/proj/proj-8.2.0.tar.gz
tar -zxvf proj-8.2.0.tar.gz
cd proj-8.2.0
./configure --prefix=/usr/local
make & make install
export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig:$PKG_CONFIG_PATH

3.geos
wget https://download.osgeo.org/geos/geos-3.9.1.tar.bz2
tar -xvjf geos-3.9.1.tar.bz2
cd geos-3.9.1
./configure --prefix=/usr/local  
make & make install

4.gdal
wget https://download.osgeo.org/gdal/3.4.0/gdal-3.4.0.tar.gz
tar -xvzf gdal-3.4.0.tar.gz
cd gdal-3.4.0
./configure --prefix=/usr/local
make & make install
```

- 源码编译
```
wget https://download.osgeo.org/postgis/source/postgis-3.3.0.tar.gz
tar -xvzf postgis-3.3.0.tar.gz
cd postgis-3.3.0
./configure --with-pgconfig=/usr/pgsql-17/bin/pg_config --with-geosconfig=/usr/bin/geos-config --with-projdir=/usr --with-gdalconfig=/usr/bin/gdal-config
make & make install
```
- 

