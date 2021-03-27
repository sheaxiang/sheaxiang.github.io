---
title: "PHP 可变数量的参数列表 …"
date: 2018-03-27T00:20:55+08:00
typora-root-url: ..\..\static
tags: ["php基础"]
categories: ["PHP"]
draft: false
---

PHP在用户自定义函数中支持可变数量的参数列表，在PHP5.6及以上的版本中，由语法…语法实现：

```php
function sum(...$numbers)
{
	$a = 0;
	foreach ($numbers as $number) {
		$a += $number;
	}
	return $a;
}
echo sum(1,2,3,4);
```

输出结果为：

```
10
```

可以看出…语法是将传入参数格式化为数组，则可以反过来：

```php
function add($a, $b) {
	return $a + $b;
}

echo add(...[1, 2])."\n";

$a = [1, 2];
echo add(...$a);
```

输出结果为：

```
3  3
```