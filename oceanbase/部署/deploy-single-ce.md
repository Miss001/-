# 社区版部署

## 机器配置
内存：10G     
cpu： 4 core     
磁盘：30GB    /        
系统：CentOS Linux release 7.9.2009 (Core)

## 环境准备
- 安装docker   
  参考 https://github.com/Miss001/docker/blob/main/%E9%83%A8%E7%BD%B2/deploy-centos7.9.md
  
## 安装OAT

### 下载安装包    
地址：[https://www.oceanbase.com/softwarecenter-enterprise](https://www.oceanbase.com/softwarecenter)     
![1732585906433](https://github.com/user-attachments/assets/4c3393eb-4622-4b1d-84df-047db85d4122)


### 解压安装
```
tar -xzf oceanbase-all-in-one-*.tar.gz
cd oceanbase-all-in-one/bin/
./install.sh
source ~/.oceanbase-all-in-one/bin/env.sh


#输出详情
--------------------------------
OAT is ready for visit
url is: http://<current_ip>:7000
user/password is: admin/aaAA11__

# 访问 http://192.168.131.30:7000 修改密码 Test@123
# 添加服务器初始化
```

## 安装oceanbase
### 下载安装包
地址：https://www.oceanbase.com/softwarecenter-enterprise      
![1732522441985](https://github.com/user-attachments/assets/e26d3572-692d-4204-8d84-6f6df41e30bd)

### 安装
```
rpm -ivh oceanbase-3.2.4.1-101000052023010822.el7.x86_64.rpm

# 默认安装路径 /home/admin/oceanbase
```

### 初始化
```
# 数据目录准备
su - admin
mkdir -p /data/1/obdemo/{etc3,sstable,slog}
mkdir -p /data/log1/obdemo/{clog,etc2}
mkdir -p /home/admin/oceanbase/store/obdemo
for t in {etc3,sstable,slog};do ln -s /data/1/obdemo/$t /home/admin/oceanbase/store/obdemo/$t; done
for t in {clog,etc2};do ln -s /data/log1/obdemo/$t /home/admin/oceanbase/store/obdemo/$t; done

#启动服务
su - admin
cd /home/admin/oceanbase 
/home/admin/oceanbase/bin/observer \
-i ens160 \
-p 2881 \
-P 2882 \
-z zone1 \
-d /home/admin/oceanbase/store/obdemo \
-l DEBUG \
-c 10001 \
-n obdemo \
-r '192.168.131.100:2882:2881' \
-o "system_memory=16G,datafile_size=100G,config_additional_dir=/data/1/obdemo/etc3;/data/log1/obdemo/etc2"
```

## 安装客户端
地址：https://www.oceanbase.com/softwarecenter?_gl=1*18ufyy8*_ga*MjIxODU1OTA3LjE3Mjg0MzkzNDg.*_ga_T35KTM57DZ*MTczMjUxNjY5OS4xNS4xLjE3MzI1MjQ4OTcuMTMuMC4w        
![1732525520393](https://github.com/user-attachments/assets/972d019f-77f2-4c6e-bbf6-c3cb296949ff)

### 安装
```
rpm -ivh libobclient-2.2.6-3.el7.x86_64.rpm
rpm -ivh obclient-2.2.6-1.el7.x86_64.rpm
```

