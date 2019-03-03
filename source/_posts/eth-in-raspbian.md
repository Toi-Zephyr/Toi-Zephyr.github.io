---
title: 在树莓派上安装 Geth
date: 2019-03-03 10:25:44
description: 树莓派 + 以太坊 —— 学习如何在树莓派上安装运行以太坊
categories: 教程
tags: [教程, 命令行, 以太坊]
toc: true
---

## 前期准备

### 版本更新

```shell
$ sudo apt-get update
$ sudo apt-get dist-upgrade
```

### CLI / Desktop

选择使用命令行还是图形界面。

```shell
$ sudo raspi-config
```

1. 3 -> boot

2. B1 -> Desktop / CLI

3. B1 -> Console or B3 -> Desktop

### Geth 支持软件

```shell
$ sudo apt-get install git golang libgmp3-dev
```

## 安装 Geth

### 下载 Geth 源码

```shell
$ mkdir src

$ cd src

$ git clone https://github.com/ethereum/go-ethereum.git
```

### 编译 Geth 源码

```shell
$ cd go-ethereum

$ make
```

### 添加 Geth 到环境

```shell
$ sudo cp build/bin/geth /usr/local/bin/
```

### 测试 Geth

```shell
$ geth version
```