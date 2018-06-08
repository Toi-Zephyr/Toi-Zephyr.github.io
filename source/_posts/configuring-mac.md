---
title: 新拿到 Mac 应该做的事情
date: 2018-06-01 05:51:22
description: 配置 Mac 各种环境，让 Mac 成为顺手的开发工具
categories: others
tags: [others]
toc: true
---

# 拿到新Mac要做的事

## 环境介绍

1. macOS 10.13.5

## Mac基础设置

### 必做

#### 允许任何软件来源

```shell
$ sudo spctl --master-disable
```

#### 打开模糊音

设置 -> 输入法 -> ☑️纠正模糊拼音

![RGzvA.png](https://s1.ax2x.com/2018/06/06/RGzvA.png)

#### 打开三指拖移

设置 -> 辅助功能 -> 鼠标与触控板 -> 触控板选项 -> 启用拖移，选择三指拖移

![RGVlO.png](https://s1.ax2x.com/2018/06/06/RGVlO.png)

#### 打开电池百分比

点击右上角电池图标 -> 显示百分比

![RGT2u.png](https://s1.ax2x.com/2018/06/06/RGT2u.png)

### 选做

#### 整理「程序坞」(Docker)

删除默认无用icon

![REcRi.png](https://s1.ax2x.com/2018/06/08/REcRi.png)

#### 设置 「访达」(Finder)

不显示硬盘

![RG0Bq.png](https://s1.ax2x.com/2018/06/06/RG0Bq.png)

侧边栏信息丰富

![RGQse.png](https://s1.ax2x.com/2018/06/06/RGQse.png)

## 环境配置第一步 —— 基础设置

### 下载 Xcode

拿到电脑第一时间，应该立刻下载 `Xcode` 并且 编写 `Hello World` 程序测试Xcode安装是否成功

### 安装 Homebrew

```shell
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

参考 [Homebrew 官网](https://brew.sh)

## 配置环境第二步 —— 开发环境

### 配置命令行

1.安装 「tree」 命令 —— 树状图显示当下目录文件

```shell
$ brew install tree
```

2.安装 「ccat」 命令 —— cat的代码高亮版本

```shell
$ brew install ccat
```

### 配置 node 环境

```shell
$ brew install node
```

设置镜像

```shell
$ npm config set registry https://registry.npm.taobao.org --global
$ npm config set disturl https://npm.taobao.org/dist --global
```

### 配置 React Native 环境

**配置环境：**

```shell
$ brew install watchman
```

```shell
$ npm install -g react-native-cli
```

**测试环境：**

```shell
$ react-native init helloWorld
```

```shell
$ cd helloWorld
$ react-native run-ios
```

![RGmV9.png](https://s1.ax2x.com/2018/06/06/RGmV9.png)


## 配置环境第三步 —— 安装开发工具

### 安装 「影子短袜」

![RGpVy.png](https://s1.ax2x.com/2018/06/06/RGpVy.png)

[GitHub 地址](https://github.com/qinyuhang/ShadowsocksX-NG-R/releases)

### 安装 「WebStorm」

![RGdqR.png](https://s1.ax2x.com/2018/06/06/RGdqR.png)

[WebStorm 官网](http://www.jetbrains.com/webstorm/)

### 安装 「VS Code」

![RGMEY.png](https://s1.ax2x.com/2018/06/06/RGMEY.png)

[VS Code 官网](https://code.visualstudio.com)

配置 `VS Code` 参考另一篇文章（还没写）

### 安装 「GitHub Desktop」

![RGGjr.png](https://s1.ax2x.com/2018/06/06/RGGjr.png)

[GitHub 地址](https://github.com/desktop/desktop)

### 安装 「微信小程序开发工具」

![RGJFJ.png](https://s1.ax2x.com/2018/06/06/RGJFJ.png)

[微信小程序官网](https://mp.weixin.qq.com/cgi-bin/wx)

### 安装 「Go2Shell」

![RGaEK.png](https://s1.ax2x.com/2018/06/06/RGaEK.png)

[Go2Shell 官网](http://zipzapmac.com/go2shell)

### 安装 「Navicat Premium」

![RGR3d.png](https://s1.ax2x.com/2018/06/06/RGR3d.png)

[Navicat 官网](https://www.navicat.com.cn/)

### 安装 「Postman」

![ROED2.png](https://s1.ax2x.com/2018/06/06/ROED2.png)

[Postman 官网](https://www.getpostman.com)

## 安装常用 App

### 下载 「Chrome」

- 优秀的浏览器

![RGAwS.png](https://s1.ax2x.com/2018/06/06/RGAwS.png)

[Chrome 官网](https://www.google.com/chrome/)

### 下载 「百度网盘不限速下载」

- 突破百度云恶心的限速

![RGN7N.png](https://s1.ax2x.com/2018/06/06/RGN7N.png)

[GitHub 地址](https://github.com/high-speed-downloader/high-speed-downloader)

### 下载 「MWeb」

- 极其优秀的 Markdown 编辑器

![RGlYu.png](https://s1.ax2x.com/2018/06/06/RGlYu.png)

[MWeb 官网](http://www.mweb.im)

### 下载 「Magnet」

- 窗口管理软件

![RMB4J.png](https://s1.ax2x.com/2018/06/06/RMB4J.png)

[Magnet 官网](http://magnet.crowdcafe.com)

### 下载 「Bartender」

- 状态栏图标管理

![RONv9.png](https://s1.ax2x.com/2018/06/06/RONv9.png)

[Bartender 官网](https://www.macbartender.com)

### 下载 「The Unarchiver」

- 解压软件

![ROovy.png](https://s1.ax2x.com/2018/06/06/ROovy.png)

### 下载 「小历」

- 看时间，看日历

![ROqlX.png](https://s1.ax2x.com/2018/06/06/ROqlX.png)

### 下载 「Unclutter」

- 文件中转站

![Rb3Bl.png](https://s1.ax2x.com/2018/06/06/Rb3Bl.png)

[Unclutter 官网](https://unclutterapp.com)

### 下载 「TeamViewer」

- 远程桌面

![RbDq6.png](https://s1.ax2x.com/2018/06/06/RbDq6.png)

[TeamViewer 官网](https://www.teamviewer.com)

### 下载 「iStat Menus」

- 状态监控

![RbT2K.png](https://s1.ax2x.com/2018/06/06/RbT2K.png)

[iStat Menus 官网](https://bjango.com/mac/istatmenus/)

## 选装 App

### 下载 「iTools Pro」

- 管理 iOS 设备

![RO2dR.png](https://s1.ax2x.com/2018/06/06/RO2dR.png)

[iTools 官网](https://www.itools.cn)

### 下载 「HandShaker」

- 管理 Android 手机

![ROe1z.png](https://s1.ax2x.com/2018/06/06/ROe1z.png)

[HandShaker 官网](https://www.smartisan.com/apps/handshaker)

### 下载 「Clock mini」

- Docker上面的小时钟

![ROnzi.png](https://s1.ax2x.com/2018/06/06/ROnzi.png)


