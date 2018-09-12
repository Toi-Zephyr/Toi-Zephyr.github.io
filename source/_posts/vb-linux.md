---
title: 使用 VirtualBox 让 PC 提供云桌面服务
date: 2019-09-12 21:35:28
description: 用户将可以通过互联网，使用微软远程桌面，远程访问在PC机上创建的虚拟机。
categories: Services Computing
tags: [Services Computing]
toc: true
---

## 当前环境

1. Windows 10 （v.1083）
2. VirtualBox （v.5.2.16）
3. CentOS 7 (x86_64 Minimal 1804)

## 相关准备

1. VirtualBox （[官网](https://www.virtualbox.org/) | [下载地址](https://www.virtualbox.org/wiki/Downloads)）
2. VirtualBox Extension Pack （[下载链接（v5.2.18）](https://download.virtualbox.org/virtualbox/5.2.18/Oracle_VM_VirtualBox_Extension_Pack-5.2.18.vbox-extpack)）
3. CentOS （[官网](https://www.centos.org/) | [下载地址](https://www.centos.org/download/)）

## 开始实验

### 安装&配置 VirtualBox

#### 安装

下载完VirtualBox的安装包后，直接安装即可。

#### 配置

1. 修改配置VirtualBox 虚拟机存储位置
   - 打开VirtualBox
   - 点击左上角菜单栏中的 【管理】--> 【全局设定】
   - 把【常规】中的【默认虚拟电脑位置】修改为一个充足空间的路径
   - ![iApoLj.png](https://s1.ax1x.com/2018/09/12/iApoLj.png)

1. 创建虚拟网卡

   - 点击左上角菜单栏中的 【管理】--> 【主机网络管理器】
   - 点击【创建】按钮，便会创建一块名为【VirtualBox Host-Only Ethernet Adapter #2】的网卡
   - 我们将会有两块网卡
     - 【VirtualBox Host-Only Ethernet Adapter】
     - 【VirtualBox Host-Only Ethernet Adapter #2】
   - 手动配置第二块网卡的IPv4地址—— 192.169.100.1/24
   - ![iA9FFx.png](https://s1.ax1x.com/2018/09/12/iA9FFx.png)
   - 测试虚拟网卡是否创建成功
     1. 在`cmd`中输入`ipconfig`
     2. 可以查找到类似于下图的结果
     3. ![iA91tP.png](https://s1.ax1x.com/2018/09/12/iA91tP.png)

### 安装 CentOS 7

   1. 创建新的虚拟机
        - 点击界面左上角【新建】，输入虚拟机名称，安装的系统为CentOS则版本选择Red Hat
        - ![iA9spT.png](https://s1.ax1x.com/2018/09/12/iA9spT.png)
        - 配置内存大小，建议2G及以上
        - ![iA90kq.png](https://s1.ax1x.com/2018/09/12/iA90kq.png)
        - 创建虚拟硬盘
        - ![iA9d7n.png](https://s1.ax1x.com/2018/09/12/iA9d7n.png)
        - 选择VDI
        - ![iA9a0s.png](https://s1.ax1x.com/2018/09/12/iA9a0s.png)
        - 选择动态分配
        - ![iA9y1U.png](https://s1.ax1x.com/2018/09/12/iA9y1U.png)
        - 配置虚拟磁盘大小，建议30G以上且为2的倍数
        - ![iA9Bt0.png](https://s1.ax1x.com/2018/09/12/iA9Bt0.png)
        - 创建成功！
        - ![iA9DhV.png](https://s1.ax1x.com/2018/09/12/iA9DhV.png)
    2. 配置虚拟机相关设置
        - 在创建好的虚拟机上面【右键】--> 【设置】
        - 点击【网络】 --> 【网卡2】
        - 点击【启用网络连接】--> 【连接方式】选择 **仅主机（Host-Only）网络** --> 【界面名称】选择 **VirtualBox Host-Only Ethernet Adapter #2**
        - ![iA9qnH.png](https://s1.ax1x.com/2018/09/12/iA9qnH.png)
    3. 启动虚拟机，安装CentOS
        - 双击我们的虚拟机，第一次启动会提醒我们选择启动盘，我们需要选择之前我们下载的镜像iso文件。选择好点击【启动】即可。
        - ![iACiuQ.png](https://s1.ax1x.com/2018/09/12/iACiuQ.png)
        - 之后出现安装界面，选择 Install CentOS 7
        - ![iACVNq.png](https://s1.ax1x.com/2018/09/12/iACVNq.png)
        - 接下来一堆字符闪过，我们等待语言界面出现即可。此时点击虚拟机界面鼠标将会被锁定在虚拟机介面内，脱离锁定需要点击【右ctrl】按钮
        - ![iAPVqH.png](https://s1.ax1x.com/2018/09/12/iAPVqH.png)
        - 选择语言，进入安装信息摘要界面
        - ![iAPEse.png](https://s1.ax1x.com/2018/09/12/iAPEse.png)
        - 选择【网络和主机名】
        - 点击【以太网（enp0s3）】右边按钮【打开】
        - ![iAPFxO.png](https://s1.ax1x.com/2018/09/12/iAPFxO.png)
        - 点击【以太网（enp0s8）】右边按钮【打开】并点击右下角【配置】，选 IPv4 设置，【方法】设置为手动，并输入刚刚的地址。
        - ![iAPAMD.png](https://s1.ax1x.com/2018/09/12/iAPAMD.png)
        - 点击开始安装。等待安装完成。
        - ![iAP8sg.png](https://s1.ax1x.com/2018/09/12/iAP8sg.png)
    
### 配置 CentOS 7

1. 安装 【wget】
    - `yum install wget`
2. 更新系统
    - `yum update`
3. 配置网络环境
    - `nmtui`
    - ![iAPvSf.png](https://s1.ax1x.com/2018/09/12/iAPvSf.png)
    - 选择 【Edit a connection】，选择之后选择右边的Edit，将网卡设置为【Automatically connect】
    - ![iAPz6S.png](https://s1.ax1x.com/2018/09/12/iAPz6S.png)
    - 选择 【Active a connection】，将网卡Active
    - ![iAPxl8.png](https://s1.ax1x.com/2018/09/12/iAPxl8.png)
4. 测试网络环境
    - 下载网络连接查看工具
    - `yum install net-tools`
    - `ifconfig`
    - `ping baidu.com`
    - 在自己的Windows上，ping创建的虚拟机
    - `ping 192.168.100.3`

### 远程连接虚拟机

#### SSH连接

    - `ssh root@192.168.100.3`
    - ![iAiGp6.png](https://s1.ax1x.com/2018/09/12/iAiGp6.png)

#### Windows 远程桌面连接

1. 安装拓展包 `VirtualBox Extension Pack`
    - 关闭所有虚拟机
    - 点击左上角菜单栏中的 【管理】--> 【全局设定】
    - 点击【扩展】
    - 点击右边绿色加号，选择下载的拓展包
    - 点击ok 完成安装
    - ![iAid7d.png](https://s1.ax1x.com/2018/09/12/iAid7d.png)
2. 设置虚拟机远程桌面
    - 在创建好的虚拟机上面【右键】--> 【设置】
    - 点击【显示】 --> 【远程桌面】
    - 点击【启用服务器】 --> 配置【服务器端口号】为5001
    - ![iAiW7j.png](https://s1.ax1x.com/2018/09/12/iAiW7j.png)
3. 测试
    - 打开【远程桌面连接】
    - 输入Windows IP地址 + : + 端口号
    - ![iAi4Nn.png](https://s1.ax1x.com/2018/09/12/iAi4Nn.png)
    - 连接成功
    - ![iAi5hq.png](https://s1.ax1x.com/2018/09/12/iAi5hq.png)

### 复制新的虚拟机

1. 在创建好的虚拟机上面【右键】--> 【复制】
2. 选择【重新初始化所有网卡的 MAC 地址】
    - ![iAix41.png](https://s1.ax1x.com/2018/09/12/iAix41.png)
3. 选择链接复制
    - ![iAivNR.png](https://s1.ax1x.com/2018/09/12/iAivNR.png)

## Q&A

Q：网卡一和网卡二是咋回事？

A：
1. 在网卡1，把网络连接选为NAT（网络地址转换模式），NAT就是当局域网内部节点（即我们的虚拟机）要与外部网络进行通讯时，将内部地址替换成公用地址，从而在外部公网（internet）上正常使用。
2. 在网卡二，启用网路连接，将连接方式改为Host-Only，Host-Only模式了一个本机与各个虚拟机的局域网，通过这张网卡，虚拟机不可以上外网，但是虚拟机之间、虚拟机和主机之间可以ping通
3. [来源](https://blog.csdn.net/qiuxy23/article/details/82532703/)








