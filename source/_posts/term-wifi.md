---
title: 命令行链接 WPA Wi-Fi
date: 2019-03-03 10:25:44
description: 使用命令行命令链接Wi-Fi
categories: 教程
tags: [教程, 命令行]
toc: true
---

## 前期提要

使用命令行链接 WPA Wi-Fi 主要涉及以下命令

1. `wpasupplicant` 
    - 该包已经被集成到 Ubuntu 中

2. `vi`
    - 修改 Wi-Fi 文件配置

## 开始

### 生成配置文件

使用 `wpasupplicant` 生成链接 WPA 加密网络所需要的配置文件。

```shell
$ wpa_passphrase Your_WIFI_ESSID Your_WIFI_PASSWORD > xxx.conf
```

### 设置无线网络

添加 Wi-Fi 配置文件

```shell
$ vi /etc/network/interfaces
```

在该文件中，添加下属命令

```shell
auto wlan0
iface wlan0 inet dhcp
wpa-conf /.../.../xxx.conf

wireless-power off
```

⚠️ 注意： `/.../.../xxx.conf` 是前面步骤所生成的配置文件所在的路径

### 应用配置

将配置好的配置应用

```shell
$ sudo wpa_supplicant -B -i wlan0 -Dwext -c /.../.../xxx.conf
```

查看是否配置好

```shell
$ iwconfig wlan0
```

### 设置自动获取 IP

```shell
$ dhclient wlan0
```

### 重启电脑

重启电脑，之后 ping 一下，看看是否联通。

```shell
$ ping zophyr.com
```




