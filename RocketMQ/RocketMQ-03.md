# 1.高级功能

## 1.1 消息存储

分布式队列因为有高可靠性的要求，所以数据要进行持久化存储

![image-20211117171341226](C:\Users\69079\AppData\Roaming\Typora\typora-user-images\image-20211117171341226.png)

1. 消息生产者发送消息
2. MQ 收到消息，将消息进行持久化，在存储中新增一条记录
3. 返回 ACK 给生产者
4. MQ push 消息给对应的消费者，然后等待消费者返回 ACK
5. 如果消息消费者在指定时间内成功返回 ACK，那么 MQ 认为消息消费成功，在存储中删除消息，即执行第 6 步；如果 MQ 在指定时间内没有收到 ACK，则认为消息消费失败，会尝试重新 push 消息，重复执行 4、5、6 步骤
6. MQ 删除消息

### 1.1.1 存储介质

* 关系型数据库DB

Apache下开源的另外一款 MQ — ActiveMQ（默认采用的 KahaDB 做消息存储）可选用 JDBC 的方式来做消息持久化，通过简单的 xml 配置信息即可实现 JDBC 消息存储。由于，普通关系型数据库（如 Mysql）在单表数据量达到千万级别的情况下，其 IO 读写性能往往会出现瓶颈。在可靠性方面，该种方案非常依赖 DB，如果一旦 DB 出现故障，则 MQ 的消息就无法落盘存储会导致线上故障

* 文件系统

目前业界较为常用的几款产品（RocketMQ/Kafka/RabbitMQ）均采用的是消息刷盘至所部署虚拟机/物理机的文件系统来做持久化（刷盘一般可以分为异步刷盘和同步刷盘两种模式）。消息刷盘为消息存储提供了一种高效率、高可靠性和高性能的数据持久化方式。除非部署 MQ 机器本身或是本地磁盘挂了，否则一般是不会出现无法持久化的故障问题。

### 1.1.2 性能对比

文件系统 > 关系型数据库DB

### 1.1.3 RMQ 消息的存储和发送

#### 1）消息存储

磁盘如果使用得当，磁盘的速度完全可以匹配上网络的数据传输速度。目前的高性能磁盘，顺序写速度可以达到 600MB/s，超过了一般网卡的传输速度。但是磁盘随机写的速度只有大概 100KB/s，和顺序写的性能相差 6000 倍！因为有如此巨大的速度差别，好的消息队列系统会比普通的消息队列系统速度快多个数量级。RocketMQ 的消息用顺序写，保证了消息存储的速度。

#### 2）消息发送

Linux 操作系统分为【用户态】和【内核态】，文件操作、网络操作需要涉及这两种形态的切换，免不了进行数据复制。一台服务器把本机磁盘文件的内容发送到客户端，一般分为两个步骤：

1）read：读取本地文件内容；

2）write：将读取的内容通过网络发送出去。

这两个看似简单的操作，实际进行了 4 次数据复制，分别是：

1. 从磁盘复制数据到内核态内存；
2. 从内核态内存复制到用户态内存；
3. 然后从用户态内存复制到网络驱动的内核态内存；
4. 最后是从网络驱动的内核态内存复制到网卡中进行传输。

![image-20211118144125535](C:\Users\69079\AppData\Roaming\Typora\typora-user-images\image-20211118144125535.png)

通过使用 mmap 的方式，可以省去向用户态的内存复制，提高速度。这种机制在 Java 中是通过 MappedByteBuffer 实现的

RocketMQ 充分利用了上述特性，也就是所谓的“零拷贝”技术，提高消息存盘和网络发送的速度。

> 这里需要注意的是，采用 MapperByteBuffer 这种内存映射的方式有几个限制，其中之一是一次只能映射 1.5~2G 的文件至用户态的虚拟内存，这也是为何 RocketMQ 默认设置单个 CommitLog 日志数据文件为 1G 的原因了

### 1.1.4 消息存储结构

RocketMQ 消息的存储是由 ConsumeQueue 和 CommitLog 配合完成的，消息真正的物理存储文件是 CommitLog，ConsumeQueue 是消息的逻辑队列，类似数据库的索引文件，存储的指向物理存储的地址。每个 Topic 下的每个 MessageQueue 都有一个对应的 ConsumeQueue 文件。

![image-20211118154229905](C:\Users\69079\AppData\Roaming\Typora\typora-user-images\image-20211118154229905.png)

* CommitLog：存储消息的元数据
* ConsumeQueue：存储消息在CommitLog 索引
* IndexFile：为了消息查询提供了一种通过 key 或时间区间来查询消息的方法，这种通过 IndexFile 来查找消息的方法不影响发送和消费消息的主流程

### 1.1.5 刷盘机制

RocketMQ 的消息是存储到磁盘上的，这样既能保证断电后恢复，又可以让存储的消息量超过内存的限制。RocketMQ 为了提高性能，会尽可能地保证磁盘的顺序写。消息在通过 Producer 写入 RocketMQ 的时候，有两种写磁盘方式，分布式同步刷盘和异步刷盘。

![image-20211118170157781](C:\Users\69079\AppData\Roaming\Typora\typora-user-images\image-20211118170157781.png)

#### 1）同步刷盘

在返回写成功状态时，消息已经被写入磁盘。具体流程是，消息写入内存的 PAGECACHE 后，立刻通知刷盘线程刷盘，然后等待刷盘完成，刷盘线程执行完成后唤醒等待的线程，返回消息写成功的状态。

#### 2）异步刷盘

在返回写成功状态时，消息可能只是被写入了内存的 PAGECACHE，写操作的返回快，吞吐量大；当内存的消息量积累到一定程度时，统一触发写磁盘动作，快速写入。

#### 3）配置

**同步刷盘还是异步刷盘，都是通过 Broker 配置文件里的 flushDiskType 参数设置的，这个参数被配置成 SYNC_FLUSH、ASYNC_FLUSH 中的一个。**

## 1.2 高可用机制

![image-20211118180527529](C:\Users\69079\AppData\Roaming\Typora\typora-user-images\image-20211118180527529.png)

RocketMQ 分布式集群是通过 Master 和 Slave 的配合达到高可用性的。

Master 和 Slave 的区别：在 Broker 的配置文件中，参数 brokerId 的值为 0 表明这个 Broker 是 Master，大于 0 表明这个 Broker 是 Slave，同时 brokerRole 参数也会说明这个 Broker 是 Master 还是 Slave。

Master 角色的 Broker 支持读和写，Salve 角色的 Broker 仅支持读，也就是 Producer 只能和 Master 角色的 Broker 连接写入消息；Consumer 可以连接 Master 角色的 Broker，也可以连接Slave 角色的 Broker 来读取消息。

### 1.2.1 消息消费高可用

在 Consumer 的配置文件中，并不需要设置是从 Master 读还是从 Slave 读，当 Master 不可用或者繁忙的时候，Consumer 会被自动切换到 Slave 读。有了自动切换 Consumer 这种机制，当一个 Master 角色的机器出现故障后，Consumer 仍然可以从 Slave 读取消息，不影响 Consumer 程序。这就达到了消费端的高可用性。

### 1.2.2 消息发送高可用

在创建 Topic 的时候，把 Topic 的多个 Message Queue 创建在多个 Broker 组上（相同 Broker 名称，不同 brokerId 的机器组成一个 Broker 组），这样当一个 Broker 组的 Master 不可用后，其他组的 Master 仍然可用，Producer 仍然可用发送消息。RocketMQ 目前还不支持把 Slave 自动转成 Master，如果机器资源不足，需要把 Slave 转成 Master，则要手动停止 Slave 角色的 Broker，更改配置文件，用新的配置文件启动 Broker。

![image-20211118224007055](C:\Users\69079\AppData\Roaming\Typora\typora-user-images\image-20211118224007055.png)

# 2.源码分析

