---
title: Docker常用命令
data:  2017-12-12 14:07:01
tag:
- Docker
categories: Docker
---

停止容器  
docker stop $(docker ps -a | grep "Exited" | awk '{print $1 }')  
删除容器
docker rm $(docker ps -a | grep "Exited" | awk '{print $1 }') 
或
docker rm -v $(docker ps -aq -f status=exited)
删除镜像
docker rmi $(docker images | grep "none" | awk '{print $3}')
