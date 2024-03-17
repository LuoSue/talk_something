---
title: "从零开始刷力扣——数组的遍历"
slug: "leetcode-array-traversal"
description: "个人题解"
image: "https://img.fallensue.top/blog/lofi-coffee-wallpapers.webp"
categories: ["leetcode"]
tags: ["数组"]
date: 2024-03-17T15:12:08+08:00
lastmod: 2024-03-17T15:12:08+08:00
toc: true
weight: false
draft: false
---

# 前言

这些题目来自于leetcode上发布的一篇讨论[有没有人一起从零开始刷力扣](https://leetcode.cn/circle/discuss/48kq9d/)与官方推出的[力扣刷题攻略](https://leetcode.cn/circle/discuss/E3yavq/)，这两篇题单大同小异，都是我认为不错的leetcode入门题单。

本文旨在记录自己的刷题历程，希望早日将这些刷透，烂熟于心。

# 485.Max Consecutive Ones 最大连续 1 的个数

原题链接：

https://leetcode.cn/problems/max-consecutive-ones/description/

题目描述：

给定一个二进制数组 `nums` ， 计算其中最大连续 `1` 的个数

> 二进制数组：数组中元素只有0或1

## 方法一：计数器法

分别定义两个变量：`max_ones`、`current_ones`，`max_ones`用于统计最大连续`1`的个数，`current_ones`用于统计当前连续`1`的个数，在遇到`1`的时候同时更新最大连续`1`的个数，在遇到`0`的时候将计数器归零。

代码实现：

```python
class Solution:
    def findMaxConsecutiveOnes(self, nums: List[int]) -> int:
        max_ones = 0
        current_ones = 0
        
        for num in nums:
            if num == 1:
                current_ones += 1
                max_ones = max(max_ones, current_ones)
            else:
                current_ones = 0
        
        return max_ones
```

* 时间复杂度：$$O(n)$$（仅遍历一次数组）

* 空间复杂度：$$O(1)$$（仅使用辅助变量）

## 方法二：滑动窗口

1. 对数组遍历一次，遍历时保存遇到的最后一个`0`的位置`index`

2. 如果遍历到`i`位置的数字是`0`，那么更新`index`为当前位置`i`
3. 如果遍历到`i`位置的数字是`1`，那么当前区间内共有`i - index`个连续的`1`
4. 记录遍历过程中所有连续的`1`的长度的最大值

代码实现：

```python
class Solution:
    def findMaxConsecutiveOnes(self, nums: List[int]) -> int:
        index = -1
        res = 0
        for i, num in enumerate(nums):
            if num == 0:
                index = i
            else:
                res = max(res, i - index)
        return res
```

* 时间复杂度：$$O(n)$$（仅遍历一次数组）

* 空间复杂度：$$O(1)$$（仅使用辅助变量）

# 495.Teemo Attacking 提莫攻击

原题链接：

https://leetcode.cn/problems/teemo-attacking/

题目描述：
给了一个递增的放毒时间序列以及每次放毒持续时间长度，求出总的中毒时间。

## 方法一：计算间隔

如果`timeSeries`为空，则返回0

使用`total_duration`统计中毒时间，每遍历一个元素计算相邻两个元素之间的时间间隔`diff`

* `diff < duration`，说明在`diff`这段时间内仍处于中毒状态，持续时间为`diff`
* `diff > duration`，说明在`diff`这段时间内只有`duration`时间段处于中毒状态，持续时间为`duration`

最后一个攻击时间的`duration`直接相加即可

代码实现：

```python
class Solution:
    def findPoisonedDuration(self, timeSeries: List[int], duration: int) -> int:
        if not timeSeries:
            return 0
        
        total_duration = 0
        for i in range(1, len(timeSeries)):
            diff = timeSeries[i] - timeSeries[i-1]
            total_duration += min(diff, duration)
        
        total_duration += duration  # 最后一次攻击的持续时间
        return total_duration
```

# 414. Third Maximum Number 第三大的数

原题链接：

https://leetcode.cn/problems/third-maximum-number/

题目大意：

给你一个非空数组，返回此数组中 **第三大的数** 。如果不存在，则返回数组中最大的数。

## 方法一：有限变量+遍历

若要返回第三大的数，则仅需要三个变量就可以表示第一大，第二大，第三大的数

遍历数组中的每一个数字，依照条件更新第一大，第二大，第三大的数

代码实现：

```python
class Solution:
    def thirdMax(self, nums: List[int]) -> int:
        # 初始化第一大、第二大和第三大的数为负无穷
        first = second = third = float('-inf')

        # 遍历数组中的每个数字
        for num in nums:
            # 如果当前数字大于第一大的数
            if num > first:
                # 更新第三大和第二大的数为原来的第二大和第一大
                third = second
                second = first
                # 更新第一大的数为当前数字
                first = num
            # 如果当前数字小于第一大但大于第二大的数
            elif num < first and num > second:
                # 更新第三大的数为原来的第二大的数
                third = second
                # 更新第二大的数为当前数字
                second = num
            # 如果当前数字小于第二大但大于第三大的数
            elif num < second and num > third:
                # 更新第三大的数为当前数字
                third = num

        # 如果第三大的数不是负无穷，则返回第三大的数，否则返回最大的数
        return third if third != float('-inf') else first
```

* 时间复杂度：$$O(n)$$（仅遍历一次数组）

* 空间复杂度：$$O(1)$$（仅使用辅助变量）

## 方法二：有序集合

使用`SortedList`，将`nums`中的元素逐个插入集合中

代码实现：

```python
from sortedcontainers import SortedList

class Solution:
    def thirdMax(self, nums: List[int]) -> int:
        s = SortedList()
        for num in nums:
            if num not in s:
                s.add(num)
                if len(s) > 3:
                    s.pop(0)
        return s[0] if len(s) == 3 else s[-1]
```

* 时间复杂度：$$O(n)$$（仅遍历一次数组）

* 空间复杂度：$$O(n)$$（创建集合）

# 628. Maximum Product of Three Numbers 三个数的最大乘积

原题链接：

https://leetcode.cn/problems/maximum-product-of-three-numbers/

题目大意：

给你一个整型数组 `nums` ，在数组中找出由三个数组成的最大乘积，并输出这个乘积。

## 方法一：排序暴力解

将原数组排序，对比后三位乘积和前两位与最后一位乘积，返回最大的那个数值

> 排序完成之后，前两位可能为负数

代码实现：

```python
class Solution:
    def maximumProduct(self, nums: List[int]) -> int:
        nums.sort()
        negative_prod = nums[0] * nums[1] * nums[-1]
        positive_prod = nums[-1] * nums[-2] * nums[-3]
        return max(negative_prod, positive_prod)
```

* 时间复杂度：$$O(nlogn)$$（排序）

* 空间复杂度：$$O(1)$$（额外变量）

## 方法二：有限变量+遍历

原理同上题方法一，定义五个变量

`min_1`：第一小值，`min_2`：第二小值，`max_1`：第一大值，`max_2`：第二大值，`max_3`：第三大值

每遇到一个元素，更新五个变量中的值

代码实现：

```python
class Solution:
    def maximumProduct(self, nums: list[int]) -> int:
        min_1, min_2, max_1, max_2, max_3 = float('inf'), float('inf'), float('-inf'), float('-inf'), float('-inf')
        for num in nums:
            if num < min_1:
                min_1, min_2 = num, min_1
            elif num < min_2:
                min_2 = num
            if num > max_1:
                max_1, max_2, max_3 = num, max_1, max_2
            elif num > max_2:
                max_2, max_3 = num, max_2
            elif num > max_3:
                max_3 = num
        return max(min_1 * min_2 * max_1, max_1 * max_2 * max_3)
```

