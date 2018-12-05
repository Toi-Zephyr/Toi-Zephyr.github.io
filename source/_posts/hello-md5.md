---
title: MD5 算法的设计与实现
date: 2018-11-02 09:33:05
description: 学习实现一种常见的加密算法，感受密码学的趣味。
categories: 教程
tags: [教程, hello-world, MD5, 算法, 加密]
toc: true
---

# 实现一个 MD5 算法

## 算法原理概述

### 什么是 MD5 ？

MD5 算法是一种消息摘要算法。
特点：

1. 定长性。输入任意长度的信息，经过处理，输出为128位的信息。
2. 唯一性。不同的输入得到的不同的结果。
3. 不可逆。根据128位的输出结果不可能反推出输入的信息。

### 实现原理

**MD5 算法可以简单概括成4步。**

1. **信息填充。**MD5 对于处理信息的字节长度有要求——输入信息的长度(bit)对512求余的结果等于448——所以我们要先对输入的数据进行处理，使其长度符合算法要求。
2. **记录信息长度。**MD5 中会用64位来存储信息长度。这样加上第一步中的长度。总的数据长度就为 `N x 512` 为512的倍数。
3. **四轮循环运算。**这是 MD5 算法的核心。我们直接根据算法定义实现就好，可以简单理解为我们进行了一系列的非线性函数运算，计算出了MD5中四个32位的称作链接变量（Chaining Variable）的整数参数的新的值。
4. **合并链接变量。**我们计算出新的四个链接变量值后，我们按照逆序，将他们转化成16进制的数然后四个合在一起。最终，我们将得到的结果输出。

## 数据结构

### 数据定义

```C++
/**
 * byte 字节
 * 一个字符(char) 即 一个字节(byte) 8位
 */
typedef unsigned char byte;
```
```C++
/**
 * bit 比特
 * 无符号整数 即 32位
 */
typedef unsigned int bit32;
```

### 公有函数

```C++
/** 
 * 构造函数，用于初始化 MD5 算法。
 */
MD5(const string &message);
```



```C++
/** 
 * 获得经过 MD5 算法之后的结果。
 * 在此处完成合并链接变量。
 */
string getResult();
```

### 私有函数

```C++
/** 
 * 初始化函数
 * 进行 Step 1.信息填充
 * 同时也会进行 Step 2.记录信息长度
 */
void _init(const string input);
```

```C++
/** 
 * 循环分组函数
 * 为步骤3做准备
 * 将每一512字节细分成16个小组，每个小组64位以此来进行循环运算。
 */
void _loop();
```

```C++
/** 
 * MD5 主变换循环
 * 进行 Step 3.四轮循环运算
 */
void _transform(const bit32 block[]);
```

```C++
/** 
 * 转化函数
 * 按照逆序，将得到的四个链接变量转化成16进制
 * 进行 Step 4.合并链接变量的前一步。
 */
string _toHex(const bit32 number);
```

### 私有变量

```C++
/** 
 * MD5 四个标准幻数 A, B, C, D
 */
bit32 _state[4];
```

```C++
/** 
 * 处理之后数据
 */
bit32 *_strByte;
```

```C++
/** 
 * 数据长度
 */
bit32 _strLength;
```

```C++
/** 
 * 结果
 */
string _result;
```

## 算法实现

### 前期准备

#### 定义标准幻数

标准的幻数（物理顺序）是

- A=(01234567)16 
- B=(89ABCDEF)16 
- C=(FEDCBA98)16 
- D=(76543210)16 

如果在程序中定义应该是

- A=0X67452301
- B=0XEFCDAB89
- C=0X98BADCFE
- D=0X10325476

```C++
/**
 * MD5 四个标准幻数 A, B, C, D
 * 由算法定义
 */
#define A 0x67452301
#define B 0xefcdab89
#define C 0x98badcfe
#define D 0x10325476
```

#### 定义变换辅助函数

以下是每次操作中用到的四个非线性函数：

```C++
/**
 * 定义 MD5 变换辅助函数
 * 由 MD5 算法本身定义
 */
#define F(x, y, z) (((x) & (y)) | ((~x) & (z)))
#define G(x, y, z) (((x) & (z)) | ((y) & (~z)))
#define H(x, y, z) ((x) ^ (y) ^ (z))
#define I(x, y, z) ((y) ^ ((x) | (~z)))
```

#### 定义四种基本变换

以下是 MD5 算法中的四种基本变换

```C++
/**
 * MD5 四种基本变换
 * 由 MD5 算法本身定义
 */
#define FF(a, b, c, d, x, s, ac) { \
    (a) += F ((b), (c), (d)) + (x) + ac; \
    (a) = ROTATELEFT ((a), (s)); \
    (a) += (b); \
}

#define GG(a, b, c, d, x, s, ac) { \
    (a) += G ((b), (c), (d)) + (x) + ac; \
    (a) = ROTATELEFT ((a), (s)); \
    (a) += (b); \
}

#define HH(a, b, c, d, x, s, ac) { \
    (a) += H ((b), (c), (d)) + (x) + ac; \
    (a) = ROTATELEFT ((a), (s)); \
    (a) += (b); \
}

#define II(a, b, c, d, x, s, ac) { \
    (a) += I ((b), (c), (d)) + (x) + ac; \
    (a) = ROTATELEFT ((a), (s)); \
    (a) += (b); \
}
```

#### 构造函数

```C++
MD5::MD5(const string &message) {
    // 初始化标准幻数
    _state[0] = A;
    _state[1] = B;
    _state[2] = C;
    _state[3] = D;
    
    // 初始化私有变量
    _result = "";
    _strByte = NULL;
    _strLength = 0;
    
    // 调用填充函数
    _init(message);
    
    // 调用循环分组函数
    _loop();
}
```

### Step 1&2. 信息填充与记录信息长度

```C++
void MD5::_init(const string input) {
    bit32 blocks = (((bit32)input.length() + 8) / 64) + 1;
    
    _strByte = new bit32[blocks * 16];
    _strLength = blocks * 16;
    
    /* 初始化 byte 串 */
    for (int i = 0 ; i < _strLength; i++) {
        _strByte[i] = 0;
    }
    
    for (int i = 0; i < input.length(); i++) {
        _strByte[i >> 2] |= input[i] << ((i % 4) * 8);
    }
    
    _strByte[input.length() >> 2] |= (0x80 << ((input.length() % 4) * 8));
    
    _strByte[blocks * 16 - 2] = (bit32)input.size() * 8;
    
}
```

#### Step 1.1

我们首先需要对MD5进行分组。MD5 以512位来进行分组。我们输入的`input`是`char`也就是8位，所以每一个分组就会有 512 $\div$ 8 = 64个`char`字符。

又因为我们要在最后用64位来记录信息长度，所以也就是需要 64 $\div$ 8 = 8 个`char`字符。

所以，我们分块的时候，我们用 `input.length()` 得知输入多少个`char`字符，再加上固定的记录信息长度的8个字符，就是我们信息的总的长度。

然后我们就可以计算出我们需要有多少个分组。

👉 `bit32 blocks = (((bit32)input.length() + 8) / 64) + 1;`

#### Step 1.2

接着，我们计算 `_strByte` 与 `_strLength`。

一个 `unsigned int` 有32位，所以我们用16个`int`就可以表达 32 x 16 = 512 位，也就是一个分组。

所以我们需要的存取数据的位数，同时初始化所有位数为0。

👉 `_strByte = new bit32[blocks * 16];`

同时我们可以确认填充后的字符串的int的位数

👉 `_strLength = blocks * 16;`

#### Step 1.3

一个 `char` 8位，一个 `unsigned int` 32位，所以一个 `unsigned int` 可以存4个 `char` 。

我们将输入的字符，存进去。通过

👉 `_strByte[i >> 2] |= input[i] << ((i % 4) * 8);`

- 每个字符都有对应的 **ASCII** 码。

- `i >> 2` 代表的是 i / 4， 也就是找到字符所对应的 `_strByte` 位置。

- `(i % 4) * 8` 就是找到这个字符在 unsigned int 中所对应的字节。

- | 操作可以理解为求和 比如 3 | 4 = 7

举个例子，输入 abc

首先 i = 0, i >> 2 等于0 所以是 _strByte[0]。然后a对应的ASCII为97，((i % 4) * 8)等于0
所以整个式子为 _strByte[0] |= 97 << 0 即 97

接着 i = 1, 1 >> 2 等于0 所以是 _strByte[0]，因为一个 uint 可以存 4个char 所以要等到第五个字符输入的时候，也就是 5 >> 2 等于 1 才会移动到下一位来存储字符。后面((i % 4) * 8) 等于 8 。因为一个char8位，所以第二个字符存进来的时候要左移八位，存到它应该在的地方。
所以整个式子为 _strByte[0] |= 98 << 8 即 25088 | 97 等于 25158

以此类推，将输入存到 _strByte 中。


#### Step 1.4

填充 100000...
在字符都转化存入 _strByte 后我们要在剩下的位置第一个填充 1 其他填充 0
又因为我们初始化 _strByte 为全0，所以我们只用确定填充 1 在哪里就好了。

👉 `_strByte[input.length() >> 2] |= (0x80 << ((input.length() % 4) * 8));`

其中0x80是二进制的10000000，我们可以理解为在原来字符串的后面接上一个10000000
具体含义可以参考 Step 1.3 中的解释。

#### Step 1.5

记录字符长度。长度指位的长度，所以要乘8，同时MD5算法使用的是小端序，所以放在倒数第二个。

👉 `_strByte[blocks * 16 - 2] = (bit32)input.size() * 8;`

### Step 3. 四轮循环运算

#### 前期准备

分组函数。

```c++
void MD5::_loop() {
    for (int i = 0; i < _strLength / 16; i++) {
        bit32 group[16];
        for (int j = 0; j < 16; j++) {
            group[j] = _strByte[i * 16 + j];
        }
        _transform(group);
    }
}
```

1. 首先是按照512 bits来分组，即16个int
2. 然后每一分组被划分为16个32位int的子分组

之后就可以进行 _transform(group);

#### 主变换循环

MD5 算法定义

```c++
void MD5::_transform(const bit32 block[]) {
    bit32 a = _state[0], b = _state[1], c = _state[2], d = _state[3];
    
    /* Round 1 */
    FF (a, b, c, d, block[0], s11, 0xd76aa478);
    FF (d, a, b, c, block[1], s12, 0xe8c7b756);
    FF (c, d, a, b, block[2], s13, 0x242070db);
    FF (b, c, d, a, block[3], s14, 0xc1bdceee);
    FF (a, b, c, d, block[4], s11, 0xf57c0faf);
    FF (d, a, b, c, block[5], s12, 0x4787c62a);
    FF (c, d, a, b, block[6], s13, 0xa8304613);
    FF (b, c, d, a, block[7], s14, 0xfd469501);
    FF (a, b, c, d, block[8], s11, 0x698098d8);
    FF (d, a, b, c, block[9], s12, 0x8b44f7af);
    FF (c, d, a, b, block[10], s13, 0xffff5bb1);
    FF (b, c, d, a, block[11], s14, 0x895cd7be);
    FF (a, b, c, d, block[12], s11, 0x6b901122);
    FF (d, a, b, c, block[13], s12, 0xfd987193);
    FF (c, d, a, b, block[14], s13, 0xa679438e);
    FF (b, c, d, a, block[15], s14, 0x49b40821);
    
    /* Round 2 */
    GG (a, b, c, d, block[ 1], s21, 0xf61e2562);
    GG (d, a, b, c, block[ 6], s22, 0xc040b340);
    GG (c, d, a, b, block[11], s23, 0x265e5a51);
    GG (b, c, d, a, block[ 0], s24, 0xe9b6c7aa);
    GG (a, b, c, d, block[ 5], s21, 0xd62f105d);
    GG (d, a, b, c, block[10], s22,  0x2441453);
    GG (c, d, a, b, block[15], s23, 0xd8a1e681);
    GG (b, c, d, a, block[ 4], s24, 0xe7d3fbc8);
    GG (a, b, c, d, block[ 9], s21, 0x21e1cde6);
    GG (d, a, b, c, block[14], s22, 0xc33707d6);
    GG (c, d, a, b, block[ 3], s23, 0xf4d50d87);
    GG (b, c, d, a, block[ 8], s24, 0x455a14ed);
    GG (a, b, c, d, block[13], s21, 0xa9e3e905);
    GG (d, a, b, c, block[ 2], s22, 0xfcefa3f8);
    GG (c, d, a, b, block[ 7], s23, 0x676f02d9);
    GG (b, c, d, a, block[12], s24, 0x8d2a4c8a);
    
    /* Round 3 */
    HH (a, b, c, d, block[ 5], s31, 0xfffa3942);
    HH (d, a, b, c, block[ 8], s32, 0x8771f681);
    HH (c, d, a, b, block[11], s33, 0x6d9d6122);
    HH (b, c, d, a, block[14], s34, 0xfde5380c);
    HH (a, b, c, d, block[ 1], s31, 0xa4beea44);
    HH (d, a, b, c, block[ 4], s32, 0x4bdecfa9);
    HH (c, d, a, b, block[ 7], s33, 0xf6bb4b60);
    HH (b, c, d, a, block[10], s34, 0xbebfbc70);
    HH (a, b, c, d, block[13], s31, 0x289b7ec6);
    HH (d, a, b, c, block[ 0], s32, 0xeaa127fa);
    HH (c, d, a, b, block[ 3], s33, 0xd4ef3085);
    HH (b, c, d, a, block[ 6], s34,  0x4881d05);
    HH (a, b, c, d, block[ 9], s31, 0xd9d4d039);
    HH (d, a, b, c, block[12], s32, 0xe6db99e5);
    HH (c, d, a, b, block[15], s33, 0x1fa27cf8);
    HH (b, c, d, a, block[ 2], s34, 0xc4ac5665);
    
    /* Round 4 */
    II (a, b, c, d, block[ 0], s41, 0xf4292244);
    II (d, a, b, c, block[ 7], s42, 0x432aff97);
    II (c, d, a, b, block[14], s43, 0xab9423a7);
    II (b, c, d, a, block[ 5], s44, 0xfc93a039);
    II (a, b, c, d, block[12], s41, 0x655b59c3);
    II (d, a, b, c, block[ 3], s42, 0x8f0ccc92);
    II (c, d, a, b, block[10], s43, 0xffeff47d);
    II (b, c, d, a, block[ 1], s44, 0x85845dd1);
    II (a, b, c, d, block[ 8], s41, 0x6fa87e4f);
    II (d, a, b, c, block[15], s42, 0xfe2ce6e0);
    II (c, d, a, b, block[ 6], s43, 0xa3014314);
    II (b, c, d, a, block[13], s44, 0x4e0811a1);
    II (a, b, c, d, block[ 4], s41, 0xf7537e82);
    II (d, a, b, c, block[11], s42, 0xbd3af235);
    II (c, d, a, b, block[ 2], s43, 0x2ad7d2bb);
    II (b, c, d, a, block[ 9], s44, 0xeb86d391);
    
    _state[0] += a;
    _state[1] += b;
    _state[2] += c;
    _state[3] += d;
}
```

### Step 4. 合并链接变量

就是16进制的转换，比较简单。

不过要注意的是，MD5 是小端算法，所以我们最后得到的结果要倒过来。

```c++
string MD5::_toHex(const bit32 number) {
    stringstream ss;
    ss << hex << number;
    // 小端 反转
    string result = "";
    result = ss.str().substr(6, 2) + ss.str().substr(4, 2) + ss.str().substr(2, 2) + ss.str().substr(0, 2);
    return result;
}
```

### Step 5. 得到答案

将得到新的四个链接变量值变成16进制加起来，就是我们的结果啦！

```C++
string MD5::getResult() {
    _result += _toHex(_state[0]);
    _result += _toHex(_state[1]);
    _result += _toHex(_state[2]);
    _result += _toHex(_state[3]);
    
    return _result;
}
```

## 结果

```shell
RUN TEST!
TEST >>>>>>>>>>----------
MD5 ("") = d41d8cd9f08204be9800998ecf8427e
MD5 ("a") = 0cc175b9c0f1b6a831c399e269772661
MD5 ("abc") = 900150983cd24fb0d6963f7d28e17f72
MD5 ("hello world") = 5eb63bbbe01eeed093cb22bb8f5acdc3
MD5 ("abcdefghijklmnopqrstuvwxyz") = c3fcd3d76192e47dfb496cca67e13b
MD5 ("ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789") = d174ab98d277d9f5a5611c2c9f419d9f
MD5 ("12345678901234567890123456789012345678901234567890123456789012345678901234567890") = 57edf4a22be3c955ac49da2e2107b67a
Program ended with exit code: 0
```


