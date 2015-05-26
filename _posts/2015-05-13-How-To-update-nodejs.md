---
author: lizhihao
comments: true
date: 2015-05-13 03:03:18+00:00
layout: post
slug: js
title: 'how to update nodejs'
thread: 98
categories:
- Js 
---

==nodejs - 如何完全更新==

----



Nodejs可以毫不犹豫地说一个版本狂魔，时不时就发布一个版本，而且还一直没有一个1.0版本，好囧呀，对于我们这些有强迫症的人来说，的确不是好事。

下面我就说一下Nodejs中常见的更新方式。

1. 更新你已经安装的NPM库，这个很简单，只需要运行。
        
        npm update –g
2. 更新Nodejs自身。一直依赖我都是下载最新版的源码，然后make install，及其繁琐。其实只需要运行以下2个命令即可：
    
        npm install –g n
        n latest
n可以下载任意版本的nodejs安装到本机，非常方便。

一行命令搞定，省去了重新编译安装的过程。
node有一个模块叫n（这名字可够短的。。。），是专门用来管理node.js的版本的。
首先安装n模块：
        
        npm install -g 
第二步：
升级node.js到最新稳定版
        
        n stable
是不是很简单？！
n后面也可以跟随版本号比如：
        
        n v0.10.26
或
        
        n 0.10.26
就这么简单，这可怎么办？？！！
另外分享几个npm的常用命令

        npm -v          #显示版本，检查npm 是否正确安装。
        npm install express   #安装express模块
        npm install -g express  #全局安装express模块
        npm list         #列出已安装模块
        npm show express     #显示模块详情
        npm update        #升级当前目录下的项目的所有模块
        npm update express    #升级当前目录下的项目的指定模块
        npm update -g express  #升级全局安装的express模块
        npm uninstall express  #删除指定的模块