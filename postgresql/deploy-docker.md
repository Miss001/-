## 拉取镜像
docker pull postgres:16

## 创建容器
```
docker run --name postgres-container \
-e POSTGRES_USER=pguser \
-e POSTGRES_PASSWORD=123456 \
-e POSTGRES_DB=default \
-p 5432:5432 \
-v pgdata:/mnt/postgres/data \
-d postgres
```
## 连接
```
docker exec -it postgres-container psql -U myuser -d mydb
```
