---
title: 搭建ShadowSocks
data:  2017-12-11 14:53:01
tag:
- ShadowSocks
categories: Linux
---

## VPS安装ShadowSocks
推荐看秋水逸冰的[《Shadowsocks 一键安装脚本（四合一）》][1]的安装教程。
运行以下命令:

```bash
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```

选择脚本（Python、R、Go、libev），任选一个：

```bash
Which Shadowsocks server you'd select:
1.Shadowsocks-Python
2.ShadowsocksR
3.Shadowsocks-Go
4.Shadowsocks-libev
Please enter a number (default 1):
```

笔者选择Shadowsocks-Go，输入3......然后，输入密码和端口，笔者直接回车用默认：

```bash
You choose = Shadowsocks-Go

Please enter password for Shadowsocks-Go
(default password: teddysun.com):

password = teddysun.com

Please enter a port for Shadowsocks-Go [1-65535]
(default port: 8989):

port = 8989


Press any key to start...or Press Ctrl+C to cancel
```

安装成功后，命令行出现：

```bash
Congratulations, Shadowsocks-Go server install completed!
Your Server IP        :  45.32.73.59
Your Server Port      :  8989
Your Password         :  teddysun.com
Your Encryption Method:  aes-256-cfb

Welcome to visit: https://teddysun.com/486.html
Enjoy it!
```

（如果安装失败，请尝试其他脚本）


[1]:	https://teddysun.com/486.html