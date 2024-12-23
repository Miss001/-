# oms安装
前提：安装docker
## 下载
链接：https://www.oceanbase.com/softwarecenter?_gl=1*6pns1g*_ga*MjIxODU1OTA3LjE3Mjg0MzkzNDg.*_ga_T35KTM57DZ*MTczNDQxMzY5OS41NC4xLjE3MzQ0MTQ1ODQuNjAuMC4w    

![1734414666090](https://github.com/user-attachments/assets/9b9dd37f-cf43-46ee-a8b6-e564ac2910fb)

## 加载镜像
```
docker load -i oms_4.2.7-ce.tar.gz

#创建目录 mkdir -p
/opt/oms/oms_logs
/opt/oms/oms_store
/opt/oms/oms_run
```

## oms配置文件
vi /opt/oms/config.yaml
```
# 您需要提前准备一个 OceanBase 数据库，用于存放 OMS 社区版的元信息
oms_meta_host: 192.168.131.80
oms_meta_port: 2881
oms_meta_user: root@test01
oms_meta_password: TEst**123

# OMS 社区版部署时会在元信息库中创建出这三个数据库
drc_rm_db: oms_rm
drc_cm_db: oms_cm
drc_cm_heartbeat_db: oms_cm_heartbeat

# OMS 社区版集群配置
cm_url: http://192.168.131.80:18088
cm_location: 100
cm_is_default: true
cm_nodes:
 - 192.168.131.80

# 端口配置
nginx_server_port: 18089
cm_server_port: 18088
supervisor_server_port: 19000
ssh_server_port: 2023
ghana_server_port: 18090

```
## 部署
```
docker run -dit -m 8G --net host\
-v /opt/oms/config.yaml:/home/admin/conf/config.yaml \
-v /opt/oms/oms_logs:/home/admin/logs \
-v /opt/oms/oms_store:/home/ds/store \
-v /opt/oms/oms_run:/home/ds/run \
-e OMS_HOST_IP=192.168.131.80 \
--privileged=true \
--pids-limit -1 \
--ulimit nproc=65535:65535 \
--name oms \
reg.docker.alibaba-inc.com/oceanbase/oms:feature_4.2.7_ce   
```
## 初始化oms
```
docker exec -it oms bash
bash /root/docker_init.sh
```
