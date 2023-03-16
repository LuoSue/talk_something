---
title: "LeetBook-初级算法-数组"
slug: "leetcode-elementary-algorithms-array"
description: "leetcode初级算法数组题解大全"
image: "https://img.fallensue.top/index_banner/cover8.png.webp"
categories: ["leetcode"]
tags: ["题解"]
date: 2023-03-16T14:42:07+08:00
lastmod: 2023-03-16T14:42:07+08:00
toc: true
weight: false
draft: false
---

# 26-删除有序数组中的重复项

## 题目描述

[题目链接](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)

## 解题思路

### 双指针法

由于给定的数组属于**升序排列**，那么数组中的重复元素必然会相邻

如果需要删除重复元素的话，就可以直接将所有不重复的元素按序排列到数组的左侧

我们使用两个指针，一个慢指针记为`p`，一个快指针记为`q`，具体的算法流程如下：

比较`p`与`q`所指元素是否相等

* 如果相等，则`q`往后移一位(此处相当于已经检测到有重复元素，需要将快指针后移以找到不重复的元素)
* 如果不相等，则将`q`所指向的元素的值复制到`p+1`所指元素上，`p`和`q`往后移一位
* 重复以上过程直到`q`遍历完整个数组为止

### 变量计数

* 使用一个变量`count`记录重复元素的个数
* 遍历数组，如果当前元素和前一个元素相等，则`count++`
* 如果当前元素和前一个元素不相等，则将当前元素向前移动`count`个位置
* 重复以上过程，直到数组遍历完成
* 返回`原长度-count`作为新数组的长度

## 代码示例

**双指针法：**

```java
/* 双指针法 */
public int removeDuplicates(int[] nums)
{
    // 定义p,q两个指针
    int p = 0;
    int q = 1;
    // 遍历整个数组
    while(q < nums.length)
    {
        // 如果p与q所指元素不相等
        if(nums[p] != nums[q])
        {
            // p所指的下一位的值变为q所指元素的
            nums[p + 1] = nums[q];
            // p后移
            p++;
        }
        // 无论相等与不相等，q都后移一位
        q++;
    }
    // 返回新排序数组的长度
    return p+1;
}
/* 
	双指针法（优化版）
	此方法在整个数组中没有重复元素的情况下，去除了不必要的原地复制操作
	来源于：https://leetcode.cn/problems/remove-duplicates-from-sorted-array/solution/shuang-zhi-zhen-shan-chu-zhong-fu-xiang-dai-you-hu/
*/
public int removeDuplicates(int[] nums) {
    if(nums == null || nums.length == 0) return 0;
    int p = 0;
    int q = 1;
    while(q < nums.length){
        if(nums[p] != nums[q]){
            if(q - p > 1){
                nums[p + 1] = nums[q];
            }
            p++;
        }
        q++;
    }
    return p + 1;
}
```

**变量计数：**

```java
public int removeDuplicates(int[] nums)
{
    // 特殊情况考虑
    if(nums.length == 0)
    {
        return 0;
    }
    // 初始化计数器
    int count = 0;
    // 遍历数组
    for(int i = 1; i < nums.length; i++)
    {
        if(nums[i] == nums[i - 1])
        {
            // 如果相等，则增加计数器
            count++;
        }
        else
        {
            // 如果不等，则向前移动
            nums[i - count] = nums[i];
        }
    }
    // 返回新的数组长度
    return nums.length - count;
}
```



## 复杂度分析

**双指针法**

* 时间复杂度：整个算法的实现需要遍历一次数组，所以时间复杂度为O(n)

* 空间复杂度：原地变换，不需要额外的空间，为O(1)

**变量计数**

* 时间复杂度：整个算法的实现需要遍历一次数组，所以时间复杂度为O(n)
* 空间复杂度：原地变换，不需要额外的空间，为O(1)
