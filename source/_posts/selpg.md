---
title: 基于 Go 开发 CLI 命令行程序
date: 2018-10-03 04:22:56
description: 使用 go 开发 selpg 命令。
categories: Go
tags: [go]
toc: true
---

# 使用 go 开发 selpg 命令

## 前期准备

1. 安装 pflag

```shell
$ go get github.com/spf13/pflag
```

2. 测试是否安装成功

```shell
$ $ go test github.com/spf13/pflag
```

## 编码开始

1. 引入相关包

```go
import (
	"bufio"
	"fmt"
	"io"
	"os"
	"os/exec"

	flag "github.com/spf13/pflag"
)
```

2. 定义数据结构

```go
type Selpg struct {
	startPage, endPage, pageLines, totalPages int
	destination, inputFile string
	fromFeed bool
}
```

3. 判断文件是否为空

```go
	if sp.inputFile != "" {
		file, err := os.Open(sp.inputFile)
		if err != nil {
			fmt.Printf("%v: error: %v, %v\n", os.Args[0], sp.inputFile, err)
			os.Exit(1)
		}
		reader = file
	} 
	else {
		reader = os.Stdin
	}
	bufReader := bufio.NewReader(reader)
	bufWriter := bufio.NewWriter(writer)
```

4. 文件分页

```go
currentPage := 1
	for runningFlag := true; runningFlag ;{
		ch, err := bufReader.ReadByte()
		if err != nil {
			if err == io.EOF {
				runningFlag = false
			} 
			else {
				log.Fatalf("Error: %v\n", err)
				os.Exit(1)
			}
		}
		if currentPage >= sp.startPage && currentPage <= sp.endPage {
			bufWriter.WriteByte(ch)
		}
		if ch == '\f' {
			bufWriter.Flush()
			currentPage += 1
		}
	}
	totalPages = currentPage
```

## 测试

### Case 1

```sh
# test1.sh

for ((i=1;i<20;i++))
do
    echo "line" $i >> test
done
```

#### Input

```shell
$ selpg --s 1 --e 1 test1
```

#### Output

```shell
line 1
line 2
line 3
line 4
line 5
line 6
line 7
line 8
line 9
line 10
line 11
line 12
line 13
line 14
line 15
line 16
line 17
line 18
line 19
```

### Case 2

```sh
# test2.sh

for ((i=1;i<20;i += 4))
do
    echo "line" $i >> testpage
    echo "line" $i+1 >> testpage
    echo "line" $i+2 >> testpage
    echo "line" $i+3 >> testpage
    echo -e '\f' >> testpage
done
```

#### Input

```shell
$ selpg --s 1 --e 3 test2
```

#### Output

```shell
line 1
line 1+1
line 1+2
line 1+3

line 5
line 5+1
line 5+2
line 5+3

line 9
line 9+1
line 9+2
line 9+3

line 13
line 13+1
line 13+2
line 13+3

line 17
line 17+1
line 17+2
line 17+3
```