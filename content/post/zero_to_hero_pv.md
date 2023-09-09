---
title: "从零开始的PV学习"
slug: "Zero-to-hero-pv"
description: "PV大题，一篇文章搞定"
image: "https://img.fallensue.top/blog/1319293.webp"
categories: ["408"]
tags: ["OS"]
date: 2023-09-09T21:59:25+08:00
lastmod: 2023-09-09T21:59:25+08:00
toc: true
weight: false
draft: false
---

# 基本概念

**临界资源**：一次仅允许一个进程使用的资源

**同步**：直接制约关系，为完成某种任务而建立的两个或多个进程，这些进程因为需要在某些位置上协调它们的工作次序而等待、传递信息所产生的制约关系

**互斥**：间接制约关系，当一个进程进入临界区使用临界资源时，另一个进程必须等待，当占用临界资源的进程退出临界区之后，另一个进程才允许去访问此临界资源

**P操作**：表示进程请求一个该类资源，资源不足时自我阻塞，资源充足时获得资源

**V操作**：表示进程释放一个该类资源，使系统中可供分配的该类资源个数加一

**互斥锁**：进程在进入临界区时应该获得锁，在退出临界区时应该获得锁

# 经典同步问题

## 生产者——消费者问题

见王道考研操作系统部分

## 读者——写者问题

见王道考研操作系统部分

## 哲学家进餐问题

见王道考研操作系统部分

## 吸烟者问题

见王道考研操作系统部分

## 独木桥问题

### 题目描述

某国道原为双向通路，因道路维修导致双向通路出现如下图所示的桥，车流如图中箭头所示。**桥上不允许两车交汇**，但允许同方向多辆车**依次**通行(即桥上可以有多个同方向车通过)。请设计控制算法，实现交通管理，以防止桥上堵塞。

![在这里插入图片描述](https://img-blog.csdnimg.cn/48fd2e9d0dec4c2492dbcca74205e05d.png#pic_center)

### 分析题目

这其实是一道**读者写者问题**的变体问题，与读者写者中的读者进程的思路是相同的。

大致流程如下图所示：

![one-way-bridge](https://img.fallensue.top/typora/one-way-bridge.png)

**思路：**

一侧车想上桥，需要先检查桥的对面是否有车上桥，如果对面桥上有车了，那么就等待到对面桥上的车下完桥为止，他就上桥，将桥“占领”，此时另一侧的车不允许再上桥下桥，但是与他同侧的车可以源源不断地上桥下桥，等到最后一辆车下桥，就由最后一辆车释放桥。另一侧也是一样的。

两侧车辆平等，是两个相同的“读者进程”组成。

**信号量设计：**

桥是临界资源，一次只能由一类相同的进程进行访问，需要设置一个互斥信号量实现对桥的互斥访问。

两侧车辆需要设置计数量来统计同侧车辆的个数，以判断是否应该将桥释放，两个变量都为int类型。

避免修改顺序的先后造成变量的差错，还需要设置两个互斥量来实现对变量的互斥访问。

### 伪代码实现

```shell
semaphore brige=1; //互斥信号量，实现对独木桥的互斥访问
int northToS=0; //计数变量，统计从北到南的车辆数
int southToN=0; //计数变量，统计从南到北的车辆数
semaphore northMutex=1; //互斥信号量，实现对northToS变量的互斥修改
semaphore SouthMutex=1; //互斥信号量，实现对southToN变量的互斥修改

n_to_s()
{
	P(northMutex);
	northToS++;
	if(northToS==1)
		P(bridge); //北侧第一辆到达的车辆抢占桥
	V(northMutex);
	过桥;
	P(northMutex);
	northToS--;
	if(northToS==0)
		V(bridge); //北侧最后一辆车下桥后释放独木桥
	V(northMutex);
}

s_to_n()
{
	P(southMutex);
	southToN++;
	if(southToN==1)
		P(bridge); //南侧第一辆到达的车辆抢占桥
	V(southMutex);
	过桥;
	P(southMutex);
	southToN--;
	if(southToN==0)
		V(bridge); //南侧最后一辆车下桥后释放独木桥
	V(southMutex);
}
```

### 拓展题型

如上所述，加入说北侧的第一辆车上桥，占领桥之后，还未下桥，那北侧可以有无数个车辆上桥。如果说为了安全起见，我们不允许无数辆汽车上桥，只允许K辆汽车同时在桥上。该问题的完整描述如下：
南北向汽车过独木桥，为了保证安全，只要桥上无车，则允许一方的汽车过桥，待一方的车全部过完后， 另一方的车才允许过桥。为了安全起见，最多允许K（K>=1）辆汽车在桥上。请用信号量和 P、V操作写出过独木桥问题的同步算法。

**思路**：
只需要增加一个记录型的信号量，用来统计同时在桥上的车辆数目即可

```shell
semaphore brige=1; //互斥信号量，实现对独木桥的互斥访问
int northToS=0; //计数变量，统计从北到南的车辆数
int southToN=0; //计数变量，统计从南到北的车辆数
semaphore northMutex=1; //互斥信号量，实现对northToS变量的互斥修改
semaphore SouthMutex=1; //互斥信号量，实现对southToN变量的互斥修改
semaphore count=k; //桥上最多有k辆汽车

n_to_s()
{
    while(1){
        P(northMutex);
        northToS++;
        if(northToS==1)
            P(bridge); //北侧第一辆到达的车辆抢占桥
        V(northMutex);
        P(count);
        过桥;
        V(count);
        P(northMutex);
        northToS--;
        if(northToS==0)
            V(bridge); //北侧最后一辆车下桥后释放独木桥
        V(northMutex);
    }
}

s_to_n()
{
    while(1)
    {
        P(southMutex);
        southToN++;
        if(southToN==1)
            P(bridge); //南侧第一辆到达的车辆抢占桥
        V(southMutex);
        P(count);
        过桥;
        V(count);
        P(southMutex);
        southToN--;
        if(southToN==0)
            V(bridge); //南侧最后一辆车下桥后释放独木桥
        V(southMutex);
    }
}
```

### 结语

独木桥问题是经典的读者写者问题变种，这个问题还衍生出了飞机起飞问题，幼儿园小朋友做游戏问题等。

## 理发师问题

### 题目描述

理发店包含一间接待室和一间工作室，有一名理发师，接待室内有n(n≥1)把椅子，而工作室只有1把椅子。如果没有顾客，理发师就去睡觉；如果理发师在睡觉；则顾客会唤醒他；如果理发师在忙且接待室有空闲椅子，那么此顾客会坐在其中1把空闲的椅子上等待；如果来时所有椅子都有人，那么顾客离去。请采用信号量机制解决该理发师问题(用伪代码描述)。

### 分析题目

理发师问题与生产者——消费者问题不同，生产者消费者问题是“生产——消费”问题，理发师问题是“服务——被服务”的问题。

理发师与顾客进程构成同步关系，即需要有顾客前来理发，理发师才“可用”（提供服务），否则理发师睡觉。

顾客进程之间存在互斥关系，同时只能有一名顾客在理发。

对椅子的访问也是互斥的，不可能两个人挤一张椅子吧。

```shell
semaphore barber=0; //理发师信号量，为0时在睡觉，为1时在工作
semaphore customers=0; //顾客信号量，为0时没有需要理发的顾客，为1时有顾客需要理发
semaphore mutex=1; //互斥锁，用于修改等待座位数量
int waiting = 0; //正在等待的顾客数量

barber()
{
	while(1)
	{
		P(customers); //理发师环顾四周，没有顾客就睡觉
		P(mutex); //只能给一个顾客提供服务
		waiting--; //有顾客得到服务，等待区的椅子空出一个
		V(barber); // 理发师被叫醒，给顾客提供服务
		V(mutex);
		理发;
	}
}

customers()
{
	while(1)
	{
		P(mutex); //一次只能有一个顾客访问椅子
		if(waiting<n)//顾客在门口看了看，如果有空闲的椅子
		{
			waiting++; //进店坐下
			V(barber); //告诉理发师我要理发
			V(mutex);
			P(barber); //等待理发师
		}
		else
			V(mutex); //位置不够，走了
	}
}
```

## 录像厅问题

### 题目描述

假设一个录像厅有0,1,2三种不同的录像片可由观众选择放映。录像厅的放映规则为：

1. 任何时刻最多只能放映一种录像片，正在放映的录像片是自动循环放映的。最后一个观众主动离开时结束当前录像片的放映。
2. 选择当前正在放映录像片的观众可立即进入，允许同时有多位选择同一中录像片的观众同时观看，同时观看的观众数量不受限制。
3. 等待观看其他录像片的观众按到达顺序排队，当一种新的录像片开始放映时，所有等待观看该录像片的观众可一次进入录像厅同时观看。
   

### 分析题目

变种的读者写者问题，可以把录像片当做n种不同的书，只有看同一本书的观众可以看。（也和独木桥问题类似）

录像厅是临界资源，只有看同一部录像片的观众可以同时进入。

为三部录像片分别设置三个信号量，`v0,v1,v2`，初值分别为`1,1,1`

三部录像片的观众数量分别为`count0,count1,count2`，需要互斥访问

```shell
semaphore v=1,v0=1,v1=1,v2=1;
int count0=0,count1=0,count2=0;
video1()
{
	while(1)
	{
		P(v0);
		count0++;
		if(count0==1)
			P(v); // 第一个到达录像厅的1号观众的抢占遥控器
		V(v0);
		看录像片;
		P(v0);
		count0--;
		if(count0==0)
			V(v); // 没人看了，结束1号影片的播放，让出录像厅
		V(v0);
	}
}

video2()
{
	while(1)
	{
		P(v1);
		count1++;
		if(count0==1)
			P(v); // 第一个到达录像厅的2号观众的抢占遥控器
		V(v1);
		看录像片;
		P(v1);
		count1--;
		if(count1==0)
			V(v); // 没人看了，结束2号影片的播放，让出录像厅
		V(v1);
	}
}

video3()
{
	while(1)
	{
		P(v2);
		count2++;
		if(count2==1)
			P(v); // 第一个到达录像厅的3号观众的抢占遥控器
		V(v2);
		看录像片;
		P(v2);
		count2--;
		if(count2==0)
			V(v); // 没人看了，结束3号影片的播放，让出录像厅
		V(v2);
	}
}
```

## 公交车问题

### 题目描述

在公共汽车上，司机和售票员的工作流程如图所示。为保证乘客的安全，司机和售票员应密切配合协调工作。请用信号量来实现司机与售票员之间的同步。

![img](https://img-blog.csdnimg.cn/20191224071206515.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NfOTk5OTk5,size_16,color_FFFFFF,t_70)

### 分析题目

此题中不存在资源抢夺现象，所以没有互斥的信号量，司机和售票员之间是同步关系

司机需要接收门是否关好的信号量，售票员需要接收车是否到站的信号量

**活动的顺序：**

> 关车门→启动车辆→正常行车→售票→到站停车→开车门

初始状态为：停车且门未关

流程：售票员告诉司机关门的信号，司机收到之后开始正常行驶，到站时司机给售票员停车的信号

设关门信号量为door，停车信号量为stop

```shell
semaphore door=0,stop=0;

Driver()
{
	while(1)
	{
		P(door); //司机等待关门信号，一旦取得信号，就启动车辆
		启动车辆;
		正常行驶;
		到站停车;
		V(stop); //司机给售票员停车的信号
	}
}

Conductor()
{
	while(1)
	{
		关车门;
		V(door); //售票员给司机已经关门的信号
		售票;
		P(stop); //等待停车信号，一旦完成停车，就开门
		开车门;
		乘客上下车;
	}
}
```

# 408考试中出现过的PV大题

## 2009（生产者与消费者问题）

![image-20230904221541355](https://img.fallensue.top/typora/image-20230904221541355.png)

### 题目分析

缓冲区是临界资源

$P_1,P_2,P_3$之间存在互斥关系，同时只能有一个进程访问缓冲区

$P_1,P_2$因奇数的存放与取出存在同步关系

$P_1,P_3$因偶数的存放与取出存在同步关系

```shell
semaphore mutex=1; //信号量用于互斥访问缓冲区
semaphore odd=0; //用于奇数放入取出同步
semaphore even=0; //用于偶数放入取出同步
semaphore empty=N; //缓冲区剩余空位个数

process P1()
while(1)
{
	x=produce(); //生成一个数
	P(empty); // 查看是否有空位
	P(mutex);
	Put(); // 放入数
	V(mutex);
	if(x%2==0)
		V(even); // 数为偶数，偶数信号量增加
	else
		V(odd); // 数为奇数，奇数信号量增加
}

process P2()
while(1)
{
	P(odd); // 检查是否有奇数
	P(mutex);
	getodd(); // 取数
	V(mutex);
	V(empty); //取数完成释放一个缓冲区位置
	countodd(); // 统计奇数数量
}

process P3()
while(1)
{
	P(even); // 检查是否有偶数
	P(mutex);
	geteven(); // 取数
	V(mutex);
	V(empty); // 取数完成释放一个缓冲区位置
	counteven(); // 统计偶数数量
}
```

## 2011（银行取号叫号问题）

![屏幕截图 2023-09-04 223419](https://img.fallensue.top/typora/屏幕截图 2023-09-04 223419.png)

![屏幕截图 2023-09-04 223438](https://img.fallensue.top/typora/屏幕截图 2023-09-04 223438.png)

### 题目分析

互斥资源：取号机（一次仅允许一位顾客使用），定义一个互斥信号量mutex

同步：顾客需要获取空座位来等待叫号，营业员空闲时将选取顾客进行服务。空座位数量决定等待顾客数量，有等待顾客服务员才能够提供服务。分别设置empty与full来实现同步关系。顾客在获得空座位之后，需要等待叫号服务，顾客与服务员之间也构成同步关系，定义一个信号量service来实现过程

```shell
semaphore mutex=1; //互斥使用叫号机
semaphore empty=10; //空座位数量
semaphore full=0; //等待顾客数量
semaphore service=0; //等待叫号

cobegin
{
	process 顾客i
	{
		P(empty); //找空位
		P(mutex); //申请使用取号机
		从取号机获取一个号码;
		V(mutex); //完成取号
		V(full); //告诉营业员有新顾客
		P(service); //等待叫号
		获取服务;
	}
	process 服务员
	{
		while(TRUE)
		{
			P(full); // 查看是否有顾客可以服务
			V(empty); // 顾客释放一个座位
			V(service); // 叫号
			为客户服务;
		}
	}
}
```

## 2013（博物馆进出问题）



![屏幕截图 2023-09-04 233940](https://img.fallensue.top/typora/屏幕截图 2023-09-04 233940.png)

![屏幕截图 2023-09-04 233958](https://img.fallensue.top/typora/屏幕截图 2023-09-04 233958.png)

### 题目分析

互斥：出入口的进出是互斥的，一次只允许一个人通过，设置互斥变量mutex

博物馆最多容纳500人，设置信号量empty，初值为500

```shell
semaphore mutex=1; //出入口互斥
semaphore empty=500; //博物馆可以容纳的最多人数

cobegin
参观者进程i:
{
	...
	P(empty); //可容纳人数减1
	P(mutex); //互斥使用门
	进门;
	V(mutex);
	参观;
	P(mutex); //互斥使用门
	出门;
	V(mutex);
	V(empty); //可容纳人数增1
	...
}
```



## 2014（生产者消费者问题）

![image-20230904235155005](https://img.fallensue.top/typora/image-20230904235155005.png)

### 题目解析

**临界资源**：缓冲区

**互斥资源**：在一个消费者进程从缓冲区连续取出10件产品之后，其他的消费者进程才可以取产品，设置互斥信号量`mutex1`用于控制消费者取产品的互斥关系。生产者与消费者对缓冲区的访问也是互斥的，所以对这个访问设置一个互斥信号量`mutex2`

**同步关系**：生产者和消费者进程共享一个可以存1000个产品的缓冲区，设同步信号量`empty=1000`，代表有几个空闲缓冲单元；设同步信号量`full=0`，代表此时缓冲区的产品数

伪代码如下：

```shell
semaphore mutex1=1,mutex2=1; //mutex1控制消费者取产品的互斥关系,mutex2控制生产者与消费者对缓冲区的访问
semaphore empty=1000,full=0; //empty代表缓冲区中剩余空闲个数，full代表缓冲区的产品数

producer()
{
	while(1)
	{
		生产一个产品;
		P(empty); //判断缓冲区是否有空位
		P(mutex2);
		把产品放入缓冲区;
		V(mutex2);
		V(full); //缓冲区产品数加一
	}
}

consumer()
{
	while(1)
	{
		P(mutex1); //互斥信号量实现一个消费者进程一次完整取产品周期内对缓冲区的控制
		for(int i=0;i<10;i++)
		{
			P(full); //判断缓冲区是否有产品
			P(mutex2);
			从缓冲区取出一件产品;
			V(mutex2);
			V(empty); //取出产品之后腾出空位
		}
		V(mutex1); //完成一个周期，释放互斥信号量
	}
}
```

## 2015（信箱辩论问题）

![屏幕截图 2023-09-05 141341](https://img.fallensue.top/typora/屏幕截图 2023-09-05 141341.png)

![屏幕截图 2023-09-05 141353](https://img.fallensue.top/typora/屏幕截图 2023-09-05 141353.png)

### 题目解析

**互斥：**辩论者们对自己和对方的信箱的访问是互斥的，设置mutexa与mutexb用于控制两个信箱的互斥访问

**同步关系**：信箱不为空时，辩论者才能从信箱中取邮件，否则等待。当信箱不满时，辩论者才能将新邮件放入信箱，否则等待。设置emptya和fulla表示A辩论者的信箱中的空余位置与剩余信件数，设置emptyb与fullb表示B辩论者的信箱中的空余位置与剩余信件数。

伪代码:

```shell
semaphore mutexa=1,mutexb=1;
semaphore emptya=M-x,emptyb=N-y;
semaphore fulla=x,fullb=y;

A()
{
	while(1)
	{
		P(fulla); //查看自己的信箱有无邮件
		P(mutexa); // 互斥访问信箱
		查看问题，写答案;
		V(mutexa);
		V(emptya); //取出了信件，释放空位
		
		P(emptyb); //准备将信件放入B的邮箱
		P(mutexb); 
		放信;
		V(mutexb);
		V(fullb); //放入信件
	}
}

B()
{
	while(1)
	{
		P(fullb); //查看自己的信箱有无邮件
		P(mutexb); // 互斥访问信箱
		查看问题，写答案;
		V(mutexb);
		V(emptyb); //取出了信件，释放空位
		
		P(emptya); //准备将信件放入A的邮箱
		P(mutexa); 
		放信;
		V(mutexa);
		V(fulla); //放入信件
	}
}
```

## 2017（复数计算）

![屏幕截图 2023-09-05 143051](https://img.fallensue.top/typora/屏幕截图 2023-09-05 143051.png)

![屏幕截图 2023-09-05 143104](https://img.fallensue.top/typora/屏幕截图 2023-09-05 143104.png)

### 题目解析

题目中说到**最大程度并发**

* `thread1`：对`x,y`读操作
* `thread2`：对`y,z`读操作
* `thread3`：对`y,z`进行读操作和写操作

**同步关系**：不存在（没有规定线程执行顺序的先后）

**互斥关系**：**读操作不存在互斥关系**，所以`thread1`与`thread2`不互斥；`thread1`与`thread3`互斥访问`y`，`thread2`与`thread3`互斥访问`y,z`；因此需要设置两个信号量实现对变量`y`的互斥访问，否则`thread1,thread2`之间对`y`的读操作也会被锁

伪代码：

```shell
semaphore mutex_y1=1; //1,3对y的互斥访问
semaphore mutex_y2=1; //2,3对y的互斥访问
semaphore mutex_z=1; //2,3对z的互斥访问

thread1()
{
	cnum w;
	P（mutex_y1); //1,3互斥访问y
	w=add(x,y);
	V(mutex_y1);
	...
}

thread2()
{
	cnum w;
	P(mutex_y2); //2,3互斥访问y
	P(mutex_z); //2,3互斥访问z
	w=add(y,z);
	V(mutex_z);
	V(mutex_y2); 
	...
}

thread3()
{
    cnum w;
    w.a=1;
    w.b=1;
 
    p(mutex_z);//2,3互斥访问z
    z=add(z,w);
    v(mutex_z);
 
    p(mutex_y_1);//1,3互斥访问y,千万别落下了
    p(mutex_y_2);//2,3互斥访问y
    y=add(y,w)
    v(mutex_y_2);
    v(mutex_y_2);
    ...
}
```

## ⭐2019（哲学家就餐问题）

![屏幕截图 2023-09-05 150346](https://img.fallensue.top/typora/屏幕截图 2023-09-05 150346.png)

### 题目解析

哲学家进餐问题强化版，即碗的数量不再是一个哲学家对应一个碗，而是需要“抢”碗用，不仅仅需要抢筷子。

#### ⭐解法一：限制最多可以访问资源的人数

```shell
semaphore capacity=n-1; //表示最多有n-1个哲学家可以争抢资源
semaphore bowl=m; //碗
semaphore chopsticks[n]={1}; //n个筷子
for(int i=0;i<n;i++)
{
	chopsticks[i]=1; //每个哲学家一侧筷子个数为1
}

Philosopher()
{
	while(1)
	{
		思考;		
		P(capacity);			//最多有n-1个人可以对资源进行访问
		P(bowl);					//申请碗
		P(chopsticks[i]);		//左手筷子
		P(chopsticks[(i+1)%n]);	//右手筷子
		恰饭;
		V(bowl);
		V(chopsticks[i]);
		V(chopsticks[(i+1)%n];
		V(capacity);
	}
}
```

#### 解法一优化

可以使用碗的数量`m`来限制访问的人数，也就是`bowl`这个信号量既充当资源，又起到了限制访问人数的作用。需要注意的是`bowl`的数量一定要小于`n`

```shell
semaphore bowl; //碗
semaphore chopsticks[n]={1}; //n个筷子
for(int i=0;i<n;i++)
{
	chopsticks[i]=1; //每个哲学家一侧筷子个数为1
}
bowl = min(m,n-1); //限制访问资源的人数最多为n-1

Philosopher()
{
	while(1)
	{
        思考;
        P(bowl); //取碗
        P(chopsticks[i]); //左手筷子
        P(chopsticks[(i+1)%n]); //右手筷子
        恰饭;
        V(chopsticks[i]);
        V(chopsticks[(i+1)%n]);
        V(bowl);
	}
}
```

#### ⭐解法2：互斥访问资源（上锁）

**仅当一位哲学家左右两边的筷子都可用时，才允许他抓起筷子**

> 这种策略是对哲学家申请资源的整个过程进行一个“上锁”，也就是说当哲学家们只能互斥地进行申请自愿的过程，这样就保证了第一个哲学家一定是可以申请到所有的资源

```shell
semaphore lock = 1;				//用以互斥申请资源的信号量
semaphore bowl = m；			//碗
semaphore chopsticks[n] = {1};	//n个筷子，
for(int i = 0; i < n; i++)
	chopsticks[i] = 1;			//每个哲学家一侧筷子个数为1

Process Philosopher()
{
	while(1)
	{
		思考;
		P(lock);				//"上锁"
		P(chopsticks[i]);		//左手筷子
		P(chopsticks[(i+1)%n]	//右手筷子
		P(bowl);				//取碗
		V(lock);
		恰饭;
		V(chopsticks[i]);
		V(chopsticks[(i+1)%n];
		V(bowl);
	}
}

```

#### 解法2优化

> 在上一个解法当中，如果除了第一个哲学家以外的其他哲学家进程执行完申请左手筷子之后发生进程调度，并发执行了一圈之后，就会出现只有第一个哲学家手里拿着能够吃饭的资源这种情况（最坏），而其他哲学家都只有左手拿了一根筷子。这与题目要求的“让尽可能多的哲学家同时就餐”不符。因此考虑如下优化，上锁的同时判断哲学家是否能够申请到所有的资源，如果能就拿，否则就不申请。

```shell
int n = 10;                 //筷子数
int m = 10;                 //碗数
semaphore lck = 1;			//取资源的信号量
bool chopsticks[n];         //所有的筷子状态，false为未取走
int bowls = 0;              //当前已分配出去的碗数
for(int i = 0; i < n; i++)
	chopsticks[i] = false;	//初始化所有的筷子为未被拿走

Progress Philosopher(int i)	//第i号哲学家
{
    while (true)
    {
    	//思考
        while(true)
        {
            P(lck);		//保证同一时刻只有一个哲学家在尝试取走
            if (!chopsticks[i] && !chopsticks[(i + 1) % n] && bowls < m)//能够拿到所有资源才会进行申请
            {
                chopsticks[i] = true;           //取走左手筷子
                chopsticks[(i + 1) % n] = true; //取走右手筷子
                bowls++;                        //取碗
                V(lck)
                break;
            }
            V(lck);
        }

        //干饭

        P(lck);                             //互斥访问数组
        chopsticks[i] = false;              //放下左手筷子
        chopsticks[(i + 1) % n] = false;    //放下右手筷子
        bowls--;                            //释放碗
        V(lck);
    }
}

```

## 2020（进程同步）

![屏幕截图 2023-09-05 154100](https://img.fallensue.top/typora/屏幕截图 2023-09-05 154100.png)

### 题目解析

该题目流程不存在互斥关系

**同步关系**：`C`的完成需要`A,B`都完成，`E`的完成需要`C、D`的完成，存在先后次序，设置四个信号量`A,B,C,D`用于保证有序进行

伪代码：

```shell
semaphore a=0,b=0,c=0,d=0; //信号量为0说明A,B,C,D操作未执行完成

A()
{
	...
	执行A操作;
	V(a); //通知A操作已完成
}

B()
{
	...
	执行B操作;
	V(b); //通知B操作已完成
}

C()
{
	...
	//检查是否已执行A操作和B操作
	P(a);
	P(b);
	执行C操作;
	V(c); //告知C操作已执行完
}

D()
{
	...
	执行D操作;
	V(d); //告知D操作已执行完
}

E()
{
	...
	//检查是否已执行C操作和D操作
	P(c); 
	P(d);
	执行E操作;
}
```

## 2021（分析同步互斥）

![屏幕截图 2023-09-05 155127](https://img.fallensue.top/typora/屏幕截图 2023-09-05 155127.png)

### 题目解析

（1）

> 信号量S是可以被多个进程共享的变量，多个进程都可通过wait()和signal()对S进行读、写操作。所以，wait()和signal操作中对S的访问必须是互斥的。

（2）

> 方法1错误。在wait()中，当S≤0时，关中断后，其他进程无法修改S的值，while语句陷入死循环。
>
> 方法2正确。方法2在循环体中有一个开中断操作，这样就可以使其他进程修改S的值，从而避免while语句陷入死循环。

（3）

> 不能，因为开中断和关中断都是特权指令，不能在用户态下执行，只能在内核态下执行。

## 2022（分析同步互斥）

![image-20230905160631017](https://img.fallensue.top/typora/image-20230905160631017.png)

### 题目解析

进程`T1`需要执行`A、E、F`，进程`T2`需要执行`B、C、D`。由图可知，`T2`执行C需要在`T1`执行`A`之后；T1执行E需要在T2执行C之后。因此存在两对同步关系。

**伪代码：**

```shell
semaphore a=0,c=0; //a:描述A、C之间的同步关系;c:描述C、E之间的同步关系
A()
{
	执行A操作;
	V(a);
	P(c);
	执行E操作;
	执行F操作;
}

B()
{
	执行B操作;
	P(a);
	执行C操作;
	V(c);
	执行D操作;
}
```
