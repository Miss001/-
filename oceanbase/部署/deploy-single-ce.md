# 社区版部署

## 机器配置
内存：10G     
cpu： 4 core     
磁盘：70GB    /        
系统：CentOS Linux release 7.9.2009 (Core)

## 环境准备
- 修改ulimits 配置
```
echo -e "* soft nofile 20000\n* hard nofile 20000" >> /etc/security/limits.d/nofile.conf
echo -e "* soft nproc 120000\n* hard nproc 120000" >> /etc/security/limits.d/nproc.conf
- 安装java
```
sudo yum install -y java-1.8.0-openjdk java-1.8.0-openjdk-devel
```
```
## 安装OBD

### 下载安装包    
地址：[https://www.oceanbase.com/softwarecenter-enterprise](https://www.oceanbase.com/softwarecenter)     
![1732585906433](https://github.com/user-attachments/assets/4c3393eb-4622-4b1d-84df-047db85d4122)


### 解压安装
```
tar -xzf oceanbase-all-in-one-4.3.4_20241108.el7.x86_64.tar.gz
cd oceanbase-all-in-one/bin/
./install.sh
source ~/.oceanbase-all-in-one/bin/env.sh

#启动obd
nohup obd web  -p 8680 &

#输出详情
--------------------------------
start OBD WEB in 0.0.0.0:8680
please open http://127.0.0.1:8680

# 访问 http://192.168.131.110:8680
# 添加集群部署
```
![1732591451378](https://github.com/user-attachments/assets/aec74ceb-2adb-45d3-aa98-28416ec09f22)
![1732591485056](https://github.com/user-attachments/assets/f9ba639c-ff25-4cb0-938f-09dc57463e14)
![1732591515865](https://github.com/user-attachments/assets/6850836e-c36a-405d-86f5-38c873d8515a)
![1732591592650](https://github.com/user-attachments/assets/149c7dff-a7e9-4e3e-bd2d-382ef2e3b015)
![image](https://github.com/user-attachments/assets/5258d23d-7723-4a1a-be39-df3c54e3291f)
![1732591652471](https://github.com/user-attachments/assets/43d1db1c-acd8-4dee-8a09-36e0cce0190f)
