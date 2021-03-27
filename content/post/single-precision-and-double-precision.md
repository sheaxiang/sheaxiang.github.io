---
title: "单精度和双精度"
date: 2018-11-24T00:39:31+08:00
typora-root-url: ..\..\static
tags: ["精度"]
categories: ["计算机基础"]
draft: false
---

单精度:也称为float,占4个字节,32位,有效位数为7位由1位符号,8位指数,23位小数组成

![单精度](/images/single-precision-and-double-precision.assets/1v2-749cc641eb4d5dafd085e8c23f8826aa_hd.png)

双精度:也称为double,占8个字节,64位,有效位16位,由1位符号,11位指数,52位小数组成

![双精度](/images/single-precision-and-double-precision.assets/2v2-48240f0e1e0dd33ec89100cbe2d30707_hd.png)

不管float还是double 在计算机上的存储都遵循`IEEE规范`，使用二进制科学计法，都包含三个部分：`符号位`，`指数位`和`尾数部分`。其中float的符号位，指数位，尾数部分分别为1, 8, 23. 双精度分别为1, 11, 52.

精度主要取决于`尾数部分的位数`，float为23位，除去全部为0的情况以外，最小为2的-23次方，约等于1.19乘以10的-7次方，所以float小数部分只能精确到后面6位，加上小数点前的一位，即有效数字为7位。 类似，double 尾数部分52位，最小为2的-52次方，约为2.22乘以10的-16次方，所以精确到小数点后15位，有效位数为16位.