---
title: iTerm链接Remote Server
date: 2022-05-09 13:29:00
author: Harrison
categories:
  - "iTerm"
tags:
  - "Learning"
  - "iTerm"
  - "Server"

---
iTerm链接Remote Server~
<!-- more -->
### 0、前言

iTerm可以通过SSH链接远端server，常用命令是：`ssh <user>@<ip>`，然后输入密码即可连接服务器。

但是每次输入用户和IP较麻烦，因此可以写一个脚本，然后将脚本放在`/usr/local/bin`下，每次执行脚本即可登录服务器。

### 1、编写脚本

在`/usr/local/bin`目录下创建文件`serverLogin`(没有后缀)

```bash
#!/usr/bin/expect -f
  set user <user>
  set host <ip>
  set password <yourPasswprd>
  set timeout -1

  spawn ssh $user@$host
  expect "*assword:*"
  send "$password\r"
  interact
```

<user> <ip> <yourPasswprd> 换成自己的相关信息

Tips: 需要给`serverLogin`文件添加可执行权限

### 2、剧终

打开iTerm，输入serverLogin即可连接远端服务器。