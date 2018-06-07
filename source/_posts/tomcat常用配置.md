---
title: tomcat常用配置
data:  2018-06-07 14:39:01
tag:
- tomcat
categories: 后端
---

1、启动跳过jar包检查

在conf/catalina.properties中加入

```bash
org.apache.el.parser.SKIP_IDENTIFIER_CHECK=true 
```

2、应用中需要绘图，例如activiti的流程图：

在catalina.sh加入

![1528354046187](C:\Users\ADMINI~1\AppData\Local\Temp\1528354046187.png)