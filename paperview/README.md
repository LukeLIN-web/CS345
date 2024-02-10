# CS345

comment: 

1.  不要说 bad, 说  your scheme would be stronger if it dealt with case X .
2. 可以说某些assumption 不成立,  insufficient evalution  ,  instances where solution不能work,  一些部分难以理解

建立一个自己的reading list,  是一个queue, 看到想读的就放进去,  然后有时间了就读.



#### arrakis

14年osdi, simon还是postdoc @uw, 开场说如果有工作请联系我, 现在已经是ap了.

109行就能改 redis use persistent log. 

It does not compare with RDMA, a classic method of Kernel Bypassing

别人的评论: 系统没有解释在使用中断时如何消除内核交叉的性能开销，也没有详细说明中断和硬件虚拟化如何协同工作.  从十年后的角度来看，这篇文章应该是内核旁路的一个很好的例子，反映了近年来高性能网络领域的一个显著趋势：将内核的工作转移到硬件和用户空间。今天流行的 eBPF 和 XDP 可以看作是这个想法的延续。这项工作扎实，思维清晰，并取得了明显的性能改进。 缺点是内核绕过的经典实现是让用户空间程序轮询用户模式驱动程序以获取信号，而不是使用中断，因为中断通常会产生开销。然而，这项工作仍然使用中断，并声称系统不会因为内核交叉而产生大量的开销，但本文没有解释这是如何实现的。特别是考虑到使用中断的目的是暂停待处理的程序，以便为其他程序提供 CPU 周期，这通常是由内核完成的。因此，中断往往会陷阱到内核来完成程序之间的调度，因此消除这种内核交叉并不是一件简单的事情。
