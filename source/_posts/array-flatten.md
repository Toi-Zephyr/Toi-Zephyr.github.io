---
title: 数组扁平化的 3.5 种方法
date: 2019-03-23 15:33:10
description: 将一个多维数组变为一维数组。
categories: 教程
tags: [JavaScript, 数组, 算法]
toc: true
---

## 扁平化？

数组扁平化即为将一个多维数组，转为一个一维数组。

```javascript
var arr = [1, [2, [3, 4],[true, 'abc']]];

console.log(flatten(arr)) 

// 输出：
// [1, 2, 3, 4, true, "abc"]
```

## 实现

一位 `C++` 选手看到这道题目的第一反应便是递归，通过递归来解决该问题。其中需要注意的是，其中的难点在于递归的结束条件，以及数组的操作。

但是作为 `JavaScript` 选手，我们有很多方便快捷的方法来对数组进行操作。同时，也不一定需要使用递归来解决问题，解题的核心思路就是遍历数组，将数组的元素提取出来。

### 递归方法

关键点：

1. 判断是不是数组，如果是数组就对该数组 `flatten` 。

2. 通过 `concat` 将递归返回的结果与现有的结果合并。

3. 遍历数组时，可以使用 `map` 来代替 `for`。
   
   - `arr.map(item => {···})`


```javascript
function flatten(arr) {
    let result = [];
    for (let i = 0; i < arr.length; i++) {
        if (Array.isArray(arr[i])) {
            result = result.concat(flatten(arr[i]));
        } else {
            result.push(arr[i]);
        }
    }
    return result;
}
```

### 拓展运算符

二维变一维，肯定不能忘记 es6 中的拓展运算符 `...` 。

其核心是：

```javascript
[].concat(...[1, 2, 3, [4, 5]]);

// [1, 2, 3, 4, 5]
```

那么，我们只要遍历数组，当数组中再也没有数组时，便是一个一维数组。

PS: `some()` 方法用于检测数组中的元素是否满足指定条件（函数提供）

```javascript
function flatten(arr) {
    while (arr.some(item => Array.isArray(item))) {
        arr = [].concat(...arr);
    }
    return arr;
}
```

### reduce

说起遍历，肯定少不了 `reduce` ，它一般用来将数组中的每一个值与前面的被返回相加的总和。

PS: 注意要提供初始值 `[]` 。

```javascript
function flatten(arr) {
    return arr.reduce(function (prev, next) {
        return prev.concat(Array.isArray(next) ? flatten(next) : next)
    }, [])
}
```

### toString / join

这是第 0.5 种方法，因为其存在一定的限制，要求数组的各个元素都为数字，否则会出现 `NaN` 的情况。

其核心思想是，将数组转化成字符串，然后通过 `split` 分割，之后再转换回数字。

```javascript
// 使用 toString

var arr = [1, [2, [3, 4],[true, 'abc']]];

var string_arr = arr.toString()
// "1,2,3,4,true,abc"
// 已经“一维”了

var split_arr = string_arr.split(',')
// ["1", "2", "3", "4", "true", "abc"]
// 变成了数组，但是各个元素是字符串


function flatten(arr) {
    return arr.toString().split(',').map(function (item) {
        return Number(item);
    })
}
```

```javascript
// 使用 join

var arr = [1, [2, [3, 4],[true, 'abc']]];

var string_arr = arr.join()
// "1,2,3,4,true,abc"
// 已经“一维”了

var split_arr = string_arr.split(',')
// ["1", "2", "3", "4", "true", "abc"]
// 变成了数组，但是各个元素是字符串

function flatten(arr) {
    return arr.join(',').split(',').map(function (item) {
        return Number(item);
    })
}
```