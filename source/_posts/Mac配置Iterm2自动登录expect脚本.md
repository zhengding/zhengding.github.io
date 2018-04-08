---
title: Mac配置Iterm2自动登录expect脚本
data:  2018-04-08 16:59:01
tag:
-  expect
-  iterm2
categories: 干货
---

1、新增文件auto_login.sh

```Bash
#/usr/bin/expect

set timeout 30
spawn ssh -p [lindex $argv 0] [lindex $argv 1]@[lindex $argv 2]
expect {
        "(yes/no)?"
        {send "yes\n";exp_continue}
        "password:"
        {send "[lindex $argv 3]\n"}
}
interact
```

2、将auto_login.sh移到/usr/local/bin

设置权限755

3、iterm2配置Login Shell

```bash
auto_ssh.sh 22 fitech01 192.168.101.30 12345678
```

