---
author: lzh
date: 2017-02-24 07:30:17+00:00
layout: post
title: "Zookeeper不提供永久Watcher"
categories:
- zookeeper
---

	
Zookeeper不提供永久Watcher

------

1. ZooKeeper不提供一个持久的Watcher注册机制

如果Watcher的注册是持久的，那么必然导致服务端的每次数据更新都会通知到客户端——这在数据变更非常频繁且监听客户端特别多的场景下，ZooKeeper无法保证性能。
很多时候，我们需要的是最新数据，而不是每次变更！
每次在注册watcher之后都getData，保证数据版本是最新的，但相比较每次都通知优势还是很明显的，这样的处理在非常频繁更新数据的情况下少了许多中间数据变更的过程。

接下来我们来分析如果做到注册Watcher以后并获取最新数据的
client客户端执行getData方法后在zookeeper服务器端将执行FinalRequestProcessor的processRequest方法：


    case OpCode.getData: {  
                lastOp = "GETD";  
                GetDataRequest getDataRequest = new GetDataRequest();  
                ByteBufferInputStream.byteBuffer2Record(request.request,  
                        getDataRequest);  
                DataNode n = zks.getZKDatabase().getNode(getDataRequest.getPath());  
                if (n == null) {  
                    throw new KeeperException.NoNodeException();  
                }  
                Long aclL;  
                synchronized(n) {  
                    aclL = n.acl;  
                }  
                PrepRequestProcessor.checkACL(zks, zks.getZKDatabase().convertLong(aclL),  
                        ZooDefs.Perms.READ,  
                        request.authInfo);  
                Stat stat = new Stat();  
                byte b[] = zks.getZKDatabase().getData(getDataRequest.getPath(), stat,  
                        getDataRequest.getWatch() ? cnxn : null);  
                rsp = new GetDataResponse(b, stat);  
                break;  
            }  

并执行到以下代码：

    public byte[] getData(String path, Stat stat, Watcher watcher)  
            throws KeeperException.NoNodeException {  
        DataNode n = nodes.get(path);  
        if (n == null) {  
            throw new KeeperException.NoNodeException();  
        }  
        synchronized (n) {  
            n.copyStat(stat);  
            if (watcher != null) {  
                dataWatches.addWatch(path, watcher);  
            }  
            return n.data;  
        }  
    }  

我们可以清楚的看到首先将watch加入到dataWatches，换句话说就是先注册了watch然后返回了path的最新数据，这样就保证了如果在没有注册watch的这段时间内如果没有件听到数据的变化那么将通过n.data返回给client客户端