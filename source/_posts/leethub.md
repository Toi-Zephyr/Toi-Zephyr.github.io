---
title: 将 LeetCode 代码同步到 GitHub
date: 2019-02-27 22:19:33
description: 三分钟：让 GitHub 与 LeetCode 在一起
categories: 教程
tags: [LeetCode, 教程, Git]
toc: true
---

内容基于 [bonfy](https://github.com/bonfy) 的代码自动下载生成器。由于 bonfy 将生成器与自己的代码混合在一起使用并不是很方便，便将其单独提取出来。

## 前期准备

### 文件下载

下载 [LeetHub](https://github.com/Zophyr/LeetHub) 中的以下三个文件，放到自己用于同步 LeetCode 代码的 GitHub 仓库中。

```
.
├── config.cfg
├── leetcode_generate.py
└── req.txt
```

### 配置环境

1. 下载 `Chromedriver`
    
    ```
    $ brew cask install chromedriver
    ```

2. 下载 `python` 支持

    ```
    $ pip3 install -r req.txt
    ```

### LeetCode 配置

修改 `config.cfg` 文件

```
username = your_username
password = yout_password
language = python,javascript
repo = https://github.com/bonfy/leetcode
driverpath = /usr/local/bin/chromedriver
```

- `username` 与 `password` 为 LeetCode 的账号密码

- `language` 为想要同步 LeetCode 上对应语言的题目

- `repo` 为同步 LeetCode 代码的 GitHub 仓库中

- `driverpath` 为 `chromedriver` 的路径

### 运行

```python
python3 leetcode_generate.py
```

将会在当前目录下生成 README 与存放代码的`solutions`文件夹。

### ⚠️ 提示

注意将 `config.cfg` 文件添加到 `.gitignore` 中，防止账号密码泄露。


