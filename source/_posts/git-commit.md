---
title: git commit 规范
date: 2019-05-04 02:15:10
description: 良好的 commit 规范有助于了解版本的更新，以及后续代码的维护。
categories: 教程
tags: [环境配置, 教程, Git]
toc: true
---

# git commit 规范

良好的 commit 规范有助于了解版本的更新，以及后续代码的维护。

## Angular 规范

```
# <类型>(影响范围): (类型的值见下面描述) <主题> (最多50个字)

# 解释为什么要做这些改动
# |<----  请限制每行最多72个字   ---->|

# 提供相关文章和其它资源的链接和关键字
# 例如: Github issue #23

# --- 提交 结束 ---
# 类型值包含
#    feat (新特性)
#    fix (bug修复)
#    docs (文档改动)
#    style (格式化, 缺失分号等; 不包括生产代码变动)
#    refactor (重构代码，即不是新增功能，也不是修改bug的代码变动)
#    test (添加缺失的测试, 重构测试, 不包括生产代码变动)
#    chore (构建过程或辅助工具的变动，更新grunt任务等; 不包括生产代码变动)
# --------------------
# 注意
#    主题和内容以一个空行分隔
#    主题限制为最大50个字
#    主题行大写
#    主题行结束不用标点
#    主题行使用祈使名
#    内容每行72个字
#    内容用于解释为什么和是什么,而不是怎么做
#    内容多行时以'-'分隔
```

## 具体例子

```
feat(个人页面): 增加粉丝数量显示

在个人页面用户的头像下方，将会显示用户的粉丝数

GitHub issue #34
```

## 参考资料

- [Commit message 和 Change log 编写指南](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)

- [一份建议的git commit模板](https://gist.github.com/jmaxhu/8e7fb69a7dcec1b9b953)

- [git commit 编写风格模板](https://yulijia.net/cn/%E6%A0%BC%E5%BC%8F%E6%8A%80%E5%B7%A7/2016/01/21/git-commit-style.html)