---
author: lizhihao
comments: true
date: 2015-03-04 03:03:18+00:00
layout: post
slug: study-restful
title: 'Study Restful'
thread: 98
categories:
- Web API 
---

REST
--
含状态传输（Representational State Transfer，简称REST）是Roy Fielding博士在2000年他的博士论文中提出来的一种软件架构风格。
目前在三种主流的Web服务实现方案中，因为REST模式与复杂的SOAP和XML-RPC相比更加简洁，越来越多的web服务开始采用REST风格设计和实现。例如，Amazon.com提供接近REST风格的Web服务进行图书查找；雅虎提供的Web服务也是REST风格的。

## 要点及标准
需要注意的是，REST是设计风格而不是标准。REST通常基于使用HTTP，URI，和XML以及HTML这些现有的广泛流行的协议和标准。

资源是由URI来指定。
对资源的操作包括获取、创建、修改和删除资源，这些操作正好对应HTTP协议提供的GET、POST、PUT和DELETE方法。
通过操作资源的表现形式来操作资源。
资源的表现形式则是XML或者HTML，取决于读者是机器还是人，是消费web服务的客户软件还是web浏览器。当然也可以是任何其他的格式。


## REST的要求
客户端和服务器结构
连接协议具有无状态性
能够利用Cache机制增进性能
层次化的系统
所需代码 - Javascript （可选）


## 关于状态
应该注意区别应用的状态和连接协议的状态。HTTP连接是无状态的（也就是不记录每个连接的信息），而REST传输会包含应用的所有状态信息，因此可以大幅降低对HTTP连接的重复请求资源消耗。


## 含状态传输的 Web 服务
含状态传输的 Web 服务（也称为 RESTful Web API）是一个使用HTTP并遵循REST原则的Web服务。它从以下三个方面资源进行定义：

直观简短的资源地址：URI，比如：http://example.com/resources/。
传输的资源：Web服务接受与返回的互联网媒体类型，比如：JSON，XML ，YAML 等。
对资源的操作：Web服务在该资源上所支持的一系列请求方法（比如：POST，GET，PUT或DELETE）。
下表列出了在实现 含状态传输的 Web 服务时HTTP请求方法的典型用途。

HTTP 请求方法在RESTful Web 服务中的典型应用

| 	资源 | GET | PUT | POST | DELETE |
| -----  |:-------------:| -----:|-----:|-----:|
| 	一组资源的URI，比如http://example.com/resources/|列出</b> URI，以及该资源组中每个资源的详细信息（后者可选）。|使用给定的一组资源<b>替换</b>当前整组资源。|在本组资源中<b>创建/追加</b>一个新的资源。 该操作往往返回新资源的URL。|<b>删除</b> 整组资源。|
| 	单个资源的URI，比如http://example.com/resources/142 | 获取指定的资源的详细信息，格式可以自选一个合适的网络媒体类型（比如：XML、JSON等）| <b>替换/创建</b> 指定的资源。并将其追加到相应的资源组中。| 把指定的资源当做一个资源组，并在其下<b>创建/追加</b>一个新的元素，使其隶属于当前资源|删除 指定的元素。|

PUT 和 DELETE 方法是幂等方法。GET方法是安全方法 （不会对服务器端有修改，因此当然也是幂等的）。

不像基于SOAP的Web服务，RESTful Web服务并没有的“正式”标准[2]。 这是因为REST是一种架构，而SOAP只是一个协议。虽然REST不是一个标准，但在实现RESTful Web服务时可以使用其他各种标准（比如HTTP，URL，XML，PNG等）。


## 实现举例
例如，一个简单的网络商店应用，

列举所有商品，

GET http://www.store.com/products
呈现某一件商品，

GET http://www.store.com/product/12345
下单购买，

POST http://www.store.com/order
<purchase-order>
  <item> ... </item1>
</purchase-order>


## REST的优点
可更高效利用缓存来提高响应速度
通讯本身的无状态性可以让不同的服务器的处理一系列请求中的不同请求，提高服务器的扩展性
浏览器即可作为客户端，简化软件需求
相对于其他叠加在HTTP协议之上的机制，REST的软件依赖性更小
不需要额外的资源发现机制
在软件技术演进中的长期的兼容性更好