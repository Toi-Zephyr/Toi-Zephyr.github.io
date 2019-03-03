---
title: Linux 开启 SSH
date: 2019-03-03 19:54:02
description: 开启 SSH 以进行远程操控
categories: 教程
tags: [教程, 命令行, 树莓派, Ubuntu]
toc: true
---

1. 下载 `openssh`

```shell
$ sudo apt-get install openssh-server
```

2. 启动 `openssh`

```shell
$ /etc/init.d/ssh start
```
小贴士：

1. 传输文件到远程主机

```shell
scp -r -P 端口号 本机文件 远程用户名 @远程地址:/ 远程路径
scp -r -P 2333 demo/ root@8.8.8.8:/home/web
```

2. host 冲突

```shell
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
```

刷新 host

```shell
$ ssh-keygen -R <host>
```

