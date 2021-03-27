---
title: "git tag标签的使用"
date: 2018-03-03T00:14:23+08:00
typora-root-url: ..\..\static
tags: ["git tag"]
categories: ["git"]
draft: false
---

原来一直不知道composer require这种格式是如何实现的：

```shell
composer require "sheaxiang/action-log:^1.1"
```

下面来看下如何实现：

一：首先创建一个git仓库，将代码上传上去

二：如果该项目有新的版本，可以使用git的tag标签方式发布新版本：

首先先加入本地仓库：

```shell
git add . && git commit -m "update"
```

然后创建tag标签：

```shell
git tag 1.0 -a
```

最后再使用命令上传：

```shell
git push --tags
```

这样git仓库中将有tag为1.0的标签，如果对应加入了composer，则可使用开始的那种方式的命令。

再贴几个关于git tag的命令：

删除标签：

```shell
git tag -d 1.0 //先删除本地仓库的tag

git push origin :refs/tags/1.0 //删除远程仓库的tag
```