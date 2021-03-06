---
title: "三段式版本号，和Composer波浪线符号 ~ 和幂符号 ^ 的区别"
date: 2018-03-10T00:17:55+08:00
typora-root-url: ..\..\static
tags: ["三段式版本号", "composer", "符号"]
categories: ["Composer"]
draft: false
---

三段式版本号的意义：

```base
1：大版本
2：小版本
3：补丁版本
```

^符号和~符号的意义：

```base
~1.2.3  代表  1.2.3  <= 版本号  <  1.3.0    //只改变最末尾的版本
^1.2.3   代表  1.2.3  <= 版本号  <  2.0.0    //尽量使用新版本，但不保证兼容性
```

但要特别说明的是，0开头的版本很特殊，究其原因是因为：0开头的版本是非稳定版，小版本是允许不向下兼容的，使用^ 会出现这种情况

```base
^0.3.0    代表  0.3.0  <= 版本号  < 0.4.0
```

~0.1这种写法是很危险的，具体看：

```base
~0.1    代表  0.1.0  <= 版本号  < 1.0.0
```