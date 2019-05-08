---
title: git-flow 实战教程
date: 2019-05-02 22:24:01
description: 介绍 git-flow 实战方式
categories: 教程
tags: [环境配置, 教程, Git, git-flow]
toc: true
---

# git-flow 实战教程

## 什么是 git-flow

git-flow 是一个 git 的拓展集，它是按 Vincent Driessen 的分支模型提供高层次的库操作，以实现一种工作流。

## flow

### master

***生产环境分支。***

- 每一个正式版本合并到该分支，且打 TAG，该分支代码可靠可用。

- 合并关系：仅接受 release / hotfix 分支的合并请求。

### develop

***开发分支。***

- 开发的代码统一归到该分支，用于日常开发。

- 合并关系：接受 feature / release / hotfix 分支的合并请求。

### feature

***功能分支。***

- 从 develop 分支拉取，开发新功能。当功能开发完毕，合并回 develop 分支。

- 合并关系：不接受任何分支的合并。从 develop 分支分出来，进行新功能的开发完成后，合并会 develop 分支。

### release

***发布分支。***

- 表示发布一个正式的版本。

- 合并关系：不接受任何分支的合并。从 develop 分支分出来，完成操作后，合并回 master / develop 分支，且打对应的 TAG。

### hotfix

***补丁分支。***

- 修复版本线上bug。

- 合并关系：不接受任何分支的合并。从 master 分支分出来，进行 bug 修复，修复完成合并回 master / develop 分支。

## flow 实践例子

下面我们将会通过一个具体的例子来进行 git-flow 工作流的演示。

假设我们现在有一个个人页面的开发任务，将其分为个人信息块，功能块，其他块三个部分。

### 1. 初始化仓库

1. 在 **master** 分支上进行代码初始化。

2. 从 **master** 中切换出 **develop** 分支。

### 2. 开发功能

1. 在 **develop** 分支中，切换出 `feature/个人信息块` 分支。

2. 在 `feature/个人信息块` 分支中，进行个人信息块相关的功能开发。

### 3. 合并新功能

1. 当 `feature/个人信息块` 上的代码开发完毕，我们将该分支合并到 **develop** 分支中。

2. 此时，**develop** 分支有了个人信息块该功能。

###  4. 后续其他功能开发

1. 仍然从 **develop** 分支中，切换出 `feature/xxxx功能` 分支。

2. 并在 `feature/xxxx功能` 分支中，进行相关功能的开发。

### 5. 版本迭代

1. 当我们三个板块都合并完毕，且都合并到 **develop** 分之后，我们准备发版。

2. 从 **develop** 分支中，切换出 `release/1.0.0` 分支。

3. 部署相关代码，并进行相关测试，如果出现了问题，则在该分支上进行更新修改。

4. QA 觉得 OK 且代码 review 也OK了，这时候就可以给该分支打标签 `1.0.0` ，同时合并回 **master** 和 **develop** 分支。

### 6. 紧急修复

假如我们现在个人页面出现了问题，比如头像应该是圆形，却变成了正方形。

1. 从 **master** 分支开辟 `hotfix/1.0.1` 分支。

2. 在 `hotfix/1.0.1` 分支上进行修复。

3. 修复完成后，将 `hotfix/1.0.1` 分支，合并回 **master** 和 **develop** 分支。

## 参考资料

- [git-flow with SourceTree](https://www.jianshu.com/p/8a3988057d0f)

- [git-flow 备忘清单](https://danielkummer.github.io/git-flow-cheatsheet/index.zh_CN.html)

- [git commit 模板](https://gist.github.com/jmaxhu/8e7fb69a7dcec1b9b953)

- [git-flow 工作流](https://www.git-tower.com/learn/git/ebook/cn/command-line/advanced-topics/git-flow)