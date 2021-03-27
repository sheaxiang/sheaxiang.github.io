---
title: "PHP 两个问号 ?? 和 ?:的区别"
date: 2018-10-19T00:30:49+08:00
typora-root-url: ..\..\static
tags: ["php基础"]
categories: ["PHP"]
draft: false
---

PHP的`三元运算符`已经很常用了,即:(expr1) ? (expr2) : (expr3) 
在 expr1 求值为 `TRUE` 时的值为 expr2，在 expr1 求值为 FALSE 时的值为 expr3

还有一种就是 ?:的形式,自php5.3起,可以省略三元运算符中间那部分。表达式 `expr1 ?: expr3` 在 expr1 求值为 TRUE 时返回 expr1，否则返回 expr3。

然后就是??这种方式,这种是比较运算符中的`NULL 合并操作符`,从PHP7开始提供,作用是:从左往右第一个存在且不为 NULL 的操作数。如果都没有定义且不为 NULL，则返回 NULL。举一个例子:

```php
echo null ?? 2;  // 2

echo 0 ?? 2;  // 0

echo 1 ?? 2; // 1
```

