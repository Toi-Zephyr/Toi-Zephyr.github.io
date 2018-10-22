---
title: Hello Python!
date: 2018-03-16 23:33:22
description: Python 环境配置 —— python 2 & python 3 和谐共处
categories: python
tags: [python]
toc: true
---

# Hello Python！

> 借用虚拟环境，在电脑上同时配置python2与python3

## 配置环境

1. macOS 10.13.6
2. 系统自带 python 2.7.10
3. Xcode 10.0

## 前期配置

1. 安装 「Homebrew」
    ```shell
    $ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```
2. 升级 「Homebrew」
    ```shell
    $ brew update
    ```

## 开始搭建

1. 安装python2
    ```shell
    $ brew install python2
    ```
2. 安装python3
    ```shell
    $ brew install python3
    ```
3. 创建配置虚拟环境
    1. 创建虚拟目录，保存虚拟环境
        ```shell
        # 在用户目录建立 python/
        $ mkdir ~/python

        # 在 python/ 文件中建立 python2/ 与 python3/
        $ mkdir ~/python/python2
        $ mkdir ~/python/python3
        ```
    2. 创建虚拟环境
        ```shell
        # 创建 python2 的虚拟环境
        $ cd ~/python/python2
        $ sudo pip2 install virtualenv
        $ virtualenv venv_py2   # venv_py2 可设置为自己喜欢的名字
        ```
        ```shell
        # 创建 python3 的虚拟环境
        $ cd ~/python/python3
        $ sudo pip3 install virtualenv
        $ virtualenv venv_py3   # venv_py3 可设置为自己喜欢的名字
        ```
    3. 开启虚拟环境
        ```shell
        # 启动 python2 虚拟环境
        $ . ~/python/python2/venv_py2/bin/activate   
        ```
        ```shell
        # 启动 python3 虚拟环境
        $ . ~/python/python3/venv_py3/bin/activate   
        ```
    4. 关闭虚拟环境
        ```shell
        # 关闭虚拟环境
        $ deactivate 
        ```
    5. 配置虚拟环境启动关闭环境快捷命令
        ```shell
        # 打开系统配置文件
        $ vim ~/.bash_profile
        ```
        ```shell
        # 在 .bash_profile 最后添加下述代码

        # Use Python 2 virtual env
        alias usepy2=". ~/python/python2/venv_py2/bin/activate"

        # Use Python 3 virtual env
        alias usepy3=". ~/python/python3/venv_py3/bin/activate"

        # Exit python virtual env
        alias byepy="deactivate"
        ```
        ```shell
        # 保存配置环境后 激活环境配置
        $ source ~/.bash_profile
        ```
4. 禁止在虚拟环境外使用 `pip` 命令
    ```shell
    # 打开系统配置文件
    $ vim ~/.bash_profile
    ```
    ```shell
    # 在 .bash_profile 最后添加下述代码
    export PIP_REQUIRE_VIRTUALENV=true
    ```
    ```shell
    # 保存配置环境后 激活环境配置
    $ source ~/.bash_profile
    ```
