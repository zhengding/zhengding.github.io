## 配置免密登录

ssh-copy-id <新节点的IP>

## 基础环境

1. 关闭selinux
```
sed -i '/SELINUX/s/enforcing/disabled/' /etc/selinux/config
setenforce 0
```
2. 关闭防火墙
```
systemctl stop firewalld.servic
systemctl disable firewalld.service
```
3. 将IP写入`/etc/mesos-slave/hostname`中：
```
vim /etc/mesos-slave/hostname

fitech01 xxx.xxx.xxx.xxx
fitech02 xxx.xxx.xxx.xxx
fitech03 xxx.xxx.xxx.xxx
fitech04 xxx.xxx.xxx.xxx
...
```
4. **在主节点上**配置主节点到新增节点的免密码登录
```
fitech01: ssh-copy-id agent4
```

## Docker

1. 安装必要的packages
```
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```
2. 配置阿里镜像源
```
sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```
3. 安装docker
```
yum install docker-ce
```
4. 更新daemon.json
```
vim /etc/docker/daemon.json
# 内容和其他节点的 /etc/docker/daemon.json 相同
```
5. 开启docker服务
```
systemctl start docker
```

## GlusterFS

1. 安装依赖
```
yum install glusterfs
yum install glusterfs-cli
yum install glusterfs-libs
yum install glusterfs-server
yum install glusterfs-fuse
yum install glusterfs-geo-replication
```
2. 挂载gluster卷
```
mkdir -p /data/replica # 这个文件夹存储内容会做副本
mkdir -p /data/mnt # 这个文件夹存储内容是分布式的
mount -t glusterfs fitech01:volume1 /data/replica # volume1 还是 volume2 挂到这个位置忘记了，去其他机器上df -h 看一看
mount -t glusterfs fitech01:volume2 /data/mnt
```

## Mesos

1. 添加mesos的yum源
```
rpm -Uvh http://repos.mesosphere.io/el/7/noarch/RPMS/mesosphere-el-repo-7-1.noarch.rpm
```
2. 安装Mesos1.5.0
```
yum install -y mesos
```
3. 配置Mesos
```
vim /etc/default/mesos-slave #内容和其他节点相同，其中有个 export MESOS_ip 注意填当前节点的 IP
```
7. 配置marathon调用mesos运行docker容器
```
echo 'docker,mesos' > /etc/mesos-slave/containerizers
```
8. 启动slave
```
systemctl enable mesos-slave && systemctl start mesos-slave
```

配置完毕后，去 fitech01:5050 上看 agent 页面是否增加了新增的节点