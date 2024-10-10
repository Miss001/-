# 下载
地址：https://opengauss.org/zh/download/  
![image](https://github.com/user-attachments/assets/3737459a-5d2c-42ea-880a-79a6b5870eae)

# 环境配置
```
设置主机名
hostnamectl set-hostname master

```
# 安装python环境
```
安装
sh Miniconda3-latest-Linux-x86_64.sh

添加环境变量
export CONDA_HOME=/opt/miniconda3
export PATH=${CONDA_HOME}/bin:$PATH

创建python环境
conda create --name myenv python=3.9
source ~/.bashrc
conda activate myenv
```

# 安装依赖
```
yum install -y bzip2 libaio-devel flex bison ncurses-devel glibc-devel patch redhat-lsb-core readline-devel zlib-devel lz4-devel openSSL-devel libffi-devel kernel-devel gcc gcc-c++ python3 python3-devel

```

# 安装
```
mkdir /opt/software/openGauss/
tar -zxvf openGauss-All-6.0.0-CentOS7-x86_64.tar.gz
tar -zxvf openGauss-OM-6.0.0-CentOS7-x86_64.tar.gz
```

# 创建cluster_config.xml 文件
单节点配置
```
<?xml version="1.0" encoding="UTF-8"?>
<ROOT>
    <!-- openGauss整体信息 -->
    <CLUSTER>
        <!-- 数据库名称 -->
        <PARAM name="clusterName" value="dbCluster" />
        <!-- 数据库节点名称(hostname) -->
        <PARAM name="nodeNames" value="node1_hostname" />
        <!-- 数据库安装目录-->
        <PARAM name="gaussdbAppPath" value="/mnt/opengauss/install/app" />
        <!-- 日志目录-->
        <PARAM name="gaussdbLogPath" value="/mnt/opengauss/log/omm" />
        <!-- 临时文件目录-->
        <PARAM name="tmpMppdbPath" value="/mnt/opengauss/tmp" />
        <!-- 数据库工具目录-->
        <PARAM name="gaussdbToolPath" value="/mnt/opengauss/install/om" />
        <!-- 数据库core文件目录-->
        <PARAM name="corePath" value="/mnt/opengauss/corefile" />
        <!-- 节点IP，与数据库节点名称列表一一对应 -->
        <!-- 如果用ipv6 替换ipv4地址即可 如：<PARAM name="backIp1s" value="2407:xxxx:xxxx:xxxx:xxxx:xxxx:caa:2335"/> -->
        <PARAM name="backIp1s" value="192.168.131.128"/> 
    </CLUSTER>
    <!-- 每台服务器上的节点部署信息 -->
    <DEVICELIST>
        <!-- 节点1上的部署信息 -->
        <DEVICE sn="node1_hostname">
            <!-- 节点1的主机名称 -->
            <PARAM name="name" value="node1_hostname"/>
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
	    <PARAM name="dataNode1" value="/mnt/opengauss/install/data/dn"/>
            <PARAM name="dataNode1_syncNum" value="0"/>
        </DEVICE>
    </DEVICELIST>
</ROOT>

```
# 部署安装环境
```
./gs_preinstall -U omm -G dbgrp -L -X /opt/software/openGauss/cluster_config.xml

```

# 部署安装
```

```

## 报错
1.Exception: [GAUSS-52200] : Unable to import module: /opt/software/openGauss/script/domain_utils/sql_handler/../../../lib/cryptography/hazmat/bindings/_openssl.abi3.so: symbol SSLv3_method, version OPENSSL_1_1_0 not defined in file libssl.so.1.1 with link time reference  
解决 :下载指定版本的包替换  
```
wget https://www.openssl.org/source/openssl-1.1.l.tar.gz
tar -xzf openssl-1.1.l.tar.gz
cd openssl-1.1.l
./config
make
sudo make install

cp openssl-1.1.1/libssl.so.1.1  /usr/lib64/libssl.so.1.1
cp openssl-1.1.1/libcrypto.so.1.1  /usr/lib64/libcrypto.so.1.1
```
