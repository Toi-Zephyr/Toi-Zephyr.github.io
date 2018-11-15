---
title: mux 源码分析及性能对比
date: 2018-11-11 17:19:11
description: 使用 Go 编写 web 后台服务 —— 系列文章之三
categories: 教程
tags: [Go, web, 后台服务, 源码阅读]
toc: true
---

> 使用 Go 编写 web 后台服务 —— 系列文章之三

本系列文章分为三部分。

1. [初试Go服务](https://zophyr.com/2018/11/11/go-web-serve-1/) —— 入门了解使用 Go 编写一个简单的 web 服务器，即「Hello World」程序。
2. GoWeb 小应用 —— 进阶了解 go 服务编程的种种细节。
3. **[mux 源码分析及性能对比](https://zophyr.com/2018/11/11/go-web-serve-3/) —— 阅读 mux 的源码及分析对比各种由 go 编写的 http-routing 性能。**

## Part 1. 源码分析

### 什么是 mux ? 

> A powerful URL router and dispatcher for golang. 

上述这段话是 [mux](https://github.com/gorilla/mux) 的简介。简单表述了什么是 mux ，一个强大的 URL 路由框架。

其出现是为了解决 golang 自带的 `http.SeverMux` 路由的缺陷：

1. `http.SeverMux` 不支持正则路由。
2. `http.SeverMux` 只支持路径匹配，不支持按照Method，header，host等信息匹配，所以也就没法实现RESTful架构。

### mux.go

> mux 核心。

一般用法：

```go
package main

import (
    "net/http"
    "log"
    "github.com/gorilla/mux"
)

func YourHandler(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("Gorilla!\n"))
}

func main() {
    r := mux.NewRouter()
    // Routes consist of a path and a handler function.
    r.HandleFunc("/", YourHandler)

    // Bind to a port and pass our router in
    log.Fatal(http.ListenAndServe(":8000", r))
}
```

源码部分：

#### func NewRouter() *Router

```go
// NewRouter returns a new router instance.
func NewRouter() *Router {
	return &Router{namedRoutes: make(map[string]*Route), KeepContext: false}
}
```

> NewRouter为创建Router实例。

#### func (r *Router) ServeHTTP(w http.ResponseWriter, req *http.Request)

```go
func (r *Router) ServeHTTP(w http.ResponseWriter, req *http.Request) {
	if !r.skipClean {
		path := req.URL.Path
		if r.useEncodedPath {
			path = req.URL.EscapedPath()
		}
		// Clean path to canonical form and redirect.
		if p := cleanPath(path); p != path {

			// Added 3 lines (Philip Schlump) - It was dropping the query string and #whatever from query.
			// This matches with fix in go 1.2 r.c. 4 for same problem.  Go Issue:
			// http://code.google.com/p/go/issues/detail?id=5252
			url := *req.URL
			url.Path = p
			p = url.String()

			w.Header().Set("Location", p)
			w.WriteHeader(http.StatusMovedPermanently)
			return
		}
	}
	var match RouteMatch
	var handler http.Handler
	if r.Match(req, &match) {
		handler = match.Handler
		req = setVars(req, match.Vars)
		req = setCurrentRoute(req, match.Route)
	}

	if handler == nil && match.MatchErr == ErrMethodMismatch {
		handler = methodNotAllowedHandler()
	}

	if handler == nil {
		handler = http.NotFoundHandler()
	}

	if !r.KeepContext {
		defer contextClear(req)
	}

	handler.ServeHTTP(w, req)
}
```

其中：

```go
if !r.skipClean {
    ···
}
```

> 这部分是在对已经解释过的 flag 进行处理。

```go
var match RouteMatch
    ······
handler.ServeHTTP(w, req)
```

> 余下这部分是在寻找匹配的路由，然后进入对应匹配路由所对应的 handler 。

#### func (r *Router) Match(req *http.Request, match *RouteMatch) bool 

我们看看是如何匹配路由的。

```go
func (r *Router) Match(req *http.Request, match *RouteMatch) bool {
	for _, route := range r.routes {
		if route.Match(req, match) {
			// Build middleware chain if no error was found
			if match.MatchErr == nil {
				for i := len(r.middlewares) - 1; i >= 0; i-- {
					match.Handler = r.middlewares[i].Middleware(match.Handler)
				}
			}
			return true
		}
	}

	if match.MatchErr == ErrMethodMismatch {
		if r.MethodNotAllowedHandler != nil {
			match.Handler = r.MethodNotAllowedHandler
			return true
		}

		return false
	}

	// Closest match for a router (includes sub-routers)
	if r.NotFoundHandler != nil {
		match.Handler = r.NotFoundHandler
		match.MatchErr = ErrNotFound
		return true
	}

	match.MatchErr = ErrNotFound
	return false
}
```

> 非常直接粗暴！直接遍历了routes，一个一个的去进行匹配，直到找到可以匹配上的。根据其简单的算法，我们可以发现这是具有一定的性能问题，会造成额外的性能开销。根据我们后面的性能测试中来看，mux 是具有一定的性能问题。

#### 其余的为接口构建

有常规和非常规，此处就不展开分析。

### Part 1. 总结

mux 的源码是比较简单直接的。思路很清晰，通读几遍便可以了解该框架的整体构架与大体思路。

但是，也正是其简单直接的编写方式，并未用的什么高深巧妙的算法，所以其性能存在一定的问题。

而我们后面的性能测试，也证实了这一点。话不多说，我们进入性能比对部分。

## Part 2. 性能比对

### Speedometer

> A go-http-routing benchmark

为了进行性能测试，我们需要一个基准测试软件。

一开始我找到了名为「[go-http-routing-benchmark](https://github.com/julienschmidt/go-http-routing-benchmark)」的基准测试，但是其已经多年没有更新，而且按照 README 操作也并不能运行。所以参考该项目的核心代码，仿制了一个自己的 go-http-routing benchmark —— [Speedometer](https://github.com/Zophyr/Speedometer)

### 测试方法

#### 安装 Speedometer

```shell
$ go get -u github.com/Zophyr/Speedometer
```

> ⚠️ 该步骤可能会需要较多的时间，请耐心等待。

#### 运行 Speedometer

1.进入 Speedometer 文件

```shell
$ cd $GOPATH/src/github.com/Zophyr/Speedometer
```

2.执行 Speedometer 测试

**测试所有 router**

```shell
$ go test -bench=.
```

**测试部分 router**

> eg. 测试 Martini & Gin & GorillaMux

```shell
$ go test -bench="Martini|Gin|GorillaMux"
```

***更详尽介绍 [Speedometer](https://github.com/Zophyr/Speedometer)***

#### 测试结果 ***Gin*** VS ***GorillaMux***

```shell
#GithubAPI Routes: 203
   Gin: 52064 Bytes
   GorillaMux: 1329248 Bytes

#GPlusAPI Routes: 13
   Gin: 3936 Bytes
   GorillaMux: 66544 Bytes

#ParseAPI Routes: 26
   Gin: 6912 Bytes
   GorillaMux: 106632 Bytes

#Static Routes: 157
   Gin: 30480 Bytes
   GorillaMux: 591136 Bytes

goos: darwin
goarch: amd64
pkg: github.com/Zophyr/Speedometer
BenchmarkGin_Param               	20000000	        65.3 ns/op	       0 B/op	       0 allocs/op
BenchmarkGorillaMux_Param        	  500000	      3135 ns/op	    1280 B/op	      10 allocs/op
BenchmarkGin_Param5              	20000000	       120 ns/op	       0 B/op	       0 allocs/op
BenchmarkGorillaMux_Param5       	  300000	      4793 ns/op	    1344 B/op	      10 allocs/op
BenchmarkGin_Param20             	 5000000	       304 ns/op	       0 B/op	       0 allocs/op
BenchmarkGorillaMux_Param20      	  200000	     10554 ns/op	    3452 B/op	      12 allocs/op
BenchmarkGin_ParamWrite          	10000000	       153 ns/op	       0 B/op	       0 allocs/op
BenchmarkGorillaMux_ParamWrite   	  500000	      3363 ns/op	    1280 B/op	      10 allocs/op
BenchmarkGin_GithubStatic        	20000000	        93.2 ns/op	       0 B/op	       0 allocs/op
BenchmarkGorillaMux_GithubStatic 	  100000	     17749 ns/op	     976 B/op	       9 allocs/op
BenchmarkGin_GithubParam         	10000000	       161 ns/op	       0 B/op	       0 allocs/op
BenchmarkGorillaMux_GithubParam  	  200000	     10595 ns/op	    1296 B/op	      10 allocs/op
BenchmarkGin_GithubAll           	   50000	     29143 ns/op	       0 B/op	       0 allocs/op
BenchmarkGorillaMux_GithubAll    	     300	   5387694 ns/op	  251648 B/op	    1994 allocs/op
BenchmarkGin_GPlusStatic         	20000000	        66.4 ns/op	       0 B/op	       0 allocs/op
BenchmarkGorillaMux_GPlusStatic  	 1000000	      2345 ns/op	     976 B/op	       9 allocs/op
BenchmarkGin_GPlusParam          	20000000	        87.5 ns/op	       0 B/op	       0 allocs/op
BenchmarkGorillaMux_GPlusParam   	  300000	      4127 ns/op	    1280 B/op	      10 allocs/op
BenchmarkGin_GPlus2Params        	20000000	       116 ns/op	       0 B/op	       0 allocs/op
BenchmarkGorillaMux_GPlus2Params 	  200000	      7711 ns/op	    1296 B/op	      10 allocs/op
BenchmarkGin_GPlusAll            	 1000000	      1248 ns/op	       0 B/op	       0 allocs/op
BenchmarkGorillaMux_GPlusAll     	   20000	     65454 ns/op	   16112 B/op	     128 allocs/op
BenchmarkGin_ParseStatic         	20000000	        72.6 ns/op	       0 B/op	       0 allocs/op
BenchmarkGorillaMux_ParseStatic  	  300000	      3825 ns/op	     976 B/op	       9 allocs/op
BenchmarkGin_ParseParam          	20000000	        76.3 ns/op	       0 B/op	       0 allocs/op
BenchmarkGorillaMux_ParseParam   	  500000	      3729 ns/op	    1280 B/op	      10 allocs/op
BenchmarkGin_Parse2Params        	20000000	        91.3 ns/op	       0 B/op	       0 allocs/op
BenchmarkGorillaMux_Parse2Params 	  500000	      3946 ns/op	    1296 B/op	      10 allocs/op
BenchmarkGin_ParseAll            	 1000000	      2134 ns/op	       0 B/op	       0 allocs/op
BenchmarkGorillaMux_ParseAll     	   10000	    127993 ns/op	   30288 B/op	     250 allocs/op
BenchmarkGin_StaticAll           	  100000	     19305 ns/op	       0 B/op	       0 allocs/op
BenchmarkGorillaMux_StaticAll    	    1000	   1645517 ns/op	  153328 B/op	    1421 allocs/op
PASS
ok  	github.com/Zophyr/Speedometer	59.501s
```

### Part 2. 总结

不出我们的意料，Gin 完胜 GorillaMux。


