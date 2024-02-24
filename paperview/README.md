# CS345

comment: 

1.  不要说 bad, 说  your scheme would be stronger if it dealt with case X .
2.  可以说某些assumption 不成立,  insufficient evalution  ,  instances where solution不能work,  一些部分难以理解
3.  可以写出他调查了哪些数据, 提供了哪些数据. 
4.  可以缩句, 就把他的话简化一下, 去掉废话.

建立一个自己的reading list,  是一个queue, 看到想读的就放进去,  然后有时间了就读.

presentation,可以参考vl2 uiuc 的slide.

### arrakis

14年osdi, simon还是postdoc @uw, 开场说如果有工作请联系我, 现在已经是ap了.

109行就能改 redis use persistent log. 

It does not compare with RDMA, a classic method of Kernel Bypassing

别人的评论: 系统没有解释在使用中断时如何消除内核交叉的性能开销，也没有详细说明中断和硬件虚拟化如何协同工作.  从十年后的角度来看，这篇文章应该是内核旁路的一个很好的例子，反映了近年来高性能网络领域的一个显著趋势：将内核的工作转移到硬件和用户空间。今天流行的 eBPF 和 XDP 可以看作是这个想法的延续。这项工作扎实，思维清晰，并取得了明显的性能改进。 缺点是内核绕过的经典实现是让用户空间程序轮询用户模式驱动程序以获取信号，而不是使用中断，因为中断通常会产生开销。然而，这项工作仍然使用中断，并声称系统不会因为内核交叉而产生大量的开销，但本文没有解释这是如何实现的。特别是考虑到使用中断的目的是暂停待处理的程序，以便为其他程序提供 CPU 周期，这通常是由内核完成的。因此，中断往往会陷阱到内核来完成程序之间的调度，因此消除这种内核交叉并不是一件简单的事情。

中断, 需要上下文切换, 进入kernel space, 清空cache , 所以比polling latency高. 

抽象，就是一个公式，可以表示多个公式。 一个类可以表示多个类 ，狗，鸡。

这个论文优秀在于还讲了scalability的 stop point，core为6的时候开始性能下降，讲了自己的弱点，解释了为什么. 

弱点，两个应用同时发的时候会严重冲突。

libix，需要改程序。arrais是透明的。
现在数据中心很多数据都存在内存中。

rdma1993年提出，2000有inifiteband，2010有roce，更好用更可靠更便宜。

###  FaRM: Fast Remote Memory

NSDI14, 亮点是 lay on top of transcation.

RDMA 需要固定的区域内存. 原因: 

1. 需要pin memory 防止被swap. 
2. security 问题.  exchange memory key. 

已有的existing large page support 都不够. 所以他们  implemented PhyCo, a kernel driver that allocates 2GB memory. 所以就每个region只需要一个entry, 也提出了新hashing algorithm. 但是这些都不是最重要的. 这些只是实现目标的新方法. 

#### 背景

In cast,  queue很多消息, 可能延迟很高.

priority flow control (PFC).  缺点是 head of line blocking, deadlocks.  

RoCE 怎么避免 drop?  PAUSE frames

别人的评论: 

可能的缺点: \- 在引导时分配的 2 GB 页面可能会浪费大量内存。 - 基于轮询的实现来提高吞吐量可能会浪费资源，并且当其他 CPU 需要额外的周期时，会成为争用的根源。 - 作者只试验了 16 字节的密钥和 32 字节的值。 - 某些实验可以从增加集群中的计算机数量中受益。

实验仅使用 16 字节密钥和 32 字节值完成，但有趣的是，当使用更大的密钥和值时，降低的 RDMA 性能将如何与 TCP/IP 相抗衡，以及是否会有某种摊销模式发挥作用。 - 一些实验可以从增加集群中的机器数量中受益，因为在什么时候 FaRM 的可扩展性将继续增加之前并不明显。这些示例包括图 12 和图 13。

Weaknesses

\- FaRM does not support swapping data between memory and disk, which requires massive memory since all the local data has to reside in memory. - SSD might become bottlenecks for certain types of workload - Lacks discussion about RDMA atomic operations

### eRPC

作者是cmu phd和intel labs, 现在去 微软cto办公室搞5G, 所以大部分人毕业后都是干不一样的工作. best paper, 之前被sigcomm拒了. 

#### 挑战1

managing packet loss, 之前的硬件要用PFC, infiniband来保证 lossless link layer. 

switch 的buffer  有几十MB ,这些是on chip ram 因为有latency要求,      大小 >> BDP. 

#### 挑战2  congest

之前用NIC offload, 但是不够灵活. 他们就只优化uncongest network.  RTT-based 拥塞控制.

他们引用一个84年的论文,  认为可以在high level 达到更好的性能.

Assumption:  大部分的rpc 都可以放在一个small packet里, 他们优化的就是这个场景.  他们用数据证明这个场景是常见的. 从而可以提高end to end的性能. 

#### 评估

第七部分, 用raft, 还有masstree, 来评估实际应用下的性能表现.  

他们后来又用DPDK implement了一些message passing组件. 

别人的评论缺点: 

- Require users to modify their source code to adopt ownership-aware APIs. 

- Non-preempt scheduling and polling may waste CPU cycles and energy. 
- The throughput of large transfers is still lower than RDMA Write. 
- Cannot tolerate high loss rate.

### StRoM

浙大王泽可老师(二作)和eth 发的.

在datacenter, 一般用DCTCP.提前减少sending rate before buffer fill up.





#### homa

https://nan01ab.github.io/2018/09/Homa-Transport-Protocol.html  写的很好. 

insight

1. short message 优先级bypass先过, low latency.
2. recevier动态分配优先级更好. 为什么? 
3. receiver 同时请求多个sender.可以提高利用率. 

为啥receiver能决定呢? 



别人的缺点: 

\- Evaluate on a relatively slow network. This solution might suffer from CPU performance when moving to a faster network as message scheduling is done by the CPU. - This solution may impose some restrictions on the network topology and packet loss rate.



### zeus

发现能耗和性能优化之间有非线性tradeoff，然后提出了一个[optimizer](https://www.zhihu.com/search?q=optimizer&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"664765417"})来自动做这个tradeoff，它会在线profiling以调整batch size和GPU的功率限制。
