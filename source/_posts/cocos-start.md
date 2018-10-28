---
title: Cocos2d-x 环境配置
date: 2018-05-15 08:49:15
description: "Cocos2d 是一个开源的2D游戏框架。<br>本文将描述如何在Mac平台下进行 Cocos2d-x 开发环境配置。"
categories: 教程
tags: [cocos2d, 环境配置, hello-world, 教程]
toc: true
---

# Cocos2d-x 安装配置

## 当前环境

1. Mac 10.11.6
2. Python 2.7.x

## 开工大吉

1.[下载Cocos2d-x 3.16](http://www.cocos.com/download)获得压缩包`cocos2d-x-3.16.zip`
![xvbPr.png](https://s1.ax2x.com/2018/05/17/xvbPr.png)

2.解压压缩包`cocos2d-x-3.16.zip`
![xvpSY.png](https://s1.ax2x.com/2018/05/17/xvpSY.png)

3.进入到文件夹`cocos2d-x-3.16`，运行

```shell
$ python setup.py
```

4.测试环境配置

```shell
$ cocos -v
Python 2.7.10
cocos2d-x-3.16
Cocos Console 2.3
```

5.完成！

## HelloWorld！

### 新建项目
```shell
$ cocos new project-name -p com.your-domain.xxx -l cpp -d /Codeccc/
```

> `new` 后面接项目名称
> `-p` 表示包名
> `-l` 表示开发语言
> `-d` 表示文件存储目录

### 新建项目目录

```shell
.
├── CMakeLists.txt
├── Classes
├── Resources
├── cocos2d
├── proj.android
├── proj.android-studio
├── proj.ios_mac
├── proj.linux
└── proj.win32
```

### 运行新项目

1.打开 `HelloWorld.xcodeproj`
![xvJ6X.png](https://s1.ax2x.com/2018/05/17/xvJ6X.png)

2.打开后
![xvh3l.png](https://s1.ax2x.com/2018/05/17/xvh3l.png)

3.点击右上件，选择Mac平台
![xv44y.png](https://s1.ax2x.com/2018/05/17/xv44y.png)

4.点击小三角形，运行（第一次编译有点慢）
![xvvMJ.png](https://s1.ax2x.com/2018/05/17/xvvMJ.png)

5.完成！

