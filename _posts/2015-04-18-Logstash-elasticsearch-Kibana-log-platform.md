# 使用 Logstash + elasticsearch + Kibana快速搭建日志平台

标签（空格分隔）： logstatsh elasticsearch kenana log nxg

---


日志的分析和监控在系统开发中占非常重要的地位，系统越复杂，日志的分析和监控就越重要，常见的需求有:
 - 根据关键字查询日志详情
 - 监控系统的运行状况
 - 统计分析，比如接口的调用次数、执行时间、成功率等
 - 异常数据自动触发消息通知 
 - 基于日志的数据挖掘

很多团队在日志方面可能遇到的一些问题有:

 - 开发人员不能登录线上服务器查看详细日志，经过运维周转费时费力 
 - 日志数据分散在多个系统，难以查找
 - 日志数据量大，查询速度慢
 - 一个调用会涉及多个系统，难以在这些系统的日志中快速定位数据 
 - 数据不够实时

常见的一些重量级的开源Trace系统有

 - facebook scribe 
 - cloudera flume 
 - twitter zipkin 
 - storm

这些项目功能强大，但对于很多团队来说过于复杂，配置和部署比较麻烦，在系统规模大到一定程度前推荐轻量级下载即用的方案，比如logstash+elasticsearch+kibana(LEK)组合。

对于日志来说，最常见的需求就是收集、查询、显示，正对应logstash、elasticsearch、kibana的功能。

# logstash

logstash部署简单，下载一个jar就可以用了，对日志的处理逻辑也很简单，就是一个pipeline的过程

    inputs >> codecs >> filters >> outputs

看到logstash支持常见的日志类型，与其他监控系统的整合也很方便，可以将数据输出到zabbix、nagios、email等。
推荐用redis作为输入缓冲队列。
你还可以把数据统计后输出到graphite，实现统计数据的可视化显示。

# elasticsearch
elasticsearch是基于lucene的开源搜索引擎，近年来发展比较快，主要的特点有
1. real time
2. distributed
3. high availability
4. document oriented
5. schema free
6. restful api
elasticsearch的详细介绍以后再写，常用的一些资源如下

# kibana

kibana是一个功能强大的elasticsearch数据显示客户端，logstash已经内置了kibana，你也可以单独部署kibana，最新版的kibana3是纯html+js客户端，可以很方便的部署到Apache、Nginx等Http服务器。

kibana3的地址: https://github.com/elasticsearch/kibana 
kibana2的地址: https://github.com/rashidkpc/Kibana 
kibana3 demo地址: http://demo.kibana.org

### 索引
 - elasticsearch默认会按照分隔符对字段拆分，日志有些字段不要分词，比如url，可以为这类字段设置not_analyzed属性。
 - 设置multi-field-type属性可以将字段映射到其他类型。multi-field-type。
 - 大量日志导入时用bulk方式。
 - 对于日志查询来说，filter比query更快,过滤器里不会执行评分而且可以被自动缓存。query-dsl。
 - elasticsearch默认一个索引操作会在所有分片都完成对文档的索引后才返回，你可以把复制设置为异步来加快批量日志的导入。

### elasticsearch 优化
 - 优化JVM 
 - 优化系统可以打开最大文件描述符的数量 
 - 适当增加索引刷新的间隔

### 最佳实践
 - 首先你的程序要写日志
 - 记录的日志要能帮助你分析问题，只记录"参数错误"这样的日志对解决问题毫无帮助
 - 不要依赖异常，异常只处理你没考虑到的地方
 - 要记录一些关键的参数，比如发生时间、执行时间、日志来源、输入参数、输出参数、错误码、异常堆栈信息等
 - 要记录sessionid、transitionid、userid等帮你快速定位以及能把各个系统的日志串联起来的关键参数
 - 推荐纯文本+json格式
 - 使用队列
### 其他日志辅助工具
 - rsyslog
 - syslog-ng
 - graylog
 - fluentd
 - nxlog



