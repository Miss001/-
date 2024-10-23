# 内置扩展包安装
### 检查已安装的扩展包
```
sudo -i -u postgres
/opt/postgresql/bin/psql
\dx
或
SELECT * FROM pg_available_extensions 
```

### 编译安装指定扩展包
进入源码编译
```
cd /opt/postgresql-17.0/contrib/uuid-ossp
make & make install
```

### 指定扩展包路径
vi /opt/postgresql/data/postgresql.conf
```
dynamic_library_path = '/opt/postgresql/share/extension'
```

### 重启加载扩展包
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

# postgis 扩展包安装
实现地理空间功能的使用
- 依赖安装
```
sudo yum install libxml2-devel libjpeg-devel libpng-devel libtiff libtiff-devel zlib-devel gcc g++ -y
export LD_LIBRARY_PATH="/usr/local/lib:$LD_LIBRARY_PATH"
```
- 1.cmake
```
wget https://cmake.org/files/v3.15/cmake-3.15.0.tar.gz
tar -zxvf cmake-3.15.0.tar.gz
cd cmake-3.15.0
./bootstrap
make
sudo make install
```

- 2.sqlite
```
wget https://www.sqlite.org/2024/sqlite-autoconf-3470000.tar.gz
tar -zxvf sqlite-autoconf-3470000.tar.gz
cd sqlite-autoconf-3410000
./configure --prefix=/usr/local
make
make install
echo "/usr/local/lib" | sudo tee -a /etc/ld.so.conf.d/sqlite3.conf
sudo ldconfig
```

- 3.proj
```
wget https://download.osgeo.org/proj/proj-8.2.0.tar.gz
tar -zxvf proj-8.2.0.tar.gz
cd proj-8.2.0
./configure --prefix=/usr/local
make 
make install
export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig:$PKG_CONFIG_PATH
```

- 4.geos
```
wget https://github.com/libgeos/geos/releases/download/3.13.0/geos-3.13.0.tar.bz2
tar -xvjf geos-3.13.0.tar.bz2
cd geos-3.13.0
./configure --prefix=/usr/local  
make 
make install libxml2
```

- 4.python3
```
sh Miniconda3-latest-Linux-x86_64.sh
export CONDA_HOME=/opt/miniconda3
export PATH=${CONDA_HOME}/bin:$PATH
```

- 5.gdal
```
wget https://download.osgeo.org/gdal/3.4.0/gdal-3.4.0.tar.gz
tar -xvzf gdal-3.4.0.tar.gz
cd gdal-3.4.0
./configure --prefix=/usr/local  --with-python=python3 --with-sqlite3=/usr/local
make 
make install
```

- 5.protobuf 
```
wget https://github.com/protocolbuffers/protobuf/releases/download/v28.3/protobuf-28.3.tar.gz
tar -xzf protobuf-28.3.tar.gz
cd protobuf-28.3
mkdir build
cd build
cmake ..              
make          
sudo make install
sudo ldconfig
```

- 6.protobuf-c
```
wget https://github.com/protobuf-c/protobuf-c/releases/download/v1.5.0/protobuf-c-1.5.0.tar.gz
tar -xzf protobuf-c-1.5.0.tar.gz
cd protobuf-c-1.5.0
./configure  --prefix=/usr/local
make 
make install
```

- postgis
```
export PATH="/usr/bin:$PATH"

wget https://download.osgeo.org/postgis/source/postgis-3.5.0.tar.gz
tar -xvzf postgis-3.5.0.tar.gz
cd postgis-3.5.0
./configure --with-pgconfig=/opt/postgresql/bin/pg_config --without-protobuf
make 
make install
```
- 

