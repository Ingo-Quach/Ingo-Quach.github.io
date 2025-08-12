---
abbrlink: ''
categories:
- - 大学生涯
cover: https://cdn.jsdelivr.net/gh/Ingo-Quach/webTuChuang@main/Qexo/25/8/OS同步与互斥part1_703c6c3906a2a55fc008a9e563ca802b.png
date: '2025-08-12T22:02:00.618872+08:00'
tags:
- 操作系统
title: 操作系统学习笔记 —— 同步与互斥 Part I
top_img: https://cdn.jsdelivr.net/gh/Ingo-Quach/webTuChuang@main/Qexo/25/8/OS同步与互斥part1_703c6c3906a2a55fc008a9e563ca802b.png
updated: '2025-08-12T22:04:34.546+08:00'
---
## 互斥与同步

### 进程同步

* 同步（synchronization）：
  * 多个相互合作的进程在一些关键点上可能需要互相等待/互相交换信息，这种相互制约关系称为进程同步
  * 不确定中蕴含确定性

![](https://cdn.jsdelivr.net/gh/Ingo-Quach/webTuChuang@main/Qexo/25/8/进程同步_a6e42be65493c088d7b12f6c8e108f3b.png)

* 互斥（mutal exclusion）：
  * 当一个进程正在使用某资源时，其他希望使用该资源的进程必须等待
  * 当带进程用完资源并释放后，才允许其他进程其访问此资源

### 临界资源与临界区

* 临界资源：一段时间内仅允许一个进程使用的资源，EX：共享变量
  * 四个部分：
    * 进入区：检查临界资源访问状态，若可以访问则设置临界资源被访问状态
    * \*临界区
    * 退出区：清除临界资源被访问状态
    * 剩余区：其他部分
* 临界区：进程中访问临界资源的那段代码
* 同类临界区：所有与同一临界资源相关联的临界区

#### 访问临界资源应遵循的原则

1. 空闲让进
   1. 若无进程处于临界区，应允许一个进程进入临界区（一次至多允许一个进程进入）
2. 忙则等待
   1. 当已有进程进入临界区，其他试图进入的进程应该等待
3. 有限等待
   1. 应保证要求进入临界区的进程在有限时间内进入临界区。也蕴含有限使用的意思。
      * 使用资源的进程不能无限使用下去，不能一直等待
4. 让权等待
   1. 当进程不能进入自己的临界区时（也就是无法完成自己的任务），应释放处理机，不至于造成饥饿/死锁

### 软件实现临界区互斥

`turn` 指示允许进入临界区的进程标识

`flag[i]` 表示进程 i 是否计划进入临界区，初始值为 0

#### \*Dekker 算法

**参数说明**：* 两个全局共享的状态变量`flag[0]`和`flag[1]`，表示临界区状态及哪个进程想要占用临界区，初始值为0。

* 全局共享变量`turn`（值为1或0）表示能进入临界区的进程序号，初始值任意，一般为0。

**算法原理**：* 设有进程 `P0` 和 `P1`，两者谁要访问临界区，就让对应的`flag = true`（例如 P0 要访问临界区，就让flag[0]=true），相当于“举手示意我要访问”。初始值为0表示一开始没人要访问。

* `trun`用于标识当前允许谁进入，turn=0则P0可进入，turn=1则P1可进入

![](https://cdn.jsdelivr.net/gh/Ingo-Quach/webTuChuang@main/Qexo/25/8/Dekker%20%E7%AE%97%E6%B3%95%20Part%20I_1bb6e2d8502a6d3658c2c3a3dbd1c487.png)


![](https://cdn.jsdelivr.net/gh/Ingo-Quach/webTuChuang@main/Qexo/25/8/Dekker%E7%AE%97%E6%B3%95%20Part%20II_8fc390c86c10bbf7b680f099974551a6.png)

#### \*Perteson 算法

![](https://cdn.jsdelivr.net/gh/Ingo-Quach/webTuChuang@main/Qexo/25/8/Perteson%20%E7%AE%97%E6%B3%95_0eaf0d9048c33e2be3e4ce088e20e58f.png)

### 硬件实现临界区互斥

#### 关中断/中断屏蔽

* 中断请求被忽视，处理器不会对中断信号进行相应
* 能保证当前运行进程将临界区代码顺利执行完，从而保证了互斥的正确实现，然后再允许中断
  * 也就是说：当前进程不会被终端，因此必然不会发生进程切换
* 中断响应将延迟到中断启用之后
* 效率问题：
  * 如果临界区执行工作很长，则无法预测中断响应的时间
  * 系统将处于暂停状态，无法响应事件
  * 限制了处理机交替执行程序的能力、执行的效率会明显降低
* 适用范围问题：
  * 不一定适用于多处理器计算机系统
  * 一个处理器关掉终端，并不意味着其他处理器也关闭终端，不能防止进程进入其他处理器执行临界代码
* 安全性问题：
  * 一个进程关中断后不再开中断，可能导致系统的终止和崩溃

#### TestAndSet

* 用硬件实现，执行的过程不允许被中断，只能一气呵成

![](https://cdn.jsdelivr.net/gh/Ingo-Quach/webTuChuang@main/Qexo/25/8/TestAndSet_53a501c8744977aa39a9d9408a8fefc3.png)

#### Exchange/Swap

* 用硬件实现，执行的过程不允许被中断，只能一气呵成
  ![](https://cdn.jsdelivr.net/gh/Ingo-Quach/webTuChuang@main/Qexo/25/8/Exchange&Swap_b6a562f49409de234d8225ae63219946.png)
