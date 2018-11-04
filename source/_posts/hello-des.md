---
title: DES的设计与实现
date: 2018-11-02 09:33:05
description: 学习实现一种常见的加密算法，感受密码学的趣味。
categories: 教程
tags: [教程, hello-world, DES, 算法]
toc: true
---
# DES算法设计及实现

## 算法设计

DES加密算法的核心流程，我们可以理解为五个步骤：

1. **明文输入**
2. **IP置换**
3. **T迭代**
4. **IP逆置换**
5. **密文输出**

其中，最为复杂的为**T迭代**，T迭代中包括：

- 16次循环迭代
- 上下半区交换
- 密匙调度压缩
- PC1、PC2压缩交换
- 循环位移
- Feistel 轮函数

因此，我们的算法设计将分为四个部分

1. 明文输入输出
2. IP置换
3. T迭代
4. IP逆置换

即目录文件结构为：

```shell
.
├── DES.h   # DES 算法
├── IPIP.h  # IP逆置换
├── IPP.h   # IP置换
├── TI.h    # T迭代
├── main.cpp    # 文件输入输出及测试
└── util.h  # 置换表以及相关运算函数
```

### 前期准备 —— `util.h`

在 `util.h` 中，我们含有以下置换表：

| 表名 | 变量名 | 类型 |
| ------ | ------ | ------ |
| IP 置换表 | IP_Init_Table[64] | int |
| IP 逆置换表 | IPR_Table[64] | int |
| E 扩展表 | E_Table[48] | int |
| P 盒 | P_Table[32] | int |
| PC1 压缩置换表 | PC1_Table[56] | int |
| PC2 压缩置换表 | PC2_Table[48] | int |
| S 盒 | S_Box[8][4][16] | int |

在 `util.h` 中，我们含有以下工具函数：

- 字符转二进制: `void CharToBit(char input[], int output[])`
- 二进制转字符: `void BitToChar(int intput[], char output[])`
- 异或操作: `void Xor(int *INA, int *INB, int len)`

### IP置换部分 —— `IPP.h`

`void IP_Init_Rep(int input[64], int output[64], int table[64])`

该函数根据表（table）中的规定，将输入的 64 位明文重新进行排序，即将第 58 位放到第 1 位，第 50 位放到第 2 位……以此类推。初始置换以后得到的是一个 64 位的输出。

### T迭代部分 —— `TI.h`

#### PC1压缩置换

> `void PC_1(int input[64], int output[56], int table[56])`

用户输出的密钥是 64 位的，根据密钥置换表PC-1，将 64 位变成 56 位密钥。（去掉了奇偶校验位）

#### 循环位移

> `void RotateL(int input[28], int output[28], int leftCount)`

将 PC-1 置换得到的 56 位密钥，分为前28位 C0 和后28位 D0，分别对它们进行循环左移，C0左移得到 C1，D0 左移得到 D1。

#### PC2压缩置换

> `void PC_2(int input[56], int output[48], int table[48])`

将 C1 和 D1 合并成 56 位，然后通过PC-2表进行压缩置换，得到当前这一轮的 48 位子密钥 K1 。

#### 子密匙循环

> `void subKey_fun(int input[64], int subKey[16][48])`

然后对 C1 和 D1 进行左移和压缩置换，获取下一轮的子密钥……一共进行16轮，得到 16 个 48 位的子密钥。

#### 密码函数

> `void F_func(int input[32], int output[32], int subKey[48])`

密码函数f(R, K)接受两个输入：32 位的数据和 48 位的子密钥。

#### E置换

> `void E_Extend(int input[32], int output[48], int table[48])`

通过表 E 进行扩展置换，将输入的 32 位数据扩展为 48 位；

#### s-box分组

> `void S_Comp(int input[48], int output[32], int table[8][4][16])`

根据S盒进行置换

#### P置换

> `void P_Rep(int input[32], int output[32], int table[32])`

把通过 S 表置换得到的 8 个 4 位连在一起，形成一个 32 位的数据。然后将该 32 位数据通过表 P 进行置换（称为P-置换），置换后得到一个仍然是 32 位的结果数据，这就是f(R, K)函数的输出

### IP逆置换部分 —— `IPIP.h`

> `void IP_Inv_Rep(int input[64], int output[64], int table[64])`

合并 L16 和 R16 得到一个 64 位的数据，再经过尾置换后得到的就是 64 位的密文。注意：要将 L16和 R16 合并成 R16L16（即左右互换）。

### DES算法部分 —— `DES.h`

在该部分进行上述部分的整合，并进行循环，可以达到解密和加密的效果。

## 彩蛋

```shell
0010 0110
1001 1111
0000 1011
0010 1010
1001 0100
0101 1100
1100 1110
1110 0010
```
