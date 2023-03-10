---
title: "数据结构-绪论"
slug: "data_structure_introduction"
description: "二战408记录"
image: "https://img.fallensue.top/post_banner/HelloHexo.png"
categories: ["408"]
tags: ["数据结构"]
date: 2023-03-12T15:39:15+08:00
lastmod: 2023-03-12T15:39:15+08:00
toc: true
weight: false
draft: false 
---

# 数据结构的基本概念

## 基本概念和术语

**数据**：

* 信息的载体
* 能被程序识别和处理的符号的集合
* 计算机程序加工的原料

**数据元素**：数据的基本单位，由若干数据项组成（数据项是构成数据元素的不可分割的最小单位）

**数据对象**：具有相同性质的数据元素的集合，数据的一个子集

**数据类型**：一个值的集合和定义在此集合上的一组操作的总称

* 原子类型：其值不可再分的数据类型
* 结构类型：其值可以再分解为若干成分（分量）的数据类型
* 抽象数据类型：抽象数据组织及与之相关的操作

**数据结构**：相互之间存在一种或多种特定关系的数据元素的集合

包括三方面的内容：逻辑结构、存储结构、数据的运算



数据的逻辑结构和存储结构密不可分，算法的设计取决于所选定的逻辑结构，算法的实现依赖于所采用的存储结构

## 数据结构三要素

### 数据的逻辑结构

逻辑结构是指数据元素之间的逻辑关系，即从逻辑关系上描述数据，与数据的存储无关

分为线性结构和非线性结构

![](https://img.fallensue.top/blog/20230312161051.png)

**集合**：结构中的数据元素之间除“同属一个集合”外，别无其他关系

**线性结构**：结构中的数据元素之间只存在一对一的关系

**树形结构**：结构中的数据元素之间存在一对多的关系

**图状结构或网状结构**：结构中的数据元素之间存在多对多的关系

### 数据的存储结构

存储结构是指数据结构在计算机中的表示（又称映像），也称物理结构。是用计算机语言实现的逻辑结构，它依赖于计算机语言。

数据的存储结构主要有顺序存储、链式存储、索引存储和散列存储

**顺序存储**：

* 把逻辑上相邻的元素存储在物理位置也相邻的存储单元中，元素之间的关系由存储单元的邻接关系来体现
* 优点：随机存取，每个元素占用最少的存储空间
* 缺点：只能使用相邻的一整块存储单元，因此可能产生较多的外部碎片   

**链式存储**：

* 不要求逻辑上相邻的元素在物理位置上也相邻，借助指示元素存储地址的指针来表示元素之间的逻辑关系
* 优点：不会出现碎片现象，能充分利用所有存储单元
* 缺点：每个元素因为存储指针而占用额外的存储空间，只能实现顺序存取

**索引存储**：

* 存储信息的同时，还建立附加的索引表，表中的每项称为索引项
* 索引项的一般形式是（关键字，地址）
* 优点：检索速度快
* 缺点：附加的索引表额外占用存储空间，增删数据时需要修改索引表，也会花费额外时间

**散列存储**：

* 根据元素的关键字直接计算出该元素的存储地址
* 优点：检索、增加和删除结点的操作都很快
* 缺点：散列函数设计不当，会出现元素存储单元的冲突，解决冲突会增加时间和空间开销

### 数据的运算

施加在数据上的运算包括运算的定义和实现

运算的定义是针对逻辑结构的，指出运算的功能；运算的实现是针对存储结构的，指出运算的具体操作步骤

# 算法和算法评价

## 算法的基本概念

算法：对特定问题求解步骤的一种描述，它是指令的有限序列，其中的每条指令表示一个或多个操作

一个算法还具有下列5个重要特性

1. **有穷性**：一个算法必须总在执行有穷步之后结束，且每一步都可在有穷时间内完成
2. **确定性**：算法中每条指令必须有确切的含义，对于相同的输入只能得出相同的输出
3. **可行性**：算法中描述的操作都可以通过已经实现的基本运算执行有限次来实现
4. **输入**：一个算法有零个或多个输入，这些输入取自于某个特定对象的集合
5. **输出**：一个算法有一个或多个输出，这些输出是与输入有着某种特定关系的量

一个好的算法应考虑达到如下目标：

* 正确性
* 可读性
* 健壮性
* 高效率与底存储量需求

## 算法效率的度量

### 时间复杂度

一个语句的频度是指该语句在算法中被重复执行的次数，算法中所有语句的频度之和记为T(n)，它是该算法问题规模n的函数，时间复杂度主要分析T(n)的数量级

### 空间复杂度

定义为该算法所耗费的存储空间，算法原地工作是指算法所需的辅助空间为常量，即O(1)

