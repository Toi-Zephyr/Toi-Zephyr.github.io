# git-flow 实战教程

## 什么是 git-flow

git-flow 是一个 git 的拓展集，它是按 Vincent Driessen 的分支模型提供高层次的库操作，以实现一种工作流。

## flow

### master

生产环境分支。

每一个正式版本合并到该分支，且打 TAG，该分支代码可靠可用。

合并关系：仅接受 release / hotfix 分支的合并请求。

### develop

开发分支。

开发的代码统一归到该分支，用于日常开发。

合并关系：接受 feature / release / hotfix 分支的合并请求。

### feature

功能分支。

从 develop 分支拉取，开发新功能。当功能开发完毕，合并回 develop 分支。

合并关系：不接受任何分支的合并。从 develop 分支分出来，进行新功能的开发完成后，合并会 develop 分支。

### release

发布分支。

表示发布一个正式的版本。

合并关系：不接受任何分支的合并。从 develop 分支分出来，完成操作后，合并回 master / develop 分支，且打对应的 TAG。

### hotfix

补丁分支。

修复版本线上bug。

合并关系：不接受任何分支的合并。从 master 分支分出来，进行 bug 修复，修复完成合并回 master / develop 分支。

## 参考资料

- [git-flow with SourceTree](https://www.jianshu.com/p/8a3988057d0f)

- [git-flow 备忘清单](https://danielkummer.github.io/git-flow-cheatsheet/index.zh_CN.html)

- [git commit 模板](https://gist.github.com/jmaxhu/8e7fb69a7dcec1b9b953)

- [git-flow 工作流](https://www.git-tower.com/learn/git/ebook/cn/command-line/advanced-topics/git-flow)