# 单节点企业版部署
## 要求
python>=3.6 <=3.10 , --enable-shared编译


## 下载
地址：https://opengauss.org/zh/download/  
![image](https://github.com/user-attachments/assets/3737459a-5d2c-42ea-880a-79a6b5870eae)

## 环境配置
```
设置主机名
hostnamectl set-hostname master

```
## 安装python环境
```
安装
sh Miniconda3-latest-Linux-x86_64.sh

添加环境变量
export CONDA_HOME=/opt/miniconda3
export PATH=${CONDA_HOME}/bin:$PATH

编译并创建python环境
tar -xf Python-3.9.20.tar.xz 
cd Python-3.9.20
sudo yum groupinstall "Development Tools"
sudo yum install openssl-devel bzip2-devel readline-devel sqlite-devel libffi-devel zlib-devel
./configure --enable-shared --prefix=/opt/miniconda3/envs/myenv
make 
make altinstall

ln -s /opt/miniconda3/envs/myenv/bin/python3.9 /opt/miniconda3/envs/myenv/bin/python
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH

conda create --name myenv
source ~/.bashrc
conda activate myenv
```

## 安装依赖
```
yum install python3-devel libltdl*
```

## 安装
```
解压
mkdir /opt/openGausssource/
tar -zxvf openGauss-All-6.0.0-CentOS7-x86_64.tar.gz
tar -zxvf openGauss-OM-6.0.0-CentOS7-x86_64.tar.gz

chmod 755 -R openGausssource

创建数据目录
mkdir -p /opt/openGauss/install/data/dn
mkdir -p /opt/openGauss/install/app
mkdir -p /opt/openGauss/log/omm
mkdir -p /opt/openGauss/tmp
mkdir -p /opt/openGauss/install/om
mkdir -p /opt/openGauss/corefile

```
## 创建用户组
```
groupadd dbgrp
useradd -g dbgrp omm
```

## 创建cluster_config.xml 文件
```
<?xml version="1.0" encoding="UTF-8"?>
<ROOT>
    <!-- openGauss整体信息 -->
    <CLUSTER>
        <!-- 数据库名称 -->
        <PARAM name="clusterName" value="dbCluster" />
        <!-- 数据库节点名称(hostname) -->
        <PARAM name="nodeNames" value="master" />
        <!-- 数据库安装目录-->
        <PARAM name="gaussdbAppPath" value="/opt/openGauss/install/app" />
        <!-- 日志目录-->
        <PARAM name="gaussdbLogPath" value="/opt/openGauss/log/omm" />
        <!-- 临时文件目录-->
        <PARAM name="tmpMppdbPath" value="/opt/openGauss/tmp" />
        <!-- 数据库工具目录-->
        <PARAM name="gaussdbToolPath" value="/opt/openGauss/install/om" />
        <!-- 数据库core文件目录-->
        <PARAM name="corePath" value="/opt/openGauss/corefile" />
        <!-- 节点IP，与数据库节点名称列表一一对应 -->
        <PARAM name="backIp1s" value="192.168.131.128"/> 
    </CLUSTER>
    <!-- 每台服务器上的节点部署信息 -->
    <DEVICELIST>
        <!-- 节点1上的部署信息 -->
        <DEVICE sn="node1_master">
            <!-- 节点1的主机名称 -->
            <PARAM name="name" value="master"/>
            <!-- 节点1所在的AZ及AZ优先级 -->
            <PARAM name="azName" value="AZ1"/>
            <PARAM name="azPriority" value="1"/>
            <!-- 节点1的IP，如果服务器只有一个网卡可用，将backIP1和sshIP1配置成同一个IP -->
            <!-- 用ipv6安装部署时 换上ipv6地址即可，后面xml文件示例也是同样操作 -->
            <PARAM name="backIp1" value="192.168.131.128"/>
            <PARAM name="sshIp1" value="192.168.131.128"/>
               
	    <!--dbnode-->
	    <PARAM name="dataNum" value="1"/>
	    <PARAM name="dataPortBase" value="15400"/>
	    <PARAM name="dataNode1" value="/opt/openGauss/install/data/dn"/>
            <PARAM name="dataNode1_syncNum" value="0"/>
        </DEVICE>
    </DEVICELIST>
</ROOT>

```
## 校验安装环境
```
./gs_preinstall -U omm -G dbgrp -L -X /opt/openGausssource/cluster_config.xml
```

## 部署安装
```
切换用户配置python环境
su - omm
conda init
source ~/.bashrc
conda activate myenv

安装
gs_install -X /opt/openGausssource/cluster_config.xml
密码
opengauss@123
```
## 验证
```
gs_om -t status
```

## 连接
```
gsql -d postgres -p 15400

create user root identified by 'root@123';
alter user root sysadmin;
```
## 退出连接
```
\q
```
## 配置 navicate 远程连接
vi postgresql.conf
```
listen_addresses = '*'
password_encryption_type = 0
```
vi pg_hba.conf
```
# Allow replication connections from localhost, by a user with the
host    all             all             0.0.0.0/0               md5
```
重启服务
```
gs_ctl restarted
```
密码需要重新更新才会生效
```
alter user root with password 'root@@123';
```
