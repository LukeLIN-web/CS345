对论文comment: 

1.  不要说 bad, 说  your scheme would be stronger if it dealt with case X .
2.  可以说某些assumption 不成立,  insufficient evalution  ,  instances where solution不能work,  一些部分难以理解
3.  可以写出他调查了哪些数据, 提供了哪些数据. 
4.  可以缩句, 就把他的话简化一下, 去掉废话.
5.  一般找weakness就挑实验最好找, 比如哪些消融实验没做, 哪些变量的影响没有测. 

建立一个自己的reading list,  是一个queue, 看到想读的就放进去,  然后有时间了就读.

presentation,可以参考vl2 uiuc 的slide. 应该要引入讨论.  应该要分析每一张图. 

读 companion paper 然后也对比一下. 列一个表对比 

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

可能的缺点: \- 在引导时分配的 2 GB 页面可能会浪费大量内存。 - 基于轮询的实现来提高吞吐量可能会浪费资源，并且当其他 CPU 需要额外的周期时，会成为争用的根源。 - 作者只试验了 16 字节的密钥和 32 字节的值。 - 某些实验可以从增加集群中的计算机数量中受益。

实验仅使用 16 字节密钥和 32 字节值完成，但有趣的是，当使用更大的密钥和值时，降低的 RDMA 性能将如何与 TCP/IP 相抗衡，以及是否会有某种摊销模式发挥作用。 - 一些实验可以从增加集群中的机器数量中受益，因为在什么时候 FaRM 的可扩展性将继续增加之前并不明显。这些示例包括图 12 和图 13。

Weaknesses

\- FaRM does not support swapping data between memory and disk, which requires massive memory since all the local data has to reside in memory. - SSD might become bottlenecks for certain types of workload - Lacks discussion about RDMA atomic operations

### eRPC

Datacenter RPCs can be General and Fast

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

缺点: 

- Require users to modify their source code to adopt ownership-aware APIs. 

- Non-preempt scheduling and polling may waste CPU cycles and energy. 
- The throughput of large transfers is still lower than RDMA Write. 
- Cannot tolerate high loss rate.

### StRoM

浙大王泽可老师(二作)和eth 发的.

在datacenter, 一般用DCTCP.提前减少sending rate before buffer fill up.

#### homa

https://nan01ab.github.io/2018/09/Homa-Transport-Protocol.html  写的很好. 

要思考, 为什么之前的解决方法行不通?  一开始都是复现, 发现有的复现不出来, 就有问题. 

有14个匿名reviewer, 说明至少1-2次被拒绝了.因为一次大概7个reviewer.

作者是斯坦福的John ousterhout.  他女儿是ucsd教授 amy ousterhout

insight

1. short message 优先级bypass先过, low latency.  
2. recevier动态分配优先级更好. 为什么? 
3. receiver 同时请求多个sender.可以提高利用率.  这是necessary evil.

为啥receiver能决定呢? 

他们就用simulation,没有真正的网络跑. 

Figure1 展示了 workload, 但是没说有多少request.

Figure4 , 不是一个CDF图,  展示了threshold, 

Figure8,非常好, 因为普通的CDF 显示不出来这个效果.  不过message 是request还是response还是加起来? 老师也不知道. 

evaluation 的第一部分问题很好, guide你做实验. 

5.2的simulation, 一个模拟器是ns-3. 用discrete event simulation.  Simpy 库. 

缺点: 

\- Evaluate on a relatively slow network. 没有在100GB 网络上. This solution might suffer from CPU performance when moving to a faster network as message scheduling is done by the CPU. - This solution may impose some restrictions on the network topology and packet loss rate.

他们的background和related work 有些重复冗余. 

他们还用了page limit.所以可能写的字数少了.  The Homa implementation contains a total of 3660 lines of C++ code, of which about half are comments.

他们还和ramcloud 比较了. 

#### Shenango

背景: 网络硬件进步了, 但是os kernel还没跟上. 

The simulator models an M/M/n/FCFS queuing system. 就是说一个泊松过程. n是server的数量.

Shenango is implemented in C and includes bindings for C++ and Rust.

work stealing:  在工作抢断调度程序中，计算机系统中的每个处理器都有一个要执行的工作项队列。可以steal别的core的task. 

用DPDK来访问nic queue. 除此以外, 没有用DPDK.

缺点: cache locality 会不会很差? 用2009年, 10Gb/s的NIC, 没有用最新的. 

cache affinity, enables the binding and unbinding of a process or a thread,  the process or thread will execute only on the designated CPUs rather than any CPU.

一作 Amy Ousterhout@ mit,  她爸爸John ousterhout是斯坦福的教授.   shinjuku是斯坦福的论文,所以amy可能听到了shinjuku的各种情况. 

#### How to diagnose nanosecond network latencies in rich end-host stacks

high overlead 有两个问题, 1. 不能apply to the whole stack,  2. Disturb the latency distribution.

what is 'swapper' process? if this process is running, the cpu is idling.

What if  ptp asks the SW clock to go back in time?  Do old timestamps need to be pushed back?

figure4, NIC delay真的是主要原因吗?  

可以写一写自己的问题, 问问大家讨论. 应该把每个图讲清楚. 实验能佐证论点吗? 哪些 实验没做?

weakness应该专注 问题本身的assumption不成立, 不focus  图画的不好,typo. 

挑战:

 cpu profiling 和NIC ptp的 time domains不一样.

keep track of meessage core.

solution: if child explains 80% deviation, blame child.

built on top of the intel-PT cpu profiler

extended linux NIC timestamp framework.

别人的看法:

\- Could not find source code of tool despite authors mentioning plans for open sourcing it. - Systems with core hand-off boundaries require patching. - User-space VMA network stack evaluation felt like an afterthought (likely due to space constraints).

the improvements in the experimental section seem very short-sighted: pinning applications to cores, limiting Memcached concurrency, etc. It seems that the solution is "overfitting" the experiment.

In my opinion, (most of) the graphs were poorly designed. To make a non-exhaustive list: - Figure 1: line for Intel-PT cannot be seen, range on the axis is unnecessarily broad (graph shows values well above 10k while nothing seems to happen after 1k); - Figure 4: graph is not informative, as the bars in the plot are all flat because of the employed scale. also, it is not immediately clear that the graph is a box plot and the red dots are outliers (the authors don't state it); - caption of Figure 5: "the reader can ignore the Y-axis". why report a figure wherein the reader should ignore some of the content? - figure 8: in the text the authors discuss that for some time the message is processed by core X and then by core Y, but I fail to see where this is shown in the picture; - figure 9: the figure is supposed to compare times between M1 and M2, but only times for M2 are shown. - in several plots, reported values overlap with lines and other graph elements, which makes such values hard to read (e.g. figure 18, the value corresponding to `Driver (A)`).

二作是老师的学生, 老师很失望, 觉得这些图做的太烂了.figure1的横线甚至不是直线,是有slit的. 

#### smartnic ipipe

https://courses.engr.illinois.edu/ece598hpn/fa2020/slides/lect23-socNICs.pdf 有讲解

\- 缺乏性能评估，不允许 iPipe 将工作负载卸载到 SmartNIC，这意味着主机的 CPU 会处理所有工作负载。 - 该解决方案似乎依赖于平台，因为它依赖于特定型号的 SmartNIC 提供的独特功能。

第一个nic 是70年代生产的.

scheduling 是centralized single queue还是 decentralized?

smartnic上用 centralized single queue, host上用 decentralized ,multiple queue. 

 每个application写了1500-2300 LOC.

Weakness: 

1. 用core 的数量来说明 CPU usage大小, 但是没有测量 core 的utilization.

2. evaluation, 没有对比 纯host运行效率. 

actor模型 优缺点是啥? 

优点: 清晰的编程模型. 

缺点: application 写起来麻烦, 需要重新写. 

#### enso

Introduction 写的非常好. 

传统的NIC缺点,  内存访问不佳, metadata overhead大.

其实就是提出了一个ring buffer, 在figure3.

挑战:

1. NIC和application之间的coordination
2. 多久notificate一次 

 reactive notifications,让软件控制通知频率,   还提出了notification prefetching mechanism.

为什么不用中断? 因为 PCIe 的RTT很小.

userspace lib 用来request notification buffers and 创建Enso pipes

思考题: kernel module有啥用? 

用更小的PCIe bandwidth达到更高的goodput.

E810 支持4.0 接口, 但是cpu实际上是用3.0的. 所以2个core以上, pcie就饱和了.

Zhipeng Zhao  据说fpga硬件部分是他写的. 

另一篇 , sosp'21  prism,提出rdma oneside 更快. redesign rdma interface with new primities.   提出软件implementation for future hardware. 但是 enso 用existing network stack.

论文缺点: 

他们的hardware不够具有代表性,  用了PCIe3.0 , 没有对比rdma.  

 No mention of API or how well/easily one can integrate the userspace library with existing code. - Some real-world applications chosen for evaluation, despite being state-of-the-art, do not seem to be industry standard. - No comparison against state-of-the-art smart NICs; only simple offloads used in DPDK.

\- Lack of comparison with RDMA-based methods. - The testbed used to evaluate Ensō might not support DCA/DDIO, which may partially mitigate the poor design of existing NICs.

### pollux

osdi'21的best paper

1 Pollux 只涉及 GPU 资源的分配，CPU 。2）本文假设所有机器都是一致的，当我们想扩展到更多的机器时，机器的性能很可能是变化的，这在现实中显然过于理想，可能会限制系统的可扩展性。MLaaS in wild 研究了这一点

\- 施加许多限制和假设。例如，Pollux 仅依靠线性模型来预测网络延迟，这可能无法对具有复杂/分层内部和内部通信的系统进行建模。此外，它不允许将多个作业分配给一台计算机。此外，支持的并行性方法也受到限制。 - 需要手动选择和实施学习率策略。 - 测试平台相对较弱。这些机器没有配备高性能的 GPU 内部通信（如 NVLINK）和节点间通信（如 RDMA 网络）。 

 假设集群中有很多“对称性”：例如，Pollux 不考虑可能存在不同类型的 GPU，并且并非所有此类 GPU 都可以支持相同的大批量大小。此外，不同节点之间的网络速度可能不同，而在本文中不考虑这种情况; - 作者不讨论除 GPU 之外的任何资源的调度 - 提交作业时，我们还需要指定内存和 CPU 数量。

figure1 为什么用16个GPU 来训练 cifar10这么小的数据集?  是假设了  数据集更大, 不同stage best batch size差距会更大.

为什么要定义batch size越大, statistical efficiency越小?  其实是[模型收敛速率]. 

T4其实是用来inference的, 很廉价, 没有nvlink, 没有infinite band or rdma, pcie 还很慢. 

实验里, optimus implementation没有用之前论文的, 而是用pollux的吞吐量model.

#### MLaaS in wild

提出如果gpu  heterogenous. 该怎么办.

### zeus

发现能耗和性能优化之间有非线性tradeoff，在线profiling以调整batch size和GPU的功率限制。

这篇文章发nsdi真的很奇怪, 感觉都不是一个community的.

小的batch size可能最终acc不同,他只测了达到target acc的时间, 如果target acc很低那问题很大. 

画图技巧， 线也可以变色， 画出探索的先后顺序。

缺点： 

baseline 太烂了，所以怎么弄都有时间和能量优化。 超频也是问题。 只说了power没说频率。

 没有考虑别的超参数如lr ，不同的batch size需要不同的lr来收敛到acc。 

不需要 每次都retrain from scratch， 他们没考虑fine tuning的情况。 

CPU也花了很多energy， 他没考虑到。



|            | zeus                                                         | envpipe                                                      |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| insight    | grid search takes too much time. We need a better search solution. | Pipeline parallel is widely used, and it has a lot of bubbles. |
| method     | Multi-armed Bandit, early stopping                           | schedule pipeline parallel, decrease SM frequency when computation has bubbles. |
| results    | reduces energy consumption (ETA) by up to 15.3%–75.8%        | 1% throughput <-> 28.5% energy                               |
| Advantages | we can learn how to optimize metrics(speed/energy) across retraining jobs. | more suitable for GPU cluster, production environment        |

#### envpipe

之前的工作会牺牲训练吞吐，所以本文想在节能的同时不能降低太多训练的效率。

在来看现有的流水线训练模式，有很多气泡。 拿出来其中的一个块来看，性能变差并不会影响整体,  充分利用这个结构figure2, 可以节约能耗，并且不会降低整体训练效率。但是对于有依赖的结构（unusable气泡）来说，降低SM的频率以减少能耗的同时，意味着影响整体训练效率。

因此本文的目标就是尽量的增加usable气泡的个数，并利用usable气泡来节能。

分为三个模块，Profiler用于获取模型执行的基本信息；Scheduler用于调整流水线并行的执行过程, Planner用于调整SM的频率

scheduler 牺牲显存, 尽量将1F1B的前传F提前放, 增加Usable气泡的个数. 这个工作提出要根据显存情况去自适应调整。

planner  降低非关键路径的频率,  部分关键路径从最外侧转移到了内侧；Iteration：逐渐调整每个阶段的SM频率，直到流水线训练的关键路径又回到最外侧；为什么? 

最终实验效果在仅损耗1%吞吐量的同时，节约28.4%的能耗；

implement 上,  on top of DeepSpeed, 用的是lock_gpu_clock 来锁. 

### omnireduce

一般认为自己最懂会给最低分, 这个论文也是一个人给了2分 exp说是4.

他们认为解决了一个重要和timely topic. crowed area. well motivated. 目前的问题都是shepherd,就是可以在camera ready解决的.

可以用 `latexdiff`  来对比两篇tex文件. 

缺点

\- 需要额外的计算服务器作为聚合器。 - \- Does not figure out an efficient method to retrieve the bitmap for tensors on CPU memory. GPU scan bitmap快. 

最缺少的是关于数据稀疏性的更深入的讨论。更详细：

可以create sparsity, 在图11, 可以remove 一些梯度. 

字和图的修改不应该放在weakness, 就放在detailed comment就行. 

没有说明为什么稀疏性存在. 

- 稀疏性的好处似乎只有在具有大型嵌入表（CTR 和 LM.在 CV 中，omnireduce 的好处仅来自流聚合组件，因为传输的数据不会减少 - 见表 1）。我们能否得出结论，OmniReduce的数据稀疏性组件仅对嵌入表有用？如果是这样，我们是否可以将 Omnireduce 用于嵌入和其他在其他情况下没有通信开销的原语（在 Omnireduce 中，客户端需要传输“下一个”块索引，这会引入一些开销）？ 
- 考虑到上述因素，我们为什么要选择块大小？也就是说，如果给定嵌入的梯度为零或非零，为什么不始终使块大小等于嵌入维度呢？ 
- 令我惊讶的是，CV网络，尤其是VGG，没有从稀疏性中获益，因为它们同时使用ReLU和dropout。我认为通过观察稀疏性如何构建的模式，这可能会在某种程度上得到改善，因为稀疏性可能不存在于连续的内存位置。 OmniReduce 不会检测到这一点，因为权重矩阵是以行方式存储的！这是作者考虑过的事情吗？（我知道由于数据局部性的原因，检测列中的稀疏性可能无效，并且只有当批处理中的所有输入都为零时，梯度列才为零，但是，在论文中对此进行一些讨论将是有益的）

### megatron

Megatron-LM: Training Multi-Billion Parameter Language Models Using Model Parallelism  居然没有投稿吗，只有arxiv.

Pipeline 并行, 不同layer在不同GPU. 缺点是有bubble.

megatron tensor 并行, 同一个layer切分到多个GPU. 缺点是通讯成本比这个高. 

X是上一层的embedding,  X的一行代表什么? 就是每个element的嵌入.

因为transformer总是有连续两层FC, 所以他们直接两层一起设计了.  第一层A按列切, 减少同步, 第二层B按行切, 也是减少同步. 

attention, 要看到所有X, 所以要把X按行切, KQV 按列切. . 

通讯量  怎么算的? 

跨servers,可以用 pipeline并行.

这里是人工调整, 不能迁移到别的模型CNN, Alpa 可以自动调优 tensor 并行. 

MLP之后, 是否在一个GPU上处理? 然后之后的self attention需要再次split到多个GPU吗? 

### ZERO

梯度和optimizer states 占用的空间很大.   占用空间的还有activation, temporary buffer和fragment memory.

forward:  GPU0 broadcast 他有的参数,  伪代码好像说每个GPU拥有一层layer ? 所以他是按 MLP layer 到每个GPU的是吗? 会不会把一个layer切分到两个GPU?  这个layer是一个全连接层吗? 可能是linear layer也可能是transformer layer. 

他们只考虑了吞吐量 per GPU, 有考虑收敛的时间吗? 

微软deepspeed组其实没啥花心思写论文, 他们只在乎代码有用就行. 

我们可以把这个技术用在 one million GPU的集群上吗?会有什么问题? 

拓展: 

1. ZeRO++: Extremely Efficient Collective Communication for Giant Model Training, 发在ICLR, 多级 参数partition.  梯度/参数 压缩.

2. MiCS: near-linear scaling for training gigantic model on public cloud. from AWS.  22年11月pVLDB, 当时李沐已经离职去startup了.  divide all devices into partition groups. each group holds a complete copy of the model states. 



### Alpa

tensorflow太难用了, 所以谷歌又提出了jax来和torch竞争. 

从jax 编译到 HLO IR  with sharding spec, 然后优化HLO IR,  新代码没有sharding spec, 而是产生 通讯原语. 

之前谷歌已经有了op并行的 Gshard和GSPMD. 

intra-op:  tensor并行 和op并行,  缺点是通讯量高, 优点是device utilization 高

inter-op :  model 并行和pipeline , 优点是通讯量少, 缺点是device utilization 低.  alpa说的inter-op 其实就是pipeline 并行.

spec, table1,  If Xi = S, it means the i-th axis of the tensor is partitioned. Otherwise, the i-th axis is replicated

一个worker可能有多个GPU. 一个mesh 有多个workers.

intra-op的挑战:  1.  一个op的sharding spec会影响其他op的sharding spec. 2. 找到proper specs 搜索空间太大. 

如果previous op mismatch,就需要reshard.

inter-op pass:  graph分成多个pipeline stages, devices 分成多个 device meshes. 然后associate to stages.  最小化pipeline执行时间.   建模为动态规划问题. 

intra-op pass: 最小化stage执行时间.  建模为 integer linear Programming问题. 

limitation:

1. 没有避免 cross-mesh resharding.  - >  利用intra node 通讯
2. 编译很久 -> early pruning,  op clusttering, 限制device mesh 形状.  
3. This method does not model and fully leverage overlapping the computation and communication. 首先，省略计算成本的原因可能值得怀疑。此模型仅考虑通信成本。但是，对于某些操作来说，计算是很耗时的。其次，成本模型不考虑重叠的计算和通信。在这种情况下，可以屏蔽计算或通信成本。因此，此成本模型可能无法生成最佳分区计划。 



另一篇paper ,[Aceso: Efficient Parallel DNN Training through Iterative Bottleneck Alleviation](https://dl.acm.org/doi/10.1145/3627703.3629554)  eurosys'24, 搜索快得多, 5%的搜索cost, 吞吐量反而更高了. 但是需要allocate resource来profiling.  alpa不profiling.



#### 缺点

1. 他没有在 异构cluster上做实验. 没考虑异构设备.
2. 编译时间长。也许很难支持具有大量层的模型。 figure10要是可以把每个步骤时间标出来就更好了. 

论文的代码一般不维护, 但是idea会被工业界吸收参考. apache spark,产生了databrick. 战胜了flink. 对于学术界来说 , novelty比代码质量重要. 

mxnet 团队 他们就想创建 mxnet 原型, 然后移交给apache,他们就搞下一个项目.



### ark

ARK: GPU-driven Code Execution for Distributed Deep Learning  NSDI'23 

代码应该在微软内部才有.

他们认为 NCCL  MMIO 在反向传播的时候会降低吞吐量. 为什么? 

hardware engine可以pipeline 处理 多个 DMA requests. cudamemcpy做不到这一点.



1. abstract all GPU op into a  set of vCTAs (virtual Cooperative Thread Array)
2. 分析op图, 获得vcTA依赖关系.
3. 根据依赖关系调度vCTAs across SMs 
4. 生成 loop kernel code.



#### 缺点

1. figure10, 多节点, baseline 用superbench, 没用megatron, 会不会因为他们比不过megatron? GPT-2 他们也没说用多大的参数的,是large还是small.
2. 实验都没测nvlink,但是现实中广泛用了nvlink.  
3. Tensor-parallel Inference 只用了2个GPU, 因为他们的 fpga 硬件engine只支持两个GPU.
4. kernel fusion 没法支持cudnn这些闭源的 kernel. ARK cannot schedule close-sourced binaries such as cuDNN.
5. loop kernel 只能处理静态计算图,  动态计算图的优势是可以在搭建网络的时候看见变量的值，便于检查。 所以tf2.0也有动态图模式了.



#### 优点

1. Introduction和background写的很好. motivation阐明清晰.
2. 提供了一个新的GPU DMA 想法.  这个系统别人可能都不会用, 但是或许给了GPU厂商启发, 之后就直接做了.  H100可能就有DMA,说  transfer large block to share memory.

