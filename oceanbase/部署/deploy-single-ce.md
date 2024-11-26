# 社区版部署

## 机器配置
内存：10G     
cpu： 4 core     
磁盘：50GB    /        
系统：CentOS Linux release 7.9.2009 (Core)

## 环境准备
- 修改ulimits 配置
```
echo -e "* soft nofile 20000\n* hard nofile 20000" >> /etc/security/limits.d/nofile.conf
echo -e "* soft nproc 120000\n* hard nproc 120000" >> /etc/security/limits.d/nproc.conf

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
![1732587580343](https://github.com/user-attachments/assets/b48bb761-ee69-4002-b1a3-872c8ba871ba)
![1732587615494](https://github.com/user-attachments/assets/903d1c69-dea7-4bab-a6c0-cc214b7fc237)
![1732587643386](https://github.com/user-attachments/assets/e6a7e44b-f5f7-436f-9ce6-b59a2673c13c)
![1732587666056](https://github.com/user-attachments/assets/c4c02043-173f-47da-a285-71c8aaf042b1)
