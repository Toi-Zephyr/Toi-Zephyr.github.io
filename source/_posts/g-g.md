---
title: Go语言环境配置及Git配置总结
date: 2018-09-26 08:49:15
description: Go & Git
categories: Go
tags: [Go]
toc: true
---

# Go语言环境配置及Git配置总结

## Go语言环境配置

### 当前环境

1. Windows 10 （v.1083）
2. VirtualBox （v.5.2.16）
3. CentOS 7 (x86_64 Minimal 1804)

### 安装 golang

1.安装

```shell
$ sudo yum install golang
```

2.验证时候安装成功

```shell
$ go version
```

### 配置环境变量（工作空间）

1.创建工作空间

```shell
$ mkdir $HOME/gowork
```

**Q&A时间！**

*什么是工作空间？*

> 工作空间其实就是一个目录，其中包含三个子目录：

> - src 目录包含Go的源文件，它们被组织成包（每个目录都对应一个包）
> - pkg 目录包含包对象
> - bin 目录包含可执行命令

> go 工具用于构建源码包，并将其生成的二进制文件安装到 pkg 和 bin 目录中。

2.编辑环境变量

1⃣️ 打开`/etc/profile`文件

```shell
$ sudo vim /etc/profile
```

2⃣️ 添加文件路径，将下述代码添加到文件的末尾

```shell
export GOPATH=$HOME/gowork
export PATH=$PATH:$GOAPTH/bin
```

3⃣️ 使文件内容生效

```shell
source $HOME/.profile
```

### 编写Go程序

1.创建包目录，创建在工作空间中

```shell
$ mkdir $GOPATH/src/github.com/user/hello-world
```

2.编写传统有趣、学习语言都要写的第一个文件 `hello-world.go`

```go
package main

import "fmt"

func main() {
	fmt.Printf("Hello, World!\n")
}
```

3.构建并安装该程序

```shell
$ go install github.com/user/hello-world
```

4.运行程序

```shell
$ $GOPATH/bin/hello-world
```

或者已经将 $GOPATH/bin 添加到 PATH 中了，可以使用

```shell
hello-world
```

5.结果

**Hello, World!**

## Git配置总结

### Git安装

1.官网下载或命令行操作

```shell
$ sudo yum install git
```

2.设置用户名与邮箱

```shell
$ git config --global user.name "Toi-Zephyr"
$ git config --global user.email "zhangjliang@mail2.sysu.edu.cn"
```

3.查看配置

```shell
$ git config --list
```

### Git相关操作命令

1.关于查看／修改git 用户名与邮箱地址
> 查看：

```shell
$ git config user.name
$ git config user.email
```

> 修改：

```shell
$ git config –global user.name “your username”
$ git config –global user.email “your email”
```

2.创建Git版本库（Repository）（在当前目录下创建仓库）

```shell
$ git init
```

3.查看仓库状态

```shell
$ git status
```

4.添加文件 readme.txt 到Git版本库

```shell  
$ git add readme.txt
（相当于把文件添加到购物车）

$ git commit -m “you can write something about your add”
（相当于结账）
```

5.将文件 readme.txt 从Git版本库删除

```shell    
$ git rm readme.txt
$ git commit -m “YCWSAYO”
```

6.比较文件差别

```shell
$ git diff
```

7.查看Git所有提交过的版本信息

```shell
$ git log –pretty=online
```

8.查看所有HEAD历史

```shell
$ git reflog
（可查看所有的commit ID）
```

9.返回上一个版本

```shell
$ git reset –hard HEAD^
```

10.返回第N个版本

```shell
$ git reset –hard HEAD~N
```

11.返回某个版本（知道commitID）

```shell
$ git reset –hard commitID
```

12.添加GitHub远程仓库

```shell
$ git remote add Name git@github.com:GHID/RepositoryName.git
（添加名为remoteName的远程仓库）
```

13.删除远程仓库

```shell
$ git remote rm remoteName
```

14.查看当前项目的远程库

```shell
$ git remote
```

15.推送本地库内容到远程库

```shell
$ git push -u origin master
（推送分支master到远程仓库origin （-u表示第一次建立本地和远程库的关系））
```

16.从远程库拉去内容到本地

```shell
$ git clone git@github.com:/GitHubID/RepositoryName.git
```

17.从远程库更新本地内容

```shell
$ git pull
```

18.创建新的分支 dev

```shell
$ git branch dev
```

19.删除dev分支

```shell
$ git branch -d dev
```

20.切换到dev分支

```shell
$ git checkout dev
```

21.查看分支

```shell
$ git branch
```

22.合并dev分支到当前master分支

```shell
$ git merge dev
```

23.合并dev分支，不使用快速合并方式 并且创建个commit提交

```shell
$ git merge –no-ff -m “your commit” dev
```

24.查看所有标签

```shell
$ git tag
```

25.添加标签到某次commit

```shell
$ git tag -a yourTagName -m “say something or not” commitId
```

26.查看某次标签

```shell
$ git show tagName
```


