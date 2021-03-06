---
layout: post
title: 分布式系统（1）-简介
date: 2019-08-29
tag: [summary]
---

## 定义与特性
> 分布式系统是若干独立自主计算机的集合，这些计算机对于用户来说像单个耦合系统。

**物理分布，逻辑集中；个体独立，整体统一**

特性：
* 自主性：计算节点硬件或软件进程是独立的
* 耦合性：用户或者应用程序感觉系统是一个系统——节点之间需要相互协作
* 构成组件并被所有用户共享
* 系统资源可能不允许访问
* 软件运行在不同处理器上的多个并发进程上
* 允许多点控制
* 允许多点失效

自主节点集合
* 节点独立行为
	- 每个节点都是独立的，有自己的本地时间
	- 没有全局锁
	- 存在基本的同步和协同的问题
* 节点集合行为
	- 如何管理集合中的节点之间的关系？开放集合、封闭集合
	- 如何知道确实是在跟一个授权（非授权的）成员通信？信任、安全机制

一致(coherent)系统
* 本质：节点无论在什么地方，用户无论何时访问，节点集合对于用户来讲都是一个整体
* 例子
	- 终端用户不知道计算发生在什么地方
	- 用户也不知道与应用相关的数据存储在什么地方
	- 数据拷贝完全是隐藏的（核心是分布式透明性）
* 挑战
	- 部分失效：不可避免地，分布式系统的某一部分会失效，部分失效以及恢复很难做到对用户的透明性

## 分布式系统中的8个谬误
1. 网络是可靠的
2. 延迟为0
3. 带宽无限
4. 网络安全
5. 网络拓扑结构不会变
6. 唯一管理员
7. 传输代价为0
8. 网络是同构的（对等实体？）

## 分布式系统、并行系统、集中系统
* 分布式系统与并行系统交叉：MPI/MapReduce
* 并行系统与集中系统交叉：OpenMP/TBB

相对于分布式系统，集中式系统的特性如下：
* 仅由单一组件构成
* 单个组件被用户一直占用
* 所有资源都是可访问的
* 软件运行在单个进程中
* 单点控制
* 单点失效

分布式系统与并行系统的区别：主要在于软件
* 并行系统各结点工作都是一样的（平行结构），软件也相同
* 但分布式系统是一个复杂的拓扑结构（树型结构），每个节点软件不必相同，可以异构

## 分布式系统的目标
**对外是一个整体，从而要使分布式系统具有一定的特性**！
* 使资源可访问：让用户方便访问资源
* 透明性：隐藏资源在网络上的分布
	- 访问、位置、迁移、重定位、复制、并发、故障、持久化
* 开放性：访问接口的标准化，封闭无法跨越不同组织
	- 良好定义的接口、互操作性、可移植性、易实现可扩展性
* 可扩展性：系统在规模、地域、管理上的可扩展性

### 透明性
完全透明性是不可取的也是难以实现的，主要因为
* 可能隐含通信的性能问题
* 完全隐藏网络和节点的失效是不可能的
	- 不能区分失效和性能变慢的节点
	- 不能确定系统失效之前的操作是什么
* 完全的透明性可能牺牲性能，暴露系统分布特征
* 保证复制节点与主节点的一致性需要时间（一致性问题）
* 为了容错需要立即将内存修改的内容同步到磁盘上

暴露系统的分布特征有一定使用场景
* 利用基于位置的服务（如：找到附近的朋友）
* 当与不同时区的用户交互时
* 当让用户理解系统发生了什么时，如当一台服务器不响应时，报告失效

结论是分布式透明性是一个较好的属性，但是需要区别对待。

### 开放性
系统根据一系列准则来提供服务，这些准则描述了所提供服务的语法和语义（标准化）
* 系统应该具有良好定义的接口
* 系统应该容易实现互操作性
* 系统应该支持可移植性
* 系统应该容易实现可扩展性

重点：**策略与机制分离** e.g.配置文件
* 实现开放性：策略（具体的实施或实现，能够设置具体是多少）
	- 需要为客户端的缓冲数据设置什么级别的一致性?
	- 我们允许下载的程序执行什么操作?
	- 当出现网络带宽波动的时候如何调整QoS需求？
	- 通信的安全水平设置多高？
* 实现开放性：机制（有/没有）
	- 允许动态设定缓冲策略
	- 支持为移动代码设置不同的信任级别
	- 为每个数据流提供可调整的QoS参数
	- 提供不同的加密算法
* 策略和机制之间的平衡
	- 策略和机制之间分离的越严格，越需要设计合适的机制，这样会导致出现很多配置参数和复杂的管理
	- 硬编码某些策略可以简化管理，减少复杂性，但是会导致灵活性降低

### 可扩展性
* 规模可扩展性：用户数量和进程数量增加
	- 计算容量受到CPU性能的限制
	- 存储容量局限，包括CPU与磁盘之间的传输速率，存在单点的瓶颈/单点失效
	- 网络局限：用户与集中服务之间的网络带宽
* 地理可扩展性：节点之间的最大物理位置
	- 不能简单从LAN扩展到WAN：很多分布式系统假设客户端-服务器之间的交互是同步的，即客户端发送请求等待结果。广域环境中的延迟问题限制扩展性
* 管理可扩展性：管理域的数量（访问控制系统、调度规则）
	- 计算网格、共享设备
	- 文件共享系统(BitTorrent)、P2P电话(Skype)、基于P2P的音频流数据(Spotify)

### 形式化分析
排队论：M/M/1
* M：到达符合Markov（泊松/指数），到达率$$\lambda$$
* M：处理符合Markov，服务率$$\mu$$
* 1：进程数目

最终会达到一个稳态（进出一样），系统中包含$$k$$个请求的概率
$$\mu p_1=\lambda p_2, p_2\lambda+p_3\mu=\lambda p_3+\mu p_2, \ldots$$
可推得
$$p_k=(1-\frac{\lambda}{\mu})(\frac{\lambda}{\mu})^k$$

最终平均吞吐为$$\lambda$$

### 扩展技术
隐藏通信延迟：尽量避免等待原程服务对请求的响应->解决地理可扩展性
* 异步通信
* 设计分离的响应消息处理器（分布）
* 但不是所有应用都满足这种模式
* 某些交互式应用的用户发出请求后，处于等待的状态

分布：在多个机器上划分数据和计算

复制和缓存：在多个不同的机器上创建多个数据副本
* 复制文件服务器和数据库
* Web站点进行镜像
* Web缓存：在浏览器或者代理位置
* 文件缓存

复制存在的问题：一致性问题，如果可以容忍不一致，那么可以减少全局同步，但这由应用程序决定

## 不同的分布式系统
三种类型分布式系统：多核、多处理器、多计算机

分布式共享内存现在已经很少用了

* <u>集群</u>计算系统：**同构**（相同OS，近乎相同的硬件），单个管理节点
* <u>网格</u>计算系统：**异构**，包含多个组织，容易扩展到广域网环境；为了允许合作，网格通常利用虚拟组织
* 云计算

事务系统(EAI) + 事务处理监控器(TPM)
* 中间件为系统集成提供通信设施
* RPC：请求通过本地调用发出，结果以函数返回值方式接收
* 面向消息的中间件(MOM)：Pub, Sub

## 分布式系统面临的挑战
* 系统设计：正确的接口设计和抽象，如何划分功能和可扩展性
* 一致性：如何一致地共享数据
* 容错：如何保障系统在出现失效情况下正常运行
* 不同的部署场景：集群、广域分布、传感网络
* 实现：如何最大化并行，性能瓶颈是什么，如何平衡负载