---
title: "两数之和-php版"
date: 2019-05-25T00:59:05+08:00
typora-root-url: ..\..\static
tags: ["php", "算法"]
categories: ["php", "算法"]
draft: false
---

我们先来看题目:

```
给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。
你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。
示例:
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

再来看下代码:

```php
class Solution {

    /**
     * @param Integer[] $nums
     * @param Integer $target
     * @return Integer[]
     */
    public static function twoSum($nums, $target) {
        $find = [];
        for($i = 0; $i < count($nums); $i++) {
            $value = $target - $nums[$i];
            if ($p = array_key_exists($value, $find)) {
                return [$find[$value], $i];
            }
            $find[$nums[$i]] = $i;
        }

        return [];
    }
}
```

然后我们可以测试一下:

```php
var_dump(Solution::twoSum([2, 7, 11, 15], 9));
```

最后再来看下执行结果:

执行用时 : 20 ms, 在Two Sum的PHP提交中击败了98.53% 的用户
内存消耗 : 15.9 MB, 在Two Sum的PHP提交中击败了18.07% 的用户