---
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: false
---

# 2023.6.28 面试

Redis八股

### 一致性哈希的原理

首先对节点进行哈希计算，哈希值通常在 2^32-1 范围内。然后将 2^32-1 这个区间首尾连接抽象成一个环并将节点的哈希值映射到环上，当我们要查询 key 的目标节点时，同样的我们对 key 进行哈希计算，然后顺时针查找到的第一个节点就是目标节点。 这样就完了吗？还没有，试想一下假如环上的节点数量非常少，那么非常有可能造成数据分布不平衡，本质上是环上的区间分布粒度太粗。

怎么解决呢？不是粒度太粗吗？那就加入更多的节点，这就引出了一致性哈希的虚拟节点概念，虚拟节点的作用在于让环上的节点区间分布粒度变细。

一个真实节点对应多个虚拟节点，将虚拟节点的哈希值映射到环上，查询 key 的目标节点我们先查询虚拟节点再找到真实节点即可。

### rdb和aop的分别

一、RDB 的优点

* 体积更小：相同的数据量 RDB 数据比 AOF 的小，因为 RDB 是紧凑型文件。
* 恢复更快：因为 RDB 是数据的快照，基本上就是数据的复制，不用重新读取再写入内存。
* 性能更高：父进程在保存 RDB 时候只需要fork一个子进程，无需父进程的进行其他io操作，也保证了服务器的性能。

二、RDB 的缺点

* 故障丢失：因为 RDB 是全量的，我们一般是使用shell脚本实现30分钟或者1小时或者每天对 Redis 进行 RDB 备份，（注，也可以是用自带的策略），但是最少也要5分钟进行一次的备份，所以当服务死掉后，最少也要丢失5分钟的数据。
* 耐久性差：相对 AOF 的异步策略来说，因为 RDB 的复制是全量的，即使是 fork 的子进程来进行备份，当数据量很大的时候对磁盘的消耗也是不可忽视的，尤其在访问量很高的时候，主线程 fork 的时间也会延长，导致 cpu 吃紧，耐久性相对较差。

三、AOF 的优点

* 数据保证：我们可以设置fsync策略，一般默认是 Everysec，也可以设置每次写入追加，所以即使服务死掉了，也最多丢失一秒数据
* 自动缩小：当 AOF 文件大小到达一定程度的时候，后台会自动的去执行 AOF 重写，此过程不会影响主进程，重写完成后，新的写入将会写到新的 AOF 中，旧的就会被删除掉。但是此条如果拿出来对比 RDB 的话还是没有必要算成优点，只是官网显示成优点而已。

四、AOF 的缺点

* 性能相对较差：它的操作模式决定了它会对 Redis 的性能有所损耗。（主线程写文档） 体积相对更大：尽管是将 AOF 文件重写了，但是毕竟是操作过程和操作结果仍然有很大的差别，体积也毋庸置疑的更大。
* 恢复速度更慢：AOF 在过去曾经发生过这样的 bug ： 因为个别命令的原因，导致 AOF 文件在重新载入时，无法将数据集恢复成保存时的原样。 测试套件里为这种情况添加了测试： 它们会自动生成随机的、复杂的数据集， 并通过重新载入这些数据来确保一切正常。 虽然这种 bug 在 AOF 文件中并不常见， 但是对比来说， RDB 几乎是不可能出现这种 bug 的。

五、RDB 和 AOF 的混合模式

在两次快照中间使用日志，第二次快照完成之后日志就可以清除以便下次使用，这样不会存在 AOF 文件过大的问题。

### redis 集群间一致性

Gossip 算法如其名，在办公室，只要一个人八卦一下，在有限的时间内所有的人都会知道该八卦的信息，这种方式也与病毒传播类似，因此 Gossip 有众多的别名，如“闲话算法”、“疫情传播算法”、“病毒感染算法”、“谣言传播算法”。但 Gossip 并不是一个新东西，之前的泛洪查找、路由算法都归属于这个范畴，不同的是 Gossip 给这类算法提供了明确的语义、具体实施方法及收敛性证明。

Redis Cluster 中的每个 Redis 实例监听两个 TCP 端口，6379（默认）用于服务客户端查询，16379（默认服务端口+10000）用于集群内部通信。集群中节点通信方式如下：

* 每个节点在固定周期内通过特定规则选择几个节点发送 Ping 消息；
* 接收到 Ping 消息的节点用 Pong 消息作为响应。

集群中每个节点通过一定规则挑选要通信的节点，每个节点可能知道全部节点，也可能仅知道部分节点，只要这些节点彼此可以正常通信，最终它们会达到一致的状态。当节点故障、新节点加入、主从关系变化、槽信息变更等事件发生时，通过不断的 Ping/Pong 消息通信，经过一段时间后所有的节点都会知道集群全部节点的最新状态，从而达到集群状态同步的目的。

## kafka八股

### kafka和rabbitmq的区别



![](<.gitbook/assets/image (2).png>)

![](.gitbook/assets/image.png)

![](<.gitbook/assets/image (1).png>)

![](https://github.com/ungle/interview-questions/assets/34132413/5ab6f2d0-462e-43c7-9e30-de69390c1447) ![](https://github.com/ungle/interview-questions/assets/34132413/37d3041e-dc1d-47f3-9a21-2f7aab60415b) ![](https://github.com/ungle/interview-questions/assets/34132413/8fb5692d-6dcd-49d2-8d4a-5ca259755248)

### kafka为什么性能这么高

* Cache Filesystem Cache PageCache缓存
* 顺序写：由于现代的操作系统提供了预读和写技术，磁盘的顺序写大多数情况下比随机写内存还要快。
  * 即使是普通的机械磁盘，顺序访问速率也接近了内存的随机访问速率。
  * Kafka的每条消息都是append的，不会从中间写入和删除消息，保证了磁盘的顺序访问。
* Zero-copy：零拷技术减少拷贝次数
  * Linux 2.4+内核通过sendfile系统调用，提供了零拷贝。数据通过DMA拷贝到内核态Buffer后，直接通过DMA拷贝到NIC Buffer，无需CPU拷贝。这也是零拷贝这一说法的来源。除了减少数据拷贝外，因为整个读文件-网络发送由一个sendfile调用完成，整个过程只有两次上下文切换，因此大大提高了性能。
  * transferTo和transferFrom并不保证一定能使用零拷贝。实际上是否能使用零拷贝与操作系统相关，如果操作系统提供sendfile这样的零拷贝系统调用，则这两个方法会通过这样的系统调用充分利用零拷贝的优势，否则并不能通过这两个方法本身实现零拷贝。
* Batching of Messages：批量量处理。合并小的请求，然后以流的方式进行交互，直顶网络上限。
  * 即使是顺序读写，过于频繁的大量小IO操作一样会造成磁盘的瓶颈，此时又变成了随机读写。Kafka的策略是把消息集合在一起，批量发送，尽可能减少对磁盘的访问。
  * 结合磁盘顺序写入，批量无疑是非常有必要（如果用的时候每发送一条消息都调用future.get等待，性能至少下降2个数量级）。写入的时候放到RecordAccumulator进行聚合，批量压缩，还有批量刷盘等...
* Pull 拉模式：使用拉模式进行消息的获取消费，与消费端处理能力相符。
