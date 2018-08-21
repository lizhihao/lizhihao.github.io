---
author: lizhihao
comments: true
layout: post
title: 'distribute db'
thread: 98
categories:
- tech
---

# 分布式数据库知识点

标签（空格分隔）： TECH

---

# 0.前言
分布式数据库的数据一致性管理是其最重要的内核技术之一，也是保证分布式数据库满足数据库最基本的ACID特性中的 “一致性”(Consistency)的保障

# 1. 数据一致性
但是本文介绍的“数据一致性”，指的是“数据在多份副本中存储时，如何保障数据的一致性”场景。

在大数据领域，数据的安全不再由硬件来保证，而是通过软件手段，通过同时将数据写入到多个副本中，来确保数据的安全。数据库在同时向多个副本写入记录时，如何确保每个副本数据一致，称为“数据一致性”。

本文在讨论分布式存储时，主要指的是大数据产品中的分布式文件系统和分布式数据库，例如：HDFS。

# 2. Raft 算法
一致性算法 Raft最大的特点在于简单易懂，并且实现起来简单。
## 算法原理

### 节点角色
Raft算法中，对节点的状态分为3种角色，分别是Leader(领导者)、Follower(追随者)和Candidate(候选者)。

Leader，负责处理来自客户端的请求，负责将日志同步到Follower中，并且保证与Follower之间的heartBeat联系;

Follower，当集群刚刚启动时，所有节点均为Follower状态，它的工作主要为响应Leader的日志同步请求，响应Candidate的请求，以及把请求到Follower的事务请求转发给Leader;

Candidate，选举Leader时负责投票，选举出来Leader后，节点将从Candidate状态变为Leader状态。

### Terms

在分布式环境下，“时间同步”一直都是老大难的技术难题。Raft为了解决这个问题，将时间划分为一个一个的Term(可以理解为“逻辑时间”)来处理在不同时间段里的数据一致性。

Terms有以下原则

1. 每个Term中，至多存在一个Leader

2. 某些Term中，有可能存在由于选举失败，没有Leader的情况

3. 每个节点自己维护本地的currentTerm

4. 每个Term都是一个连续递增的编号

5. 如果Follower的Term编号比别的Follower Term编号小时，该Follower Term编号将更新Term编号，以保持与其他Follower Term编号一致


### 选举

Raft的选举由定时器触发，每个节点的触发时间都不相同。

所有的节点在开始时状态都为Follower，当定时器触发选举后Term编号递增，该节点的状态由Follower转为Candidate，并且向其他节点发起RequestVote RPC请求，这时选举有3种情况可能发生：

1. 发起RequestVote的节点收到n/2+1(过半数)个节点的投票，该节点将从Candidate状态变为Leader状态，开始向其他节点发送HeartBeat以保持Leader的正常状态

2. 如果收到投票请求后，该节点发现发起投票的节点Term大于自己，则该节点状态从Candidate转为Follower，否则保持Candidate状态，并且拒绝该投票请求

3. 选举期间发生了超时，则Term编号递增，重新发起选举


### 日志复制

日志复制主要的作用就是用来保证节点的数据一致性与高可用性。

当Leader被选举出来后，所有的事务操作都必须要经过Leader处理。这些事务操作成功后，将会被按顺序写入到LOG中，每个LOG都包含一个index编号。

Leader在LOG发生变化后，通过HeartBeat将新的LOG同步到Follower上，Follower在接收到LOG后，再向Leader发送ACK信息，当Leader接到大多数(2/n+1)Follower的ACK信息后，将该LOG设置为已提交，并且Leader将LOG追加到本地磁盘中。

同时Leader将在下一个HeartBeat中，通知所有的Follower将该LOG存储在各自的本地磁盘中。

### 安全性

安全性是用于确保每个节点都是按照相同的日志序列进行执行的安全机制。

如果当某个Follower在同步Leader的日志时失败，但是未来该Follower又可能被选举为Leader时，就有可能导致前一个Leader已经commit的日志发生覆盖，这样就导致了节点执行不同序列的日志。

Raft的安全性就是用于保证选举出来的Leader一定包含先前已经commit LOG 的机制，主要遵循的原则如下：

1 每个Term 只能选举一个Leader;

2 Leader的日志完整性，则当Candidate重新选举Leader时，新的Leader必须要包含先前已经commit的LOG;

3 Candidate在选举新的Leader时，使用Term来保证LOG的完整性;


# ZooKeeper简介

ZooKeeper是一个开放源码的分布式应用程序协调服务，它包含一个简单的原语集，分布式应用程序可以基于它实现同步服务，配置维护和命名服务等。

## ZooKeeper设计目的

1. 最终一致性：client不论连接到哪个Server，展示给它都是同一个视图，这是zookeeper最重要的性能。

2. 可靠性：具有简单、健壮、良好的性能，如果消息m被到一台服务器接受，那么它将被所有的服务器接受。

3. 实时性：Zookeeper保证客户端将在一个时间间隔范围内获得服务器的更新信息，或者服务器失效的信息。但由于网络延时等原因，Zookeeper不能保证两个客户端能同时得到刚更新的数据，如果需要最新数据，应该在读数据之前调用sync()接口。

4. 等待无关（wait-free）：慢的或者失效的client不得干预快速的client的请求，使得每个client都能有效的等待。

5. 原子性：更新只能成功或者失败，没有中间状态。

6. 顺序性：包括全局有序和偏序两种：全局有序是指如果在一台服务器上消息a在消息b前发布，则在所有Server上消息a都将在消息b前被发布；偏序是指如果一个消息b在消息a后被同一个发送者发布，a必将排在b前面。


## Zookeeper数据结构

1. 每个子目录项如NameService都被称作为znode，这个znode是被它所在的路径唯一标识，如Server1这个znode的标识为/NameService/Server1。

2. znode可以有子节点目录，并且每个znode可以存储数据，注意EPHEMERAL（临时的）类型的目录节点不能有子节点目录。

3. znode是有版本的（version），每个znode中存储的数据可以有多个版本，也就是一个访问路径中可以存储多份数据，version号自动增加。

4. znode的类型：

Persistent节点，一旦被创建，便不会意外丢失，即使服务器全部重启也依然存在。每个 Persist 节点即可包含数据，也可包含子节点。

Ephemeral节点，在创建它的客户端与服务器间的 Session 结束时自动被删除。服务器重启会导致 Session 结束，因此 Ephemeral 类型的 znode 此时也会自动删除。

Non-sequence节点，多个客户端同时创建同一 Non-sequence 节点时，只有一个可创建成功，其它匀失败。并且创建出的节点名称与创建时指定的节点名完全一样。

Sequence节点，创建出的节点名在指定的名称之后带有10位10进制数的序号。多个客户端创建同一名称的节点时，都能创建成功，只是序号不同。

5. znode可以被监控，包括这个目录节点中存储的数据的修改，子节点目录的变化等，一旦变化可以通知设置监控的客户端，这个是Zookeeper的核心特性，Zookeeper的很多功能都是基于这个特性实现的。

6. ZXID：每次对Zookeeper的状态的改变都会产生一个zxid（ZooKeeper Transaction Id），zxid是全局有序的，如果zxid1小于zxid2，则zxid1在zxid2之前发生。