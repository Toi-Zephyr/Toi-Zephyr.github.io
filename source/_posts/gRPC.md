---
title: gRPC 初体验
date: 2019-01-17 11:04:03
description: gRPC 简单上手体验
categories: gRPC
tags: [gRPC]
toc: true
---

# gRPC 初体验

## 什么是 gRPC

**gRPC 是一个现代化高性能开源远程过程调用（RPC）框架。**

先来了解一下RPC。RPC（Remote Procedure Call）远程过程调用，它是一种通过网络从远程计算机程序上请求服务，而不需要了解底层网络技术的协议。

而 gRPC 是谷歌自己的 RPC。Google 基于 RPC 而开发的通用 RPC 框架。与许多 RPC 系统类似，gRPC 也是基于以下理念：定义一个服务，指定其能够被远程调用的方法（包含参数和返回类型）。在服务端实现这个接口，并运行一个 gRPC 服务器来处理客户端调用。在客户端拥有一个存根能够像服务端一样的方法。

## 快速上手

### 安装 gPRC

```shell
$ go get -u google.golang.org/grpc
```

### 安装 Protocol Buffers v3

```shell
$ go get -u github.com/golang/protobuf/protoc-gen-go
```

### Hello World!

在安装的gRPC中，含有相关案例代码。

存放路径：

```shell
$GOPATH/src/google.golang.org/grpc/examples
```

1. 进入 hello world

```shell
cd $GOPATH/src/google.golang.org/grpc/examples
```

2. 启动服务端

```shell
$ go run greeter_server/main.go
```

3. 启动客户端

```shell
$ go run greeter_client/main.go
```

4. 结果

客户端输出：

```shell
Greeting: Hello world
```

## 代码阅读

### 服务端

```go
// SayHello implements helloworld.GreeterServer
func (s *server) SayHello(ctx context.Context, in *pb.HelloRequest) (*pb.HelloReply, error) {
	log.Printf("Received: %v", in.Name)
	return &pb.HelloReply{Message: "Hello " + in.Name}, nil
}
```

在 service 的 main.go 中，生命并实现 SayHello 接口。

### 客户端

```go
// 建立 gPRC 链接服务
conn, err := grpc.Dial(address, grpc.WithInsecure())

// 创建新客户端
c := pb.NewGreeterClient(conn)

// 调用 SayHello 服务
r, err := c.SayHello(ctx, &pb.HelloRequest{Name: name})
```