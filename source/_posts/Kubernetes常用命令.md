---
title: Kubernetes常用命令
data:  2019-02-21 14:53:01
tag:
- Kubernetes
categories: Docker
---

1、批量删除Evicted Pods

```shell
kubectl get pods | grep Evicted | awk '{print $1}' | xargs kubectl delete pod
kubectl get pods | grep OutOfcpu | awk '{print $1}' | xargs kubectl delete pod
```

