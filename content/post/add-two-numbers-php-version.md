---
title: "两数相加-php版"
date: 2020-06-29T01:05:28+08:00
typora-root-url: ..\..\static
tags: ["php", "算法"]
categories: ["php", "算法"]
draft: false
---

## 题目:

给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

示例：

```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```

## 解析:

### 一 链表:

链表属于`线性表`的一个子类,线性表,顾名思义:0个或多个数据元素的有限序列,线性表有两种存储结果:`顺序存储结构`和`链式存储结构`

`顺序存储结构`: 用一段地址连续的存储单元依次存储线性表的数据元素

`链式存储结构:` 地址可以是连续也可以是不连续的存储单元存储数据元素

而`基于链式存储结构的链表`,可以理解为:你要找一个人,名为张三,首先跑到了A,没找到,A告诉你B可能知道,你跑到B,B说C可能知道,跑到C,果然找到了张三,如果没有就一直不停的去找

链表分为:`单链表`,`循环链表`和`双向链表`

单链表很好理解,每个节点都存有当前数据和下个节点的地址

循环链表,则是在单链表的基础上加上了闭环,最后一个元素又指向了第一个元素,主要特点是:①适合合并两个循环链表,②从任意一个节点出发,都能访问链表的全部节点

双向链表,顾名思义,每个节点既指定了前驱也指定了后继,这个比单链表要复杂一些,有点是操作每个节点相邻的节点效率会比较高

这一题使用的是最简单的单链表

### 二 解题:

```php
/**
 * Definition for a singly-linked list.
 * class ListNode {
 *     public $val = 0;
 *     public $next = null;
 *     function __construct($val) { $this->val = $val; }
 * }
 */
class Solution {

    /**
     * @param ListNode $l1
     * @param ListNode $l2
     * @return ListNode
     */
    function addTwoNumbers($l1, $l2) {
        $obj = null;
        //进位,比如9+9+1=19
        $additional = 0;

        do {
            //第一个链表的一个值+第二个链表的一个值+进位
            $value = $l1->val + $l2->val + $additional;

            if ($value < 10) {//小于10则进位为0
                $additional = 0;
            } else {//大于10则进位为1,同时值-10
                $value -= 10;
                $additional = 1;
            }
            //初始化链表
            $temp_obj = new ListNode($value);
            //第一次$obj为空,请注意,这里是赋值了对象,=和clone是有区别的,一个是深拷贝一个是浅拷贝
            if (is_null($obj)) {
                $obj = $temp_obj;
            } else {
                //下个值则写入链表的next
                $next->next = $temp_obj;
            }
            //为下次循环做准备
            $next = $temp_obj;

            $l1 = $l1->next;
            $l2 = $l2->next;
   
        } while($l1 || $l2 || $additional);//只要有一个都执行,使用do while是为了需先执行一次改变$l1,$l2,$additional

        return $obj;
    }
}
```

