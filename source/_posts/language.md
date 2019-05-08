---
title: 使用 JS 正确获取用户语言
date: 2019-05-06 04:44:22
description: 用 JS 来设置语言。
categories: 教程
tags: [环境配置, 教程, Git]
toc: true
---

# 使用 JS 正确获取用户语言

在 JS 中，获取用户当前语言主要通过以下两个 API：

1. `navigator.language`

2. `navigator.languages`

## navigator.language

> 返回一个表示用户偏好语言的字符串

返回的值有以下几种情况

## navigator.languages

 > 返回一个 DOMString 的数组，数组内容表示网站访客所使用的语言