https://developer.aliyun.com/article/1025085
## 安装 Docker 20.x
安装依赖
```
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
安装docker
```
sudo yum install docker-ce-20.10.0 docker-ce-cli-20.10.0 containerd.io
```
启动dockers
```
sudo systemctl start docker
sudo systemctl enable docker
```
## 安装oracle
拉取镜像
```
docker pull registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g
```
创建数据目录
```
mkdir -p /opt/oracle
```
创建容器
```
docker run -it -d \
-p 1521:1521 \
--name oracle \
-v /opt/oracle:/data/oracle \
registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g
```
进入容器
```
docker exec -it oracle bash
```
切换root用户,密码为 helowin 
```
su
```
添加环境变量
```
export ORACLE_HOME=/home/oracle/app/oracle/product/11.2.0/dbhome_2
export ORACLE_SID=helowin
export PATH=$ORACLEHOME/bin:PATH

source /etc/profile 

```
添加软连接
```
ln -s $ORACLE_HOME/bin/sqlplus /usr/bin

```
切换到oracle用户
```
su - oracle
```
登录
```
sqlplus /nolog 
conn /as sysdba
```
修改sys、system用户密码
```
alter user system identified by 123456 ;
alter user sys identified by 123456 ;
```
添加用户
```
create user root identified by 123456;
#授权
grant connect,resource,dba to root;
#设置密码永不过期
ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED;
#修改数据库最大连接数
alter system set processes=1000 scope=spfile;
