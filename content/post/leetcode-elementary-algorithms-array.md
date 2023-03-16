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

## 26. 删除有序数组中的重复项

### 题目描述

[题目链接](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)

### 解题思路

#### 双指针法

由于给定的数组属于**升序排列**，那么数组中的重复元素必然会相邻

如果需要删除重复元素的话，就可以直接将所有不重复的元素按序排列到数组的左侧

我们使用两个指针，一个慢指针记为`p`，一个快指针记为`q`，具体的算法流程如下：

比较`p`与`q`所指元素是否相等

* 如果相等，则`q`往后移一位(此处相当于已经检测到有重复元素，需要将快指针后移以找到不重复的元素)
* 如果不相等，则将`q`所指向的元素的值复制到`p+1`所指元素上，`p`和`q`往后移一位
* 重复以上过程直到`q`遍历完整个数组为止

#### 变量计数

* 使用一个变量`count`记录重复元素的个数
* 遍历数组，如果当前元素和前一个元素相等，则`count++`
* 如果当前元素和前一个元素不相等，则将当前元素向前移动`count`个位置
* 重复以上过程，直到数组遍历完成
* 返回`原长度-count`作为新数组的长度

### 代码示例

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



### 复杂度分析

**双指针法**

* 时间复杂度：整个算法的实现需要遍历一次数组，所以时间复杂度为O(n)

* 空间复杂度：原地变换，不需要额外的空间，为O(1)

**变量计数**

* 时间复杂度：整个算法的实现需要遍历一次数组，所以时间复杂度为O(n)
* 空间复杂度：原地变换，不需要额外的空间，为O(1)

## 122. 买卖股票的最佳时机 II

### 题目描述

[原题链接](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/)

### 解题思路

#### 贪心算法

原理：**贪心算法或贪心思想**采用贪心的策略，保证每一次操作都是**局部最优**的，从而得到**全局最优**的结果

由于本题没有限制股票买入/卖出的次数，所以我们可以采用贪心算法，只需要从头到尾遍历一次数组，只要后一天的价格高于前一天，就在前一天买入，后一天卖出。这样可以保证每一次都能够获得正利润，并且不会错过任何一个上涨的区间。

#### 动态规划

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

### 代码示例

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

### 复杂度分析

**贪心算法**

* 时间复杂度：O(n)，n为数组的长度，需要遍历一次数组
* 空间复杂度：O(1)，存储空间为常量

**动态规划**

* 时间复杂度：O(n)，n为数组的长度，需要遍历一次数组
* 空间复杂度：O(1)，存储空间为常量

## 189. 轮转数组

### 题目描述

[原题链接](https://leetcode.cn/problems/rotate-array/)

### 解题思路

#### 使用额外的数组

使用额外的数组，将每个元素放至正确的位置。原数组中下标为`i`的元素需要放至新数组中下标为`(i+k) mod length`的位置，最后只需要将新数组拷贝至原数组即可

#### 数组反转

原理：利用反转的性质，将数组分为两部分，前面是`n-k`个元素，后面是`k`个元素。如果我们将整个数组进行反转，那么原来的后`k`个元素就会到前面，原来的前`n-k`个元素就会到后面，但是顺序都是相反的。所以如果我们再分别对这两个部分进行反转，就能够恢复它们的顺序，实现轮转的效果

```
例如，如果数组是 [1,2,3,4,5,6,7] ，k 是 3 ，那么按照以下步骤进行反转：

反转整个数组：[7,6,5,4,3,2,1]
反转前k个元素：[5,6,7,4,3,2,1]
反转剩余的元素：[5,6,7,1,2,3,4]
```

#### 环状替换

原理：将整个数组看成一个头尾相接的环，将每个元素向右移动k位，如果超出数组长度就从头开始，需要使用一个变量`count`来记录移动了多少次，当`count`等于数组长度时，就停止移动。我们也需要使用一个变量`start`来记录每次循环的起始位置，如果发生了重复覆盖的情况，就将`start++`，并继续循环

### 代码示例

**使用额外的数组**

```java
public void rotate(int[] nums, int k) {
    int n = nums.length;
    int [] arr = new int[n];
    for(int i = 0; i < n; i++)
    {
        int j = (i + k) % n;
        arr[j] = nums[i];
    }
    for(int m = 0; m < n; m++)
    {
        nums[m] = arr[m];
    }
}
```

**数组反转**

```java
// 反转数组代码
void reverse(int[] nums, int start, int end)
{
    while(start < end)
    {
        int temp = nums[start];
        nums[start] = nums[end];
        nums[end] = temp;
        start++;
        end--;
    }
}
public void rotate(int[] nums, int k)
{
    // 防止k大于数组长度
    k %= nums.length;
    // 反转整个数组
    reverse(nums, 0, nums.length - 1);
    // 反转前k个元素
    reverse(nums, 0, k - 1);
    // 反转剩余的元素
    reverse(nums, k, nums.length - 1);
}
```

**环状替换**

```java
public void rotate(int[] nums, int k)
{
    // 防止k大于数组长度
    k %= nums.length;
    // 记录移动了多少次
    int count = 0;
    // 循环直到移动完所有元素
    for(int start = 0; count < nums.length; start++)
    {
        // 记录当前位置
        int current = start;
        // 记录当前元素值
        int prev = nums[start];
        do
        {
            // 计算下一个位置
            int next = (current + k) % nums.length;
            // 记录下一个元素值
            int temp = nums[next];
            // 将当前元素值赋给下一个位置
            nums[next] = prev;
            // 更新当前元素值为下一个元素值
            prev = temp;
            // 更新当前位置为下一个位置
            current = next;
            // 增加移动次数
            count++;
        }while(start != current);
    }
}
```

### 复杂度分析

**使用额外的数组**

* 时间复杂度：整个算法的实现需要遍历一次数组，所以时间复杂度为O(n)

* 空间复杂度：需要建立新数组，空间复杂度为O(n)

**数组反转**

* 时间复杂度：遍历一次数组，时间复杂度为O(n)
* 空间复杂度：原地，空间复杂度为O(1)

**环状替换**

* 时间复杂度：遍历一次数组，时间复杂度为O(n)
* 空间复杂度：原地，空间复杂度为O(1)

## 217. 存在重复元素

### 题目描述

[原题链接](https://leetcode.cn/problems/contains-duplicate/)

### 解题思路

#### 哈希表

原理：将数组中的元素插入到哈希表中，如果插入一个元素时发现此元素已经存在于哈希表中，则说明存在重复元素

#### 排序

原理：对数字从小到大进行排序之后，数组的重复元素必然出现于相邻位置。对数组进行排序之后扫描数组，每次判断相邻的两个元素是否相等，如果相等则说明存在重复的元素

### 代码示例

**哈希表**

```java
public boolean containsDuplicate(int[] nums)
{
    // 定义哈希表
    Set<Integer> set = new HashSet<>();
    //遍历数组，将元素加入哈希表
    for (int num : nums) {
        // 如果已存在，说明重复元素
        if (!set.add(num)) {
            return true;
        }
    }
    return false;
}
```

**排序**

```java
public boolean containsDuplicate(int[] nums)
{
    // 对数组进行排序
    Arrays.sort(nums);
    // 遍历数组
    for(int i = 0; i < nums.length - 1; i++)
    {
        // 比较相邻元素
        if(nums[i] == nums[i + 1])
        {
            return true;
        }
    }
    return false;
}
```

### 复杂度分析

**哈希表**

* 时间复杂度：O(n)
* 空间复杂度：O(n)
* 这种方法的优点是速度快，不需要改变数组的顺序。缺点是需要额外的空间来存储集合。

**排序**

* 时间复杂度：O(nlogn)
* 空间复杂度：O(1)或O(n)，取决于排序算法是否原地排序
* 这种方法的优点是不需要额外的空间，或者只需要很少的空间。缺点是速度慢，而且会改变数组的顺序。

## 136. 只出现一次的数字

### 题目描述

[原题链接](https://leetcode.cn/problems/single-number/)

### 解题思路

#### 异或

原理：异或运算符的原理是，对于两个二进制数，如果相同位上的数字相同，则结果为0，否则为1。因此，如果一个数组中有两个相同的数字，它们的异或结果为0，而0与任何数字异或都等于那个数字本身。所以，如果一个数组中只有一个数字出现一次，而其他数字都出现两次，那么把所有数字进行异或运算后得到的结果就是那个**只出现一次的数字**。

#### 哈希表

原理：同上一题

### 代码示例

**异或**

```java
public int singleNumber(int[] nums) {
    int ans = 0;
    for (int num : nums) {
        ans ^= num;
    }
    return ans;
}
```

**哈希表**

```java
public int singleNumber(int[] nums)
{
    HashMap<Integer,Integer> map = new HashMap<>();
    for(int num : nums)
    {
        map.put(num,map.getOrDefault(num, 0) + 1);
    }
    for(int key : map.keySet())
    {
        if(map.get(key) == 1)
        {
            return key;
        }
    }
    return -1;
}
```

### 复杂度分析

**异或**

* 时间复杂度：O(n)
* 空间复杂度：O(1)

**哈希表**

* 时间复杂度：O(n)
* 空间复杂度：O(n)

## 350. 两个数组的交集 II

### 题目描述

[原题链接](https://leetcode.cn/problems/intersection-of-two-arrays-ii/)

### 解题思路



### 代码示例



### 复杂度分析



## 66. 加一

### 题目描述

[原题链接](https://leetcode.cn/problems/plus-one/)

### 解题思路



### 代码示例



### 复杂度分析



## 283. 移动零

### 题目描述

#### [283. 移动零](https://leetcode.cn/problems/move-zeroes/)

### 解题思路



### 代码示例



### 复杂度分析



## 1. 两数之和

### 题目描述

#### [1. 两数之和](https://leetcode.cn/problems/two-sum/)

### 解题思路



### 代码示例



### 复杂度分析



## 36. 有效的数独

### 题目描述

[36. 有效的数独](https://leetcode.cn/problems/valid-sudoku/)

### 解题思路



### 代码示例



### 复杂度分析



## 48. 旋转图像

### 题目描述

[48. 旋转图像](https://leetcode.cn/problems/rotate-image/)

### 解题思路



### 代码示例



### 复杂度分析

