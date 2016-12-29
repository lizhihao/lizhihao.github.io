# TCP: SYN ACK FIN RST PSH URG

---

# 三次握手Three-way Handshake 

一个虚拟连接的建立是通过三次握手来实现的 

1. (B) --> [SYN] --> (A) 

假如服务器A和客户机B通讯. 当A要和B通信时，B首先向A发一个SYN (Synchronize) 标记的包，告诉A请求建立连接. 

注意: 一个 SYN包就是仅SYN标记设为1的TCP包(参见TCP包头Resources). 认识到这点很重要，只有当A受到B发来的SYN包，才可建立连接，除此之外别无他法。因此，如果你的防火墙丢弃所有的发往外网接口的SYN包，那么你将不 能让外部任何主机主动建立连接。 

2. (B) <-- [SYN/ACK] <--(A) 

接着，A收到后会发一个对SYN包的确认包(SYN/ACK)回去，表示对第一个SYN包的确认，并继续握手操作. 

注意: SYN/ACK包是仅SYN 和 ACK 标记为1的包. 

3. (B) --> [ACK] --> (A) 

B收到SYN/ACK 包,B发一个确认包(ACK)，通知A连接已建立。至此，三次握手完成，一个TCP连接完成 

Note: ACK包就是仅ACK 标记设为1的TCP包. 需要注意的是当三此握手完成、连接建立以后，TCP连接的每个包都会设置ACK位 

这就是为何连接跟踪很重要的原因了. 没有连接跟踪,防火墙将无法判断收到的ACK包是否属于一个已经建立的连接.一般的包过滤(Ipchains)收到ACK包时,会让它通过(这绝对不是个 好主意). 而当状态型防火墙收到此种包时，它会先在连接表中查找是否属于哪个已建连接，否则丢弃该包 

# 四次握手Four-way Handshake 

四次握手用来关闭已建立的TCP连接 

1. (B) --> ACK/FIN --> (A) 

2. (B) <-- ACK <-- (A) 

3. (B) <-- ACK/FIN <-- (A) 

4. (B) --> ACK --> (A) 

注意: 由于TCP连接是双向连接, 因此关闭连接需要在两个方向上做。ACK/FIN 包(ACK 和FIN 标记设为1)通常被认为是FIN(终结)包.然而, 由于连接还没有关闭, FIN包总是打上ACK标记. 没有ACK标记而仅有FIN标记的包不是合法的包，并且通常被认为是恶意的 

连接复位Resetting a connection 

四次握手不是关闭TCP连接的唯一方法. 有时,如果主机需要尽快关闭连接(或连接超时,端口或主机不可达),RST (Reset)包将被发送. 注意在，由于RST包不是TCP连接中的必须部分, 可以只发送RST包(即不带ACK标记). 但在正常的TCP连接中RST包可以带ACK确认标记 

请注意RST包是可以不要收到方确认的? 

无效的TCP标记Invalid TCP Flags 

到目前为止，你已经看到了 SYN, ACK, FIN, 和RST 标记. 另外，还有PSH (Push) 和URG (Urgent)标记. 

最常见的非法组合是SYN/FIN 包. 注意:由于 SYN包是用来初始化连接的, 它不可能和 FIN和RST标记一起出现. 这也是一个恶意攻击. 

由于现在大多数防火墙已知 SYN/FIN 包, 别的一些组合,例如SYN/FIN/PSH, SYN/FIN/RST, SYN/FIN/RST/PSH。很明显，当网络中出现这种包时，很你的网络肯定受到攻击了。 

别的已知的非法包有FIN (无ACK标记)和"NULL"包。如同早先讨论的，由于ACK/FIN包的出现是为了关闭一个TCP连接，那么正常的FIN包总是带有 ACK 标记。"NULL"包就是没有任何TCP标记的包(URG,ACK,PSH,RST,SYN,FIN都为0)。 

到目前为止，正常的网络活动下，TCP协议栈不可能产生带有上面提到的任何一种标记组合的TCP包。当你发现这些不正常的包时，肯定有人对你的网络不怀好 意。 

UDP (用户数据包协议User Datagram Protocol) 
TCP是面向连接的，而UDP是非连接的协议。UDP没有对接受进行确认的标记和确认机制。对丢包的处理是在应用层来完成的。(or accidental arrival). 

此处需要重点注意的事情是：在正常情况下，当UDP包到达一个关闭的端口时，会返回一个UDP复位包。由于UDP是非面向连接的, 因此没有任何确认信息来确认包是否正确到达目的地。因此如果你的防火墙丢弃UDP包，它会开放所有的UDP端口(?)。 

由于Internet上正常情况下一些包将被丢弃，甚至某些发往已关闭端口(非防火墙的)的UDP包将不会到达目的，它们将返回一个复位UDP包。 

因为这个原因，UDP端口扫描总是不精确、不可靠的。 

看起来大UDP包的碎片是常见的DOS (Denial of Service)攻击的常见形式 (这里有个DOS攻击的例子，http://grc.com/dos/grcdos.htm ). 

ICMP (网间控制消息协议Internet Control Message Protocol) 
如同名字一样， ICMP用来在主机/路由器之间传递控制信息的协议。 ICMP包可以包含诊断信息(ping, traceroute - 注意目前unix系统中的traceroute用UDP包而不是ICMP)，错误信息(网络/主机/端口 不可达 network/host/port unreachable), 信息(时间戳timestamp, 地址掩码address mask request, etc.)，或控制信息 (source quench, redirect, etc.) 。 

你可以在http://www.iana.org/assignments/icmp-parameters中找到 ICMP包的类型。 

尽管ICMP通常是无害的，还是有些类型的ICMP信息需要丢弃。 

Redirect (5), Alternate Host Address (6), Router Advertisement (9) 能用来转发通讯。 

Echo (8), Timestamp (13) and Address Mask Request (17) 能用来分别判断主机是否起来，本地时间 和地址掩码。注意它们是和返回的信息类别有关的。 它们自己本身是不能被利用的，但它们泄露出的信息对攻击者是有用的。 

ICMP消息有时也被用来作为DOS攻击的一部分(例如：洪水ping flood ping,死 ping ?呵呵，有趣 ping of death)?/p> 

包碎片注意A Note About Packet Fragmentation 

如果一个包的大小超过了TCP的最大段长度MSS (Maximum Segment Size) 或MTU (Maximum Transmission Unit)，能够把此包发往目的的唯一方法是把此包分片。由于包分片是正常的，它可以被利用来做恶意的攻击。 

因为分片的包的第一个分片包含一个包头，若没有包分片的重组功能，包过滤器不可能检测附加的包分片。典型的攻击Typical attacks involve in overlapping the packet data in which packet header is 典型的攻击Typical attacks involve in overlapping the packet data in which packet header isnormal until is it overwritten with different destination IP (or port) thereby bypassing firewall rules。包分片能作为 DOS 攻击的一部分，它可以crash older IP stacks 或涨死CPU连接能力。 

Netfilter/Iptables中的连接跟踪代码能自动做分片重组。它仍有弱点，可能受到饱和连接攻击，可以把CPU资源耗光。

