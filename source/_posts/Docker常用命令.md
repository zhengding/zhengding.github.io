---
title: Docker常用命令
data:  2017-12-12 14:07:01
tag:
- Docker
categories: Docker
---

查看所有容器
docker ps -a
停止容器  
docker stop $(docker ps -a | grep "Exited" | awk '{print $1 }')  
删除容器
docker rm $(docker ps -a | grep "Exited" | awk '{print $1 }') 
或
docker rm -v $(docker ps -aq -f status=exited)
删除镜像
docker rmi $(docker images | grep "none" | awk '{print $3}')
进入容器
docker exec -it fc873 /bin/bash
