
#### redis集群，为什么是16384，哨兵模式，选举过程，会有脑裂问题么，raft算法，优缺点
    （1）如果槽位为65536，发送心跳信息的消息头达8k，发送的心跳包过于庞大。
    （2）redis的集群主节点数量基本不可能超过1000个。
    集群节点越多，心跳包的消息体内携带的数据越多。如果节点过1000个，也会导致网络拥堵。因此redis作者，不建议redis cluster节点数量超过1000个。 那么，对于节点数在1000以内的redis cluster集群，16384个槽位够用了。没有必要拓展到65536个。
    （3）槽位越小，节点少的情况下，压缩率高。
##### redis脑裂
哨兵模式脑裂:宕机,需要选举备份节点，选举一个备份作为主节点。
集群模式脑裂:集群中的多节点通讯出现问题.

    由于网路问题，导致redis的master与slave、sentinel集群处于不同网络分区，此时因为sentinel集群无法感知到master的存在，将slave选举为master。
    此时有两个master节点，类似于一个大脑分裂为两个。
    **会出现数据丢失的问题**。
解决：
`
min-replicas-to-write 3   ；表示连接到master的最少slave量
min-replicas-max-lag  10 ；表示slave链接到master的最大延迟时间
`
>按照上面的配置，要求至少3个slave节点，且数据复制和同步的延迟不能超过10秒，否则的话master就会拒绝写请求，
> 配置了这两个参数之后，如果发生集群脑裂，原先的master节点接收到客户端的写入请求会拒绝，就可以减少数据同步之后的数据丢失。

### raft算法
>解决分布式一致性问题

Redis使用了Raft算法实现领导者选举。


#### redis cluster为什么没有使用一致性hash算法，而是使用了哈希槽预分片？
    缓存热点问题：一致性哈希算法在节点太少时，容易因为数据分布不均匀而造成缓存热点的问题。
    一致性哈希算法可能集中在某个hash区间内的值特别多，会导致大量的数据涌入同一个节点，
    造成master的热点问题(如同一时间20W的请求都在某个hash区间内)。

#### 你们项目为什么用redis，快在哪，怎么保证高性能，高并发的

#### redis字典结构，hash冲突怎么办，rehash，负载因子

#### redis字符串实现，sds和c区别，空间预分配

#### redis有序集合怎么实现的，跳表是什么，往跳表添加一个元素的过程，添加和获取元素，获取分数的时间复杂度，为什么不用红黑树，红黑树有什么特点，左旋右旋操作

#### redis缓存穿透，布隆过滤器，怎么使用，有什么问题，怎么解决这个问题

#### redis分布式锁，过期时间怎么定的，如果一个业务执行时间比较长，锁过期了怎么办，怎么保证释放锁的一个原子性，你们redis是集群的么，讲讲redlock算法

#### redis线程模型，单线程有什么优缺点，为什么单线程能保证高性能，什么情况下会出现阻塞，怎么解决

#### 你们用的redis集群么，扩容的过程，各个节点间怎么通信的

#### redis持久化过程，aof持久化会出现阻塞么，一般什么情况下使用rdb，aof

#### 你们用redis么，用来做什么，什么场景使用的，遇到过什么问题，怎么解决的

#### redis管道用过么，用来做什么，它的原理是，保证原子性么，和事务的区别，redis事务保证原子性么

#### redis强一致性么，怎么保证强一致性，有什么方案

#### redis的几种数据类型，你们用过哪些，zset有用来做什么

#### redis实现分布式锁，还有其他方式么，zookeeper怎么实现，各有什么有缺点，你们为什么用redis实现

#### 微信朋友圈设计，点赞，评论功能实现，拉黑呢，redis数据没了怎么办

#### 怎么保证redis和mysql的一致性，redis网络原因执行超时了会执行成功么，那不成功怎么保证数据一致性

#### 一个热榜功能怎么设计，怎么设计缓存，如何保证缓存和数据库的一致性