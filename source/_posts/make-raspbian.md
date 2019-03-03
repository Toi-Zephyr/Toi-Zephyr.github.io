---
title: 制作64位的树莓派
date: 2019-03-03 19:37:59
description: 为树莓派安装64位的 Ubuntu 系统
categories: 教程
tags: [教程, 命令行, 树莓派, Ubuntu]
toc: true
---

## 前期准备

### 下载64位系统

树莓派官方系统并未支持64位，所以我们使用民间制作的64位树莓派系统。

[ubuntu64-rpi](https://github.com/chainsx/ubuntu64-rpi)

### 格式化 SD 卡

将 SD 卡以 `MS-DOS(FAT)` 的格式格式化。

![sd.png](https://s2.ax1x.com/2019/03/03/kLT2Wt.png)

## 制作 SD 卡

### 1. 查看当前已挂载的卷

```shell
$ df -h
```

出现类似于下述输出

```shell
$ df -h
Filesystem      Size   Used  Avail Capacity iused               ifree %iused  Mounted on
/dev/disk1s1   234Gi  181Gi   49Gi    79% 2056580 9223372036852719227    0%   /
devfs          189Ki  189Ki    0Bi   100%     655                   0  100%   /dev
/dev/disk1s4   234Gi  3.0Gi   49Gi     6%       5 9223372036854775802    0%   /private/var/vm
map -hosts       0Bi    0Bi    0Bi   100%       0                   0  100%   /net
map auto_home    0Bi    0Bi    0Bi   100%       0                   0  100%   /home
/dev/disk2s1    30Gi  1.5Mi   30Gi     1%       0                   0  100%   /Volumes/TESTER2
```

找到我们的 SD 卡 **TESTER2** - `disk2s1`

### 2. 卸载分区

卸载 **TESTER2** - `disk2s1`

```shell
$ diskutil unmount /dev/disk2s1 
Volume TESTER2 on disk2s1 unmounted
```

确认设备

```shell
$ diskutil list
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *251.0 GB   disk0
   1:                        EFI EFI                     209.7 MB   disk0s1
   2:                 Apple_APFS Container disk1         250.8 GB   disk0s2

/dev/disk1 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +250.8 GB   disk1
                                 Physical Store disk0s2
   1:                APFS Volume Macintosh HD            194.5 GB   disk1s1
   2:                APFS Volume Preboot                 23.2 MB    disk1s2
   3:                APFS Volume Recovery                514.9 MB   disk1s3
   4:                APFS Volume VM                      3.2 GB     disk1s4

/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *31.7 GB    disk2
   1:                 DOS_FAT_32 TESTER2                 31.7 GB    disk2s1
```

### 3. 刷入镜像

使用 `DD` 命令刷入镜像。

```shell
$ sudo dd bs=4m if=cxcore.img of=/dev/rdisk2

512+0 records in
512+0 records out
2147483648 bytes transferred in 191.747330 secs (11199549 bytes/sec)
```

⚠️ 注意：最后面磁盘名字是 **rdisk2**，要在本身的磁盘名前加上 `r`。

### 4. 卸载设备

```shell
$ diskutil unmountDisk /dev/disk2
Unmount of all volumes on disk2 was successful
```

## 完成！

⚠️ 注意：其中的 **disk2**, **disk2s1**, **rdisk2** 要注意加以区分。