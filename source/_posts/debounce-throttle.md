---
title: JavaScript 中的防抖与节流
date: 2019-03-24 08:53:02
description: 防抖与节流都是为了限制事件的频繁触发，控制函数的执行次数。
categories: 教程
tags: [JavaScript, 函数, 防抖, 节流]
toc: true
---

为了限制事件的频繁触发，控制函数的执行次数，我们引入以下两个概念。

- debounce 防抖

- throttle 节流

## 防抖

### 何为防抖？

> 在事件被触发n秒后再执行回调，如果在这n秒内又被触发，则重新计时。

也就是说，事件触发 n 秒后才执行，如果在一个事件触发的 n 秒内又触发了这个事件，那就以新的事件的时间为准，n 秒后才执行；无论如何，只有等触发完事件 n 秒内不再触发事件，才执行。

举个例子：如果有人进电梯（触发事件），那电梯将在10秒钟后出发（执行事件监听器），这时如果又有人进电梯了（在10秒内再次触发该事件），我们又得等10秒再出发（重新计时）。

### 实现

#### 核心实现

每当被触发，就会清除计时器，设定新的计时器。

```javascript
function debounce(func, wait) {
    var timeout;
    return function () {
        clearTimeout(timeout)
        timeout = setTimeout(func, wait);
    }
}
```

#### 完善功能

接下来，我们需要解决核心代码存在的两个问题：

1. 将 this 指向正确的对象。

2. 传递参数。

```javascript
function debounce(func, wait) {
    let timeout;

    return function () {
        let context = this;
        let args = arguments;

        clearTimeout(timeout)
        timeout = setTimeout(function () {
            func.apply(context, args)
        }, wait);
    }
}
```

## 节流

### 何为节流？

> 规定在一个单位时间内，只能触发一次函数。如果这个单位时间内触发多次函数，只有一次生效。

也就是说，如果持续触发事件，每隔一段时间，只执行一次事件。

举个例子：就如同 fps 游戏的射速，就算一直按着鼠标射击，也只会在规定射速内射出子弹。

### 实现

#### 核心实现

判断上次触发时间与当下时间的间隔，若大于，则执行，反之不执行。

```javascript
function throttle(func, wait) {
    let previous = 0;

    return function () {
        let now = +new Date();

        if (now - previous > wait) {
            func();
            previous = now;
        }
    }
}
```

#### 完善功能

接下来，我们需要解决核心代码存在的两个问题：

1. 将 this 指向正确的对象。

2. 传递参数。

```javascript
function throttle(func, wait) {
    let context, args;
    let previous = 0;

    return function() {
        let now = +new Date();
        
        context = this;
        args = arguments;
        
        if (now - previous > wait) {
            func.apply(context, args);
            previous = now;
        }
    }
}
```

## 参考资料

- [轻松理解JS函数节流和函数防抖](https://juejin.im/post/5a35ed25f265da431d3cc1b1)

- [7分钟理解JS的节流、防抖及使用场景](https://segmentfault.com/a/1190000016261602)