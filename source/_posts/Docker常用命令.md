---
title: Docker常用命令
data:  2017-12-12 14:07:01
tag:
- Docker
categories: Docker
---

1、查看所有容器

```shell
docker ps -a
```

2、停止容器  

```shell
docker stop $(docker ps -a | grep "Exited" | awk '{print $1 }')
```

3、删除容器

```shell
docker rm $(docker ps -a | grep "Exited" | awk '{print $1 }') 
```

或

```shell
docker rm -v $(docker ps -aq -f status=exited)
```

4、删除镜像

```shell
docker rmi $(docker images | grep "none" | awk '{print $3}')
```

5、进入容器

```shell
docker exec -it fc873 /bin/bash
```

6、进入容器之后进行编辑ssh文件，如果没有ssh,需要先安装：
安装ssh-client命令：

```shell
sudo apt-get install openssh-client  
```

安装ssh-server命令：

```Shell
sudo apt-get install openssh-server
```

安装完成以后，先启动服务：

```shell
sudo /etc/init.d/ssh start  
```

启动后，可以通过“ps -e|grep ssh”查看是否正确启动。

7、编辑ssh配置文件：

```shell
$sudo vim/etc/ssh/sshd_config
```

PermitRootLogin without-password 改为 PermitRootLogin yes`
`PasswordAuthentication yes 改为 PasswordAuthentication no

8、重启服务：

```shell
$ sudo service ssh restart
```

9、设置ssh密码：

```shell
$ passwd root
Enter new UNIX password:   
Retype new UNIX password:   
passwd: password updated successfully
```

10、如何在Docker容器内外互相拷贝数据？

​	从容器内拷贝文件到主机上：

```shell
docker cp <containerId>:/file/path/within/container /host/path/target
```

​	从主机上拷贝文件到容器内:

### 1.用-v挂载主机数据卷到容器内

```bash
docker run -v /path/to/hostdir:/mnt $container  

在容器内拷贝  

cp /mnt/sourcefile  /path/to/destfile 
```

### 2.直接在主机上拷贝到容器物理存储系统

A. 获取容器名称或者id :

```Bash
$ docker ps 
```

B. 获取整个容器的id

```bash
$ docker inspect -f   '{{.Id}}'  步骤A获取的名称或者id  
```

C. 在主机上拷贝文件:

```Bash
$ sudo cp path-file-host /var/lib/docker/aufs/mnt/FULL_CONTAINER_ID/PATH-NEW-FILE   
```

或者  

```Bash
$ sudo cp path-file-host /var/lib/docker/devicemapper/mnt/123abc<<id>>/rootfs/root  
```

**例子：**

```bash
$ docker ps  

  

CONTAINER ID      IMAGE    COMMAND       CREATED      STATUS       PORTS        NAMES  

  

d8e703d7e303   solidleon/ssh:latest      /usr/sbin/sshd -D                      cranky_pare  

  

$ docker inspect -f   '{{.Id}}' cranky_pare  

  

or   

$ docker inspect -f   '{{.Id}}' d8e703d7e303  

 

d8e703d7e3039a6df6d01bd7fb58d1882e592a85059eb16c4b83cf91847f88e5  



$ sudo cp file.txt /var/lib/docker/aufs/mnt/**d8e703d7e3039a6df6d01bd7fb58d1882e592a85059eb16c4b83cf91847f88e5 
```

 

### 3.用输入输出符

```Bash
docker run -i ubuntu /bin/bash -c 'cat > /path/to/container/file' < /path/to/host/file/  
```

或者

```Bahs
docker exec -it <container_id> bash -c 'cat > /path/to/container/file' < /path/to/host/file/ 
```

