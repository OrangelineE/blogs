# ACID Feature
1. Atomicity: 要么一起成功，要么一起失败
2. Consistency：跨节点场景下也不能出现中间状态。在转账的例子中， A和B的数据可能不在一个节点上，转账的全程也依然要保持A+B都为100块，不存在其他的结果
3. Isolation：一个分布式事务在一个节点的部分哪怕完成了，在整个分布式事务完成前，也不能被其他事务读取使用
4. Durability：事务提交完成后的数据不仅要在当前节点保存，还应该在分布式场景下有备份，具备高可用性。

分布式事务定义：能保证分布式ACID特性的事务

考虑电商交易的下单 -> 支付 -> 确认 -> 发货 -> 物流 -> 确认收货 -> 交易完成的交易流程，如图3-1，整个交易要保证ACID特性，所以这是一个事务。而整个交易流程是长，往往在实现中会拆分多个系统，如拆成订单系统、仓库系统、物流系统，每个系统都可能有独自的数据库（分库/分库），那么这就成为了一个分布式事务。

# 早期实践
## 两阶段提交/XA事务模型
第一阶段: 分布式事务中的协调器收到客户端的commit命令，决定提交。然后协调器向各节点发送prepare T消息，各节点决定该节点上是否提交还是中止（通过获取锁，写入重做日志的结果来判断），并返回给协调器。

第二阶段: 协调器收到各节点的ready或don’t commit响应，如果协调器从T的所有成分都收到了ready T，那么它就决定提交T，并向所有节点发送commit T消息；如果协调器收到来自一个或多个节点的don’t commit T消息，那么它就决定abort T，并向所有节点发送abort T消息。

XA事务是把两阶段提交标准化后形成指定的数据库接口，再由一个事务管理器作为协调者统一管理两阶段提交，相当于标准化的两阶段提交。

很多单机数据库, 如Oracle, DB2, MySQL支持XA接口, 应用程序实现一个事务管理器, 然后调用下面的数据库的XA接口实现两阶段提交, 从而完成分布式事务。XA事务允许各系统选择不同类型的数据库, 只要它们都支持XA接口。

## TCC事务模型

TCC将整个业务逻辑分为三块：Try、Confirm和Cancel三个操作。

+ Try就是尝试扣除用户余额、尝试扣除库存；
+ Confirm就是Try的结果都成功，可以正式把订单状态改为“已支付”，通知仓库发货；
+ Cancel则是Try失败，那么就要取消交易，恢复用户余额，恢复库存。

TCC的一个关键点是，Try阶段如果完成，那么很多状态就已经保存起来了，再出现故障也不会影响事务正常完成，也就是说，哪怕Confirm阶段或Cancel阶段失败，也会通过不断重试最终完成交易，而不是回滚。

TCC的过程类似于两阶段提交，Try过程和两阶段提交的prepare比较相似。但TCC没有事务管理器的抽象，Try、Confirm、Cancel和业务场景绑定密切，要考虑具体业务来实现。

## Saga事务模型

Saga本来是为解决长事务而出现的分布式事务模型，长事务指的是需要大段时间而不允许它们保持其事务所需要的锁的事务。

电商交易如果采用两阶段提交，则订单系统的子事务要等到用户确认收货交易完成后才能提交，而物流的时间一般比较长，这个时间可能需要几天。而订单系统的事务可能会锁住一些资源，如用户的余额，这个锁维持几天的时间，会导致用户无法消费，这显然是不可行的。

Saga是把分布式事务看作一个个单机事务的集合，对每一个单机事务Ai，Saga都有一个补偿事务Ai⁻¹。如果我们执行A，后来又执行A⁻¹，那么所产生的数据库状态同A⁻¹和A都未执行前一样

## 分布式数据库事务模型

可以看到，因为TCC事务模型对业务的入侵比较大，所以不太适合实现通用的分布式数据库。所以目前主流的关系型分布式数据库有两种主流的解决方案，分别基于Saga和两阶段提交，我们下面来具体介绍一下。
1. 分布式中间件数据库

在单机数据库的基础上实现一个中间件层（Proxy），中间件层基于单机事务加`Saga`来实现分布式事务。高可用通过单机数据库的主备复制实现。
```plaintext

```

2. 原生分布式数据库
利用共识原生实现自带高可用的存储层，基于两阶段提交实现分布式事务，不存在单机事务

### 基于Saga的事务一致性

前面介绍了Saga的基本原理，Saga原型需要设计补偿事务，和业务绑定较深。但Saga的思想是通用的，分布式数据库可以通过解析单机事务的SQL，然后生成它们的逆SQL，从而生成补偿事务。

实现这一点要满足两个条件：

1. 要有一个标志把同一个分布式事务的所有单机事务联系起来，以便在需要执行补偿事务时能够找到它们；
2. 有的单机事务已经提交，但它所在的分布式事务未提交，这时它提交的结果在分布式角度仍属不胜数据，通常不能允许其他事务对其进行读取，要对它进行单独处理。

```plaintext

```

关于Saga有一个点需要特殊说明一下: 虽然分布式数据库可以通过Saga的补偿事务来保证分布式事务的一致性，但这是一种`兜底机制`，代价很大并且极少触发。只有在分布式事务提交阶段，有单机节点已经提交完成后，其它的单机节点因故障而回滚，才会触发补偿事务。

正常情况下，分布式事务执行到提交阶段后，各节点就并行开始提交，在没有错误的情况下，Proxy收到所有节点的commit成功消息时就可以认为提交成功，这个过程近似于一阶段提交的效率。

总结一下，基于Saga的分布式事务实现较简单、稳定，但在分布式MVCC、分布式事务性能、可重用能力等方面其实并不然在短期，而要进一步解决这个问题，就需要对开源单机数据库进行改造，而改造的过程又是不简单、不稳定的。

在良好的分布式表设计和应用配合改造的前提下，分布式中间件数据库可以接近甚至达到单机数据库的性能。

### 基于两阶段提交的事务一致性

前面介绍了两阶段提交的原理。可以看出，两阶段提交对事务管理器的抽象天然适合实现分布式数据库内部，这也是大部分原生分布式数据库选择两阶段提交的原因。

两阶段提交有一个最显著的特点: 事务只有在两阶段提交的prepare阶段才会进行数据行上的锁，整个执行过程都是无锁的，这意味着并行的事务很有可能修改了同一行数据。

我们把两阶段提交的这种特性称之为 `“first-committer-wins”`， 是一种乐观事务模型（即假设事务没有冲突发生）。在评估两阶段提交事务模型的性能、设计两阶段提交事务隔离级别时，一定要考虑到这一点。

目前一些商业的原生分布式数据库对两阶段提交的加锁进行了改造，支持了悲观锁（即在事务执行阶段就加锁），这进一步提高了原生分布式数据库的支持能力。

此外，最基本的两阶段提交有一个严重的问题：协调器存在可用问题，一旦协调器宕机，在它恢复或新的协调器被选举出来之前，整个系统都是不可用的。这个问题显然不可接受。

Google的Percolator分布式事务模型对两阶段提交的这个问题做出了优化：

把收到分布式事务请求的第一个节点作为两阶段提交的协调者节点，以存储节点的可用性保证了协调者的高可用，同时也不会出现协调者宕机了整个系统都不可用的情况，因为任何一个节点都可以是协调者节点了。

此外，Percolator在事务开始时和事务提交时都会以一个时间戳并写入数据中，这个时间戳可以很便捷地支持分布式MVCC的实现。

总结一下，基于两阶段提交的分布式事务模型对分布式MVCC、高可用能力的支持更加彻底，其结构上下贯通，计算和存储可以互相适配设计，拥有理论上更高的性能上限。

但是，目前原生分布式数据库的产品成熟度、运维能力仍然不足，缺少像MySQL这样稳定单机库的支持（就算是MySQL，也依旧存在不少问题），使用此类产品必然面临很多困难和调整。对数据库厂家的运维支持能力有很高要求，基于此类数据库设计的系统对厂家的依赖性也必然更强。


# Database Lock

`锁`是用来管理对共享资源并发访问的一种机制。锁是数据库系统区别于文件系统的一个关键特性，在数据库中，除了对用户数据上锁，还会对缓存池中LRU列表、数据库表元数据等其它资源上锁。

不同的数据库对锁的实现差异很大，有的数据库是表锁设计，有的数据库是页锁设计，有的数据库是行锁设计；有的数据库会有锁升级，有的数据库则不会。Oracle、MySQL以及大部分分布式关系型数据库都是采用的行锁设计。

单机数据库的行锁通常会分为两种：共享锁（S Lock） 和 排他锁（X Lock）。

```plaintext

   |  X  |  S  
---|-----|-----
 X | 不兼容 | 不兼容 
 S | 不兼容 | 兼容  
```

可以发现，X锁和任何的锁都不兼容，S锁仅和S锁兼容。

所以，S锁通常用来用于读的锁定，阻塞其它事务上X锁，防止读取的数据被修改。X锁通常用来作写的锁定，阻塞其它事务在该行数据的任何操作。

以MySQL为例，select ... lock in share mode 上的是S锁，不会阻塞后续的select/select ... for update上S锁的操作; 但会阻塞后续的update, delete, select for update。

而update/select ... for update上的是X锁，会阻塞写或读的操作，包括update/select ... for update/delete/select ... lock in share mode。

事务中的锁还有一个很重要的概念，叫两阶段锁理论（2 Phase Lock）。在事务中，加锁阶段和解锁阶段是两个独立的阶段，不能有重叠。两阶段锁理论和事务的原子性以及隔离性关系密切，是保证并发执行正确性的重要条件。

在单机数据库上，锁资源默认是对所有事务可见的，一行数据的加锁和解锁都可以看作原子操作。

但在分布式数据库中，数据行会分布在多台机器上，因为跨节点网络传输的不可靠性，加锁解锁的过程不再是实时且原子的了。

那么保证分布式上锁的正确性，需要在分布式事务中维持一个`中心节点`来记录锁信息。加锁与解锁的成功和失败都以中心节点为准。

Saga —— `独立中心节点`

两阶段提交 —— `协调者节点`

在任何情况下，只要出现了“中心”这样的概念，一定要考虑的一个点就是中心节点的高可用性，因为中心节点一旦出现故障，往往意味着整个集群的不可用。

对于锁表和协调者，分布式数据库通常采用共识算法来保证其高可用性。Percolator模型则更进一步，把两阶段提交的协调者放在了分布式事务的一个随机的数据节点，把协调者的高可用转化成了数据库节点的高可用问题。

分布式锁通常只有X锁。这是因为实现分布式锁的代价本来就大，如果还要实现分布式锁就会再增加非常多的调度与维护代价，并且S锁的使用本来就少。因此一般没有实现分布式锁的方案。对于select ... lock in share mode语句，分布式数据库一般不支持（原生分布式数据库），或选择只在单机节点上上锁（分布式中间件数据库）。

# Transaction Isolation Levels

隔离级别本质上是定义事务并发问题的。我们把事务并发简化为三个关系（读与读之间没有相互阻塞的必要，所以不讨论读读关系）：

(1) 写写关系。分为写不阻塞写和写阻塞写。

(2) 写读关系（写后读）。分为写不阻塞读和写阻塞读。

(3) 读写关系（读后写）。分为读不阻塞写、读阻塞写和读间隙阻塞写。

隔离级别   | 写写关系    | 写读关系    | 读写关系
----------|-------------|------------|------------
丢失更新   | 写不阻塞写  | 写不阻塞读  | 读不阻塞写
读未提交   | 写阻塞写    | 写不阻塞读  | 读不阻塞写
读已提交   | 写阻塞写    | 写阻塞读    | 读不阻塞写
可重复读   | 写阻塞写    | 写阻塞读    | 读阻塞写 
可串行化   | 写阻塞写    | 写阻塞读    | 读间隙阻塞写

我们最常用的隔离级别：读已提交和可重复读。

其中可重复读会造成同一时刻只有一个事务对这一行数据进行操作，效率太低，对此我们进行优化：`一致性非锁定读`

用锁来实现并发控制的方法，会阻塞对已上锁数据的访问，代价显然极大。因此现在大部分分布数据库都支持一致性非锁定的读。

一致性非锁定读是指存储引擎通过多版本并发控制 (MVCC) 的方式来读取当前执行时间数据库中行的数据。如果读取的行正在被锁住，那么这时读取操作不会因此去等待行上锁的释放，而是去读取行的一个快照数据。

现在的大部分关系型数据库都是基于多版本并发控制 (MVCC) 的，所以我们要引入一个针对快照机制的新的隔离级别`快照隔离` (Snapshot Isolation, SI)。

SI隔离级别会去读事务开始时间点的数据库快照，从而避免不可重复读，但又不像用锁实现的可重复读隔离级别那样读也要阻塞写。（如果始终读取最新的快照，则相当于RC，但我们讲SI，一般指的是读事务开始时的快照）

那么SI和RR有什么区别呢？这就涉及到两种特殊的场景——幻读和偏序写

## 幻读(Phantom Read)
幻读是一种并发问题，发生在一个事务在两次查询之间发现数据不一致的情况。例如，事务A执行了一次查询，返回了满足条件的记录集。然后，另一个事务B在事务A尚未结束时插入了一些新记录，这些记录也符合事务A的查询条件。如果事务A在稍后重新执行相同的查询，它会发现比之前多了额外的记录，这就是幻读。

场景: 假设事务A在数据库中查询所有年龄大于30岁的员工，并返回了10条记录。在事务A还没有提交的时候，事务B插入了一条年龄为32岁的新员工记录。事务A再次查询时，结果中就会多出一条新的记录，这就是幻读。

影响: 幻读可能导致事务在执行过程中产生不一致的结果，破坏数据的一致性。

-----------------------------------------------------------
我们把点查询称为item查询，非点查询（范围、聚合等）称为predicate查询。

那么我们可以把幻读定义为不可重复读的predicate版本。幻读和不可重复读都需要在一次事务中进行两次读操作。不可重复读指的是两次item查询查到了不同的内容，而幻读指的是两次predicate查询查到了不同的结果。

我们可以从图中看到，在第二次predicate读取时，RR隔离级别读到了变化后的结果，这是因为只对第一次查到的数据上了行锁，而没有对整个范围上锁。而SI级别不对任何数据上锁，每次查询相同的快照，就可以保证读取数据的一致。（这里说明一点，MySQL默认隔离级别一般被称为RR，但实际上是SI。有资料用select范围for update来说明幻读现象，说明有间隙锁可以避免幻读，这其实是不对的，这是快照读和当前读的差别，而不是幻读）

ANSI SQL-92把可串行化隔离级别定义成不会发生肮脏读、不可重复读和幻读，但这并不与真正的Serializable语义等价，比如下面要介绍的偏序写问题。
## 偏序写(Write Skew)

偏序写是一种特殊的并发写操作异常，发生在两个事务在读同一组数据后，都基于这些数据做出了一些决定，并同时尝试修改不同的记录，最终导致数据的不一致。

场景: 假设有两个医生（事务A和事务B）正在修改一个病人的医疗记录。两个医生都读取了病人的当前状态，决定进行不同的处理。事务A基于当前的读操作修改了病人的诊断信息，而事务B基于相同的读操作修改了治疗计划。如果这两个事务同时提交，就可能导致病人的诊断和治疗计划之间出现逻辑不一致的情况。

影响: 偏序写的主要问题是，它会破坏数据的逻辑一致性，即使各个事务单独看起来是正确的，但它们一起执行时可能产生不正确的结果。
------------------------------------------------------------------------
隔离级别     | 写写关系    | 写读关系    | 读写关系    | 存在的问题
------------|------------|------------|------------|--------------
丢失更新     | 写不阻塞写  | 写不阻塞读  | 读不阻塞写  | ALL
读未提交     | 写阻塞写    | 写不阻塞读  | 读不阻塞写  | 脏读/不可重复读/幻读/偏序写
读已提交     | 写阻塞写    | 写阻塞读    | 读不阻塞写  | 不可重复读/幻读/偏序写
可重复读     | 写阻塞写    | 写阻塞读    | 读阻塞写    | 幻读
快照隔离     | 写阻塞写    | 写不阻塞读  | 读不阻塞写  | 偏序写
可串行化     | 写阻塞写    | 写阻塞读    | 读间隙阻塞写| 无

--------------------------------------------------------------------------
理论上，分布式事务隔离级别和单机事务应该并无差别，但因为通常不设计分布式S锁，所以不存在读阻塞写的情况。

所以，要么RC，要么SI。

分布式SI的支持需要全局快照。
-------------------------------------------------------------------------
读语句隔离级别    | 弱一致性读       | 分布式写不阻塞读
                 | 强一致性读       | 分布式写阻塞读

写语句隔离级别    | 弱一致性写       | 分布式写不阻塞写
                 | 强一致性写       | 分布式写阻塞写
