---
author: lzh
date: 2017-03-07 07:30:17+00:00
layout: post
title: "Gearman简介"
categories:
- Greaman
- 分布式
- 分发任务框架
---

------

# 简介
Gearman是一个分发任务的程序框架，可以用在各种场合，Gearman更偏向于任务分发功能。它的任务分布非常简单，简单得可以只需要用脚本即可完成。Gearman最初用于LiveJournal的图片resize功能，由于图片resize需要消耗大量计算资 源，因此需要调度到后端多台服务器执行，完成任务之后返回前端再呈现到界面。

通过Gearman分布式任务分发框架自然而然地便可以搭建一个分布式计算集群，从这点来说把Gearman称作是分布式计算框架也未尝不可，whatever，只要我们理解了原理，叫什么都无所谓啦。
Geraman宣称的几大卖点有：开源、多语言支持、富有弹性、高效、易于嵌入和无单点失败。开源和多语言支持毋须多言，其他几个特性需要分别说明一下。

Gearman 分布式任务实现原理上只用到2个字段，function name和data。function name即任务名称，由client传给job server, job server根据function name选择合适的worker节点来执行。data通常为执行任务所需的自定义的内容，比如简单的做法可以把需要执行的脚本当成data即可(当然要注 意其中的安全防范)。如果有多个worker可以处理同一个function name, 则job server会自动分配一个。当用于远程监控场景时，我们可以让每个worker注册成不同的业务名称，以达到方便控制每台worker节点的目的。

# Greaman 可以做什么

Gearman可以做什么

1. 异步处理:图片处理,订单处理,批量邮件/通知之类的
2. 要求高CPU或内存的处理:大容量的数据处理,MapReduce运算,日志聚集,视频编码
3. 分布式和并行的处理
5. 定时处理:增量更新,数据复制
6. 限制速率的FIFO处理
7. 分布式的系统监控任务


# Gearman框架中的三个角色

1. Client： 提交任务的人。创建需要被执行的job然后发送给Job Server。
2. Worker： 真正干活的人。向Job Server注册然后从Job Server处拿活干, PHP, Ruby, Java等流行软件进行编写。
3. Job Server：传说中的manager。接收client提交的Job，分发给相应的worker。并能在worker出现异常时重新派发job。

# job，worker的工作流程

1. Worker通过CAN_DO消息，注册到Job server上。
2. 随后发起GRAB_JOB，主动要求分派任务。
3. Job server如果没有job可分配，就返回NO_JOB。
4. Worker收到NO_JOB后，进入空闲状态，并给Job server返回PRE_SLEEP消息，告诉Job server:”如果有工作来的话，用NOOP请求我先。”
5. Job server收到worker的PRE_SLEEP消息后，明白了发送这条消息的worker已经进入了空闲态。
6. 这时如果有job提交上来，Job server会给worker先发一个NOOP消息。
7. Worker收到NOOP消息后，发送GRAB_JOB向Job server请求任务。
8. Job server把工作派发给worker。
9. Worker干活，完事后返回WORK_COMPLETE给Job server。
注：
值得注意的是，第6步中，Job server会给每个发送过PRE_SLEEP消息的worker都发送NOOP消息，哪个worker先进入到第7步，即哪个worker发送的GRAB_JOB最先被Job server收到，那么这个job就被派发到哪个worker。这一点可以在worker端实现时利用起来，以控制任务的派发策略。也就是说，我们可以通过自定义worker端的请求策略的方式来达到自定义job分派策略的目的。

# Example
1,安装方法

    #安装gearman
    yum install gearmand
    #安装libgearman
    yum install libgearman-devel
    #安装gearman php扩展
    pecl install gearman
    #添加gearman.so到 php.ini
    echo "extension=gearman.so" >> /etc/php.ini
 没有pecl的话装一下

    yum install php-pear
如果pecl没法安装的话，可以直接下载源代码，使用phpize编译安装

    cd ~/software
    wget http://pecl.php.net/get/gearman-1.1.2.tgz
    cd gearman-1.1.2
    phpize
    ./configure
    make && make install
 

2，简单使用示例：（我们使用Gearman来异步处理百度云推送服务）

先看client.php注册事件，client.php

    <?php
    // 创建Gearman对象
    $client = new GearmanClient();
    // addServer默认是localhost,端口默认是4730，如果不是默认的话可以调整
    $client -> addServer();
    // $client->addServer("192.168.0.0",4730);
    echo "Sending job\n";
    $username = "test";
    $message = "message";
    $data = array(
        "username" => $username,
        "message" => $message , 
    );
    // 注册事件 以及 传递参数 , 多个参数使用json_encode转换
    // 任务可以阻塞式运行，还可以指定优先级 ；当然，也可以非阻塞（运行不等待结果）运行
    // 可以参考php Gearman api文档 : doNornal, doHigh, doLow,doBackground
    $result = $client -> doBackground("testFunction", json_encode($data));
    if ($result) {
        echo "Success: $result\n";
    }
    ?>


再看worker的处理，其实就是注册事件处理函数 ， worker.php

    <?php
    $worker = new GearmanWorker();
    $worker->addServer();
    // 注册事件及事件处理函数
    $worker->addFunction("testFunction","handler");
    // 运行worker
    while ($worker->work());
    function handler(GearmanJob $job) {
        $workload = json_decode($job->workload());
        echo "received: " . print_r($workload,1);
    }
    ?>

可以命令行运行 php client.php , php worker.php ，这里启动顺序最好是先启动worker，这样事件可以得到及时处理；也可以先启动client，事件会在Gearman中排队，等待worker处理；

还可以启动多个worker，Gearman会自动进行负载均衡，分配到不同的worker进行处理。

大家可以发掘更多应用场景...


# Gearman 的高级特性
在一个 Web 应用程序内可能有许多地方都会用到Gearman。可以导入大量数据、发送许多电子邮件、编码视频文件、挖据数据并构建一个中央日志设施—所有这些均不会影响站点的体验和响应性。可以并行地处理数据。而且，由于Gearman协议是独立于语言和平台的，所以您可以在解决方案中混合编程语言。比如，可以用PHP编写一个 producer，用 C、Ruby 或其他任何支持 Gearman 库的语言编写 worker。
一个连接客户机和 worker 的 Gearman 网络实际上可以使用任何您能想象得到的结构。很多配置能够运行多个代理并将 worker 分配到许多机器上。负载均衡是隐式的：每个可操作的可用 worker（可能是每个 worker 主机具有多个 worker）从队列中拉出作业。一个作业能够同步或异步运行并具有优先级。

Gearman 的最新版本已经将系统特性扩展到了包含持久的作业队列和用一个新协议来通过 HTTP提交工作请求。对于前者，Gearman工作队列保存在内存并在一个关系型数据库内存有备份。这样一来，如果Gearman守护程序故障，它就可以在重启后重新创建这个工作队列。另一个最新的改良通过一个memcached集群增加队列持久性。memcached 存储也依赖于内存，但被分散于几个机器以避免单点故障。

Gearman 是一个刚刚起步却很有实力的工作分发系统。据 Gearman 的作者 Eric Day 介绍，Yahoo! 在 60 或更多的服务器上使用 Gearman 每天处理 600 万个作业。新闻聚合器Digg也已构建了一个相同规模的 Gearman 网络，每天可处理 400,000个作业。Gearman的一个出色例子可以在Narada这个开源搜索引擎（参见 参考资料）中找到。

Gearman 的未来版本将收集并报告统计数据、提供高级监视和缓存作业结果等。为了跟踪这个Gearman项目，可以订阅它的 Google 组，或访问 Freenode 上它的 IRC 频道 #gearman。