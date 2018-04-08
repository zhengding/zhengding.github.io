---
title: Linux之Java环境变量配置
data:  2018-04-08 08:53:01
tag:
- Linux
- Java
- 环境变量
categories: Linux
---

```bash
# vi /etc/profile
export JAVA_HOME=/home/fitech03/jdk1.8.0_161

export JRE_HOME=$JAVA_HOME/jre

export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH

export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH

# source /etc/profile
```

```bash
#/etc/environment

export JAVA_HOME=/home/fitech03/jdk1.8.0_161

export CLASSPATH=.:$JAVA_HOME/lib:$JAVA_HOME/jre/lib

# source /etc/environment
```