---
title: 使用极路由进行网络加速（SS）
date: 2018-06-06 22:53:04
description: 借助 Github开源社区上的SS插件 为极路由添加跳跃能力
categories: 教程
tags: [教程, SS, 极路由]
toc: true
---

# 使用极路由进行网络加速（SS）

## 0x001. 开通极路由开发者模式

善用百度，各个版本的开启模式不一样。

## 0x002. 开启极路由开发者模式

“云插件” > “全部插件” > “开发者模式” > ”确定
（不同版本可能会有出入）

<!--more-->

## 0x003. 使用SSH登录路由器

`使用工具`：
- PUTTY (Windows 平台)

![xCDM9.png](https://s1.ax2x.com/2018/05/17/xCDM9.png)

- 终端 （Mac平台）

```shell
$ ssh root@192.168.199.1 -p 1022
```

## 0x004. 安装SS插件

```shell
cd /tmp && curl -k -o shadow.sh https://raw.githubusercontent.com/qiwihui/hiwifi-ss/master/shadow.sh && sh shadow.sh && rm shadow.sh
```

## 0x005. 配置

## 鸣谢

[插件作者 qiwihui](https://github.com/qiwihui)
[插件 GitHub](https://github.com/qiwihui/hiwifi-ss)