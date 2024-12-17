# oms安装
前提：安装docker
## 下载
链接：https://www.oceanbase.com/softwarecenter?_gl=1*6pns1g*_ga*MjIxODU1OTA3LjE3Mjg0MzkzNDg.*_ga_T35KTM57DZ*MTczNDQxMzY5OS41NC4xLjE3MzQ0MTQ1ODQuNjAuMC4w    

![1734414666090](https://github.com/user-attachments/assets/9b9dd37f-cf43-46ee-a8b6-e564ac2910fb)

## 加载镜像
```
docker load -i oms_4.2.7-ce.tar.gz

#创建目录 mkdir -p
/data/oms/oms_logs
/data/oms/oms_store
/data/oms/oms_run
```

## oms配置文件
```
# 您需要提前准备一个 OceanBase 数据库，用于存放 OMS 社区版的元信息
oms_meta_host: xxx.xxx.xxx.1
oms_meta_port: 2883
oms_meta_user: root@oms****
oms_meta_password: oms

# OMS 社区版部署时会在元信息库中创建出这三个数据库
drc_rm_db: oms_rm
drc_cm_db: oms_cm
drc_cm_heartbeat_db: oms_cm_heartbeat

# OMS 社区版集群配置
cm_url: http://xxx.xxx.xxx.2:8088
cm_location: 100
cm_is_default: true
cm_nodes:
 - xxx.xxx.xxx.2
tsdb_service: 'INFLUXDB'
tsdb_enabled: true
tsdb_url: 'xxx.xxx.xxx.4:8086'
tsdb_username: username
tsdb_password: 123****

```
## 部署
```
docker run -dit --net host \
-v /data/config.yaml:/home/admin/conf/config.yaml \
-v /data/oms/oms_logs:/home/admin/logs \
-v /data/oms/oms_store:/home/ds/store \
-v /data/oms/oms_run:/home/ds/run \
-e OMS_HOST_IP=${OMS_HOST_IP} \
--privileged=true \
--pids-limit -1 \
--ulimit nproc=65535:65535 \
--name ${CONTAINER_NAME} \
work.oceanbase-dev.com/obartifact-store/oms:${IMAGE_TAG}   
```
