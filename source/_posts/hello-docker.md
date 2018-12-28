---
title: Docker 使用初体验
date: 2018-12-20 18:12:22
description: “不落地”云软件开发流水线实战第一步
categories: 教程
tags: [教程, hello-world, docker]
toc: true
---

# Docker 使用初体验

## Docker 简介

### 什么是 Docker ？

Docker是一个开放源代码软件项目，让应用程序布署在软件货柜下的工作可以自动化进行，借此在Linux操作系统上，提供一个额外的软件抽象层，以及操作系统层虚拟化的自动管理机制。 Docker利用Linux核心中的资源分离机制，例如cgroups，以及Linux核心名字空间，来创建独立的容器。

### Docker 特点

1. **轻量性**
运行在一台机器上的容器共享其操作系统内核，它们启动使用更少的内存。Docker的镜像文件可以通过分层的文件系统及共享的公共文件创建，节省磁盘空间并提高下载效率。

2. **开放性**
Docker容器基于开放标准，使容器能够在所有的主流Linux及windows或者其他设备的顶层运行。

3. **安全性**
容器隔离彼此和基础架构应用，同时为应用程序提供了额外的保护层。

## 快速上手

### 安装 Docker

[官网安装下载](https://www.docker.com)

### 拉取镜像

我们可以通过

```shell
docker pull ${image_uri}:${image_tag}
```

命令，可以从远程仓库（默认是Docker Hub）中拉取所需要的镜像。

例如：

```shell
$ docker pull ubuntu:16.04
```

然后通过`docker images`命令，查看保存在本地镜像，发现多了一个ubuntu的镜像。

### 容器创建、启动、停止、登入

我们可以通过

```shell
$ docker run -it ${image_id}
```

创建一个容器。

其中：

1. `image_id`是镜像的id，可通过`docker images`查看
2. `-it`可以在启动后，连上容器的终端

通过

```shell
docker start ${container_id}
```

我们可以启动该容器。

通过

```shell
docker stop ${container_id}
```

我们可以停止容器。

### Dockerfile 构建镜像

Dockerfile 可以通过编写一个构建过程，来一站式构建镜像。

eg.

```shell
FROM centos:7

MAINTAINER dalaobringmefly <hongzc@mail2.sysu.edu.cn>

RUN yum install -y gcc

RUN rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm

RUN yum install golang -y

ENV GOROOT /usr/lib/golang
ENV PATH=$PATH:/usr/lib/golang/bin

RUN mkdir -p /root/gopath
RUN mkdir -p /root/gopath/src
RUN mkdir -p /root/gopath/pkg
RUN mkdir -p /root/gopath/bin
ENV GOPATH /root/gopath

RUN go get github.com/boltdb/bolt
RUN go get github.com/graphql-go/graphql
RUN go get -u github.com/go-sql-driver/mysql

RUN mkdir -p /root/gopath/src/BringMeFlyServer/
COPY . /root/gopath/src/BringMeFlyServer/

WORKDIR /root/gopath/src/BringMeFlyServer
RUN go build -o server.bin main.go

CMD /root/gopath/src/BringMeFlyServer/server.bin
```


