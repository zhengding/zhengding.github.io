---
title: Centos7.0 中的中iptables、firewall和SELINUX
data:  2017-12-8 17:14:01
tag:
- 防火墙
- iptables
- firewall
- SELINUX
categories: Linux
---

CentOS 7.0默认使用的是firewall作为防火墙，这里改为iptables防火墙。

## firewall
firewall能够允许哪些服务可用，那些端口可用.... 属于更高一层的防火墙。
firewall的底层是使用iptables进行数据过滤，建立在iptables之上。

firewall是动态防火墙，使用了D-BUS方式，修改配置不会破坏已有的数据链接。  

## 关闭firewall

```bash
systemctl stop firewalld.service #停止firewall
systemctl disable firewalld.service #禁止firewall开机启动
firewall-cmd --state #查看默认防火墙状态（关闭后显示notrunning，开启后显示running）  
```

## iptables
iptables用于过滤数据包，属于网络层防火墙.  

在设置iptables后需要重启iptables，会重新加载防火墙模块，而模块的装载将会破坏状态防火墙和确立的连接。会破坏已经对外提供数据链接的程序。可能需要重启程序。  

## iptables防火墙
```bash
yum install iptables-services #安装iptables
vi /etc/sysconfig/iptables #编辑iptables防火墙配置文件
```

```bash
Firewall configuration written by system-config-firewall
Manual customization of this file is not recommended.
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT
:wq! #保存退出
```

```bash
systemctl restart iptables.service #最后重启防火墙使配置生效
systemctl enable iptables.service #设置防火墙开机启动
yum remove iptables   #卸载iptables
```

## SELinux

SELinux(Security-Enhanced Linux) 是美国国家安全局（NSA）对于强制访问控制的实现，是 Linux历史上最杰出的新安全子系统。它不是用来防火墙设置的。但它对Linux系统的安全很有用。Linux内核(Kernel)从2.6就有了SELinux。  

SELinux是一种基于 域-类型 模型（domain-type）的强制访问控制（MAC）安全系统，它由NSA编写并设计成内核模块包含到内核中，相应的某些安全相关的应用也被打了SELinux的补丁，最后还有一个相应的安全策略。任何程序对其资源享有完全的控制权。假设某个程序打算把含有潜在重要信息的文件扔到/tmp目录下，那么在DAC情况下没人能阻止他。SELinux提供了比传统的UNⅨ权限更好的访问控制。

## 关闭SELinux

```bash
vi  /etc/selinux/config
```

```bash
SELINUX=enforcing #注释掉  
SELINUXTYPE=targeted #注释掉  
SELINUX=disabled #增加  
```

:wq! #保存退出
setenforce 0 #使配置立即生效