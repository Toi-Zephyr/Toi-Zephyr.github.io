---
title: 初试 Go 服务
date: 2018-11-11 17:08:56
description: 使用 Go 编写 web 后台服务 —— 系列文章之一
categories: 教程
tags: [教程, hello-world, Go, web, 后台服务]
toc: true
---

> 使用 Go 编写 web 后台服务 —— 系列文章之一

本系列文章分为三部分。

1. **[初试Go服务](https://zophyr.com/2018/11/11/go-web-serve-1/) —— 入门了解使用 Go 编写一个简单的 web 服务器，即「Hello World」程序。**
2. GoWeb 小应用 —— 进阶了解 go 服务编程的种种细节。
3. mux 源码分析及性能对比 —— 阅读 mux 的源码及分析对比各种由 go 编写的 http-routing 性能。

## 前期准备

### 选择框架

#### ⚠️ 为什么要选择使用框架？

简单来说，提高开发效率，不重复造轮子。

我们现在的学习目标是使用 Go 编写 web 后台，对于 http 协议的具体实现和原理，并非我们本次的重点。所以借助框架，可以提高我们的开发效率，尽快上手。

#### ⚠️ 选择什么框架？

一般来说，找开源的、流行的（个人看法）。

开源的好处有很多，暂且不表；而流行这代表其有着丰富的参考资料遇到问题也比较好找到方法解决。

这次我们选择使用 [gin](https://github.com/gin-gonic/gin) 来作为我们的框架。

#### ⚠️ 为什么选择 gin ？

> Gin is a HTTP web framework written in Go (Golang). It features a Martini-like API with much better performance -- up to 40 times faster. If you need smashing performance, get yourself some Gin.

gin GitHub 页面的介绍也许就回答了我们这个问题。

**gin 简单易用**。曾经 martini 是非常流行的 go web 框架，因为其简单易用，且接口清晰明了，所以深受大家喜爱，但是开发者与社区没有再对其更新迭代，所以我们不选择它。

**gin 快到起飞**。介绍中说有40倍的提升，并非玩笑。具体对比可以看本系列文章的第三篇「[mux 源码分析及性能对比]()」，可以自己跑一波分，感受差距。


## 编写服务代码

既然前期准备已经完成，那么我们开始编写代码。

### router.go

```go
package routes

import (
	"net/http"

	"github.com/gin-gonic/gin"
)

// 我们可以通过定一个 router 来定义路由的行为，这是 web 服务编程的核心
func Router() *gin.Engine {
	// 生成默认路由
	router := gin.Default()

	// 设置路径的处理，即当访问 /ginTest 时，后台作出的反应
	router.GET("/ginTest", func(c *gin.Context) {
		// 我们选择使用 Json 作为响应，返回一个 Json
		c.JSON(http.StatusOK, gin.H{
			"message": "Hello World! by go serve with gin.",
		})
	})

	// 返回该路由
	return router
}
```

### main.go

```go
package main

import (
	"os"

	"studyRouterWithGin/routes"

	flag "github.com/spf13/pflag"
)

const (
	// 设置默认端口号及 host
	defaultHost string = "localhost"
	defaultPort string = "8080"
)

func main() {
	// 调用我们编写好的路由
	r := routes.Router()

	// 查看本地 host 如果不存在，便使用我们默认的 host
	host := os.Getenv("HOST")
	if len(host) == 0 {
		host = defaultHost
	}

	// 查看本地 port 如果不存在，便使用我们默认的端口
	port := os.Getenv("PORT")
	if len(port) == 0 {
		host = defaultPort
	}

	// 读取终端中输入的 host 与 端口号
	hostFlag := flag.StringP("hostname", "h", defaultHost, "The host to deploy at.")
	portFlag := flag.StringP("port", "p", defaultPort, "The port for listening.")

	flag.Parse()

	// 如果有用户输入的端口号及 host 就使用用户输入的
	if len(*hostFlag) != 0 {
		host = *hostFlag
	}
	if len(*portFlag) != 0 {
		port = *portFlag
	}

	// 启动服务，服务器为 host:port
	r.Run(host + ":" + port)
}
```

## 启动服务

我们的文件目录结构为

```shell
.
├── main.go
└── routes
    └── router.go
```

我们在终端中输入下述命令：

```shell
$ go run main.go
```

可得输出：

```shell
$ go run main.go
[GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.

[GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
 - using env:   export GIN_MODE=release
 - using code:  gin.SetMode(gin.ReleaseMode)

[GIN-debug] GET    /ginTest                  --> studyRouterWithGin/routes.Router.func1 (3 handlers)
[GIN-debug] Listening and serving HTTP on localhost:8080
```

**服务启动成功**

## 测试服务

### Curl 测试

我们新建终端，在其中输入下述命令：

```shell
$ curl -v localhost:8080/ginTest
```

可得输出：

```shell
$ curl -v localhost:8080/ginTest
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8080 (#0)
> GET /ginTest HTTP/1.1
> Host: localhost:8080
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Content-Type: application/json; charset=utf-8
< Date: Thu, 15 Nov 2018 08:00:15 GMT
< Content-Length: 71
<
* Connection #0 to host localhost left intact
{"message":"You've successfully received a message from a gin server."}
```

返回中每行的第一个符号含义：

- `*` 表示 curl 任务；

- `>` 发送的信息;

- `<` 返回的信息

### Apache web 压力测试

我们新建终端，在其中输入下述命令：

```shell
$ ab -n 10000 -c 100 http://127.0.0.1:8080/ginTest
```

可得输出：

```shell
$ ab -n 10000 -c 100 http://127.0.0.1:8080/ginTest
This is ApacheBench, Version 2.3 <$Revision: 1826891 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 127.0.0.1 (be patient)
Completed 1000 requests
Completed 2000 requests
Completed 3000 requests
Completed 4000 requests
Completed 5000 requests
Completed 6000 requests
Completed 7000 requests
Completed 8000 requests
Completed 9000 requests
Completed 10000 requests
Finished 10000 requests


Server Software:
Server Hostname:        127.0.0.1
Server Port:            8080

Document Path:          /ginTest
Document Length:        71 bytes

Concurrency Level:      100
Time taken for tests:   0.928 seconds
Complete requests:      10000
Failed requests:        0
Total transferred:      1940000 bytes
HTML transferred:       710000 bytes
Requests per second:    10771.09 [#/sec] (mean)
Time per request:       9.284 [ms] (mean)
Time per request:       0.093 [ms] (mean, across all concurrent requests)
Transfer rate:          2040.62 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    4   1.3      4       9
Processing:     1    5   1.3      5      13
Waiting:        0    3   1.3      3      10
Total:          4    9   1.8      9      18

Percentage of the requests served within a certain time (ms)
  50%      9
  66%     10
  75%     10
  80%     10
  90%     11
  95%     13
  98%     14
  99%     14
 100%     18 (longest request)
```

同时，在运行服务器的终端中，我们可得到类似下述的结果：

```shell
[GIN] 2018/11/15 - 16:01:46 | 200 |      13.855µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |        13.6µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |       12.21µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |      11.624µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |      10.914µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |      11.466µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |      11.327µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |      65.145µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |      11.076µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |      11.242µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |      13.757µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |      13.616µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |      13.308µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |      24.963µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |      25.624µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |        21.9µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |      39.611µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |      25.627µs |       127.0.0.1| GET      /ginTest
[GIN] 2018/11/15 - 16:01:46 | 200 |      18.268µs |       127.0.0.1| GET      /ginTest
```

## 三个终端的情况

![ivu7Kx.png](https://s1.ax1x.com/2018/11/15/ivu7Kx.png)

