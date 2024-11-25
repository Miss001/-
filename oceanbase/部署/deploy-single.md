# 机器配置
内存：8G     
cpu: 2 core     
磁盘：80GB    /      
      30GB   /data/1     
      40GB   /data/log1     
系统：CentOS Linux release 7.9.2009 (Core)

# 环境准备
- 安装docker   
  参考 https://github.com/Miss001/docker/blob/main/%E9%83%A8%E7%BD%B2/deploy-centos7.9.md
  
# 安装OAT
### 下载安装包    
地址：https://www.oceanbase.com/softwarecenter-enterprise     
![1732265190460](https://github.com/user-attachments/assets/b7e744bd-ec26-4d98-a54f-8c88f52384ec)

### 解压安装
```
tar -xf oat-all-in-one-x86.tar
sh oat-all-in-one-x86/install.sh

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
-i 192.168.1.100 \
-p 3306 \
-P 2882 \
-z zone1 \
-d /home/admin/oceanbase/store/obdemo \
-l DEBUG \
-n obdemo \
-c 10001 \
-o "system_memory=16G,config_additional_dir=/data/1/obdemo/etc3;/data/log1/obdemo/etc2"
```

