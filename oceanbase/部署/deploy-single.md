# 机器配置
内存：8G   
cpu:2 core   
磁盘：60GB

# 环境准备
echo never > /sys/kernel/mm/redhat_transparent_hugepage/enabled

# 安装 Docker
安装依赖
```
sudo yum install -y yum-utils
sudo wget -O /etc/yum.repos.d/docker-ce.repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```
查看版本
```
yum list docker-ce --showduplicates | sort -r
```
安装docker
```
sudo yum -y install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
启动dockers
```
sudo systemctl start docker
sudo systemctl enable docker
```
