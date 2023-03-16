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

# 26.删除有序数组中的重复项

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

**双指针法**

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

**变量计数**

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

# 122.买卖股票的最佳时机 II

## 题目描述

[原题链接](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/)

## 解题思路

### 贪心算法

原理：**贪心算法或贪心思想**采用贪心的策略，保证每一次操作都是**局部最优**的，从而得到**全局最优**的结果

由于本题没有限制股票买入/卖出的次数，所以我们可以采用贪心算法，只需要从头到尾遍历一次数组，只要后一天的价格高于前一天，就在前一天买入，后一天卖出。这样可以保证每一次都能够获得正利润，并且不会错过任何一个上涨的区间。

### 动态规划

原理：我们只需要穷举每一天的`状态`，然后再根据每个`状态`做出最优的`选择`

我们需要使用一个二维矩阵表示`状态`

1. 状态定义

   状态`dp[i][j]`定义：`dp[i][j]`表示到下标为`i`的这一天，持股状态为`j`时，我们手上有的最大现金数

   * 第一维`i`表示下标为`i `的那一天（ 具有前缀性质，即考虑了之前天数的交易 ）
   * 第二维`j`表示下标为`i`的那一天是持有股票，还是持有现金。这里 0 表示持有现金（cash），1 表示持有股票（stock）

2. 状态转移方程定义

   `dp[i][0] = max(dp[i-1][0],dp[i-1][1] + prices[i])`

   `dp[i][1] = max(dp[i-1][1],dp[i-1][0] - prices[i])`

   在第`i`天持有现金，则可能是第`i-1`天就持有现金，第`i`天也没有购入股票；或者是第`i-1`天持有股票，在第`i`天售出

   在第`i`天持有股票，则可能是第`i-1`天就持有股票，第`i`天也没有出售股票；或者是第`i-1`天没有股票，在第`i`天买入

3. 最后返回值：`dp[length - 1][0]`，因为必然会有`dp[length - 1][0] > dp[length - 1][1]`

## 代码示例

**贪心算法**

```java
public int maxProfit(int[] prices)
{
    // 初始化总利润为0
    int profit = 0;
    // 遍历数组中每个元素
    for(int i = 1; i < prices.length; i++)
    {
        // 如果后一天比前一天高，则进行交易，并累加利润
        if(prices[i] > prices[i-1])
        {
            profit += prices[i] - prices[i-1];
        }
    }
    // 返回总利润
    return profit;
}
```

**动态规划**

```java
public int maxProfit(int[] prices)
{
    int n = prices.length;
    // 定义两种状态
    int dp_i_0 = 0, dp_i_1 = Integer.MIN_VALUE;
    for(int i = 0; i < n; i++)
    {
        int temp = dp_i_0;
        dp_i_0 = Math.max(dp_i_0 , dp_i_1 + prices[i]);
        dp_i_1 = Math.max(dp_i_1, temp - prices[i]);
    }
    return dp_i_0;
}
```

## 复杂度分析

**贪心算法**

* 时间复杂度：O(n)，n为数组的长度，需要遍历一次数组
* 空间复杂度：O(1)，存储空间为常量

**动态规划**

* 时间复杂度：O(n)，n为数组的长度，需要遍历一次数组
* 空间复杂度：O(1)，存储空间为常量
