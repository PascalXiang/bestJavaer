# ICMP 协议

* [ICMP 协议](#icmp-协议)
   * [什么是 ICMP](#什么是-icmp)
      * [ICMP 的主要功能](#icmp-的主要功能)
      * [ICMP 在 IPv4 和 IPv6 的封装](#icmp-在-ipv4-和-ipv6-的封装)
   * [ICMP 的主要消息](#icmp-的主要消息)
      * [ICMP 目标不可达(类型 3)](#icmp-目标不可达类型-3)
      * [ICMP 重定向消息(类型 5)](#icmp-重定向消息类型-5)
      * [ICMP 超时消息(类型 11)](#icmp-超时消息类型-11)
      * [ICMP 回送消息(类型 0 和 类型 8)](#icmp-回送消息类型-0-和-类型-8)
      * [其他 ICMP 消息](#其他-icmp-消息)
         * [ICMP 原点抑制消息(类型 4)](#icmp-原点抑制消息类型-4)
         * [ICMP 路由器探索消息(类型 9、10)](#icmp-路由器探索消息类型-910)
         * [ICMP 地址掩码消息(类型 17、18)](#icmp-地址掩码消息类型-1718)
   * [ICMPv6](#icmpv6)
      * [ICMPv6 的作用](#icmpv6-的作用)
      * [ICMPv6 邻居探索](#icmpv6-邻居探索)
      * [ICMPv6 的组播收听发现协议](#icmpv6-的组播收听发现协议)
   * [与 ICMP 有关的攻击](#与-icmp-有关的攻击)

> 这是计算机网络连载系列的第九篇文章，前八篇文章见
>
> [计算机网络基础知识总结](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247486242&idx=1&sn=fac49b0b79515a5ed6afd4b341aff87b&chksm=e999fe30deee772637e1c52fb9001c60e60a772e7adba6701329c81974e76c57bb7b2e570225&token=850264305&lang=zh_CN#rd)
>
> [TCP/IP 基础知识总结](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247486408&idx=1&sn=c332ae7ae448f3eb98865003ecade589&chksm=e999fedadeee77cc6281d1b170bd906b58220d6cd83054bc741821f4167f1f18ceee9ba0e449&token=850264305&lang=zh_CN#rd)
>
> [计算机网络应用层](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247486507&idx=1&sn=622cc363b34bce54f4953076faa1cad6&chksm=e999f939deee702f2444df83ad9805de8c70fb88b89d299fdf0a82b3463e253f32372963c039&token=1398464113&lang=zh_CN#rd)
>
> [计算机网络传输层](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247487108&idx=1&sn=7b47f421bb1dee4edb357a10399b7fec&chksm=e999fb96deee7280a17bfff44c27ef11a60e93e48f9da738670a779ecf6accb5a6a4ebd3cbcc&token=1398464113&lang=zh_CN#rd)
>
> [计算机网络网络层](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247487683&idx=1&sn=e0949e72e039759545450852d8bc0ada&chksm=e999e5d1deee6cc7ab9e42b50329924fee39c45955516b406046605d27928825a0f628d13e7c&token=1398464113&lang=zh_CN#rd)
>
> [计算机网络链路层](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247488884&idx=1&sn=0fdb91b7f5081d2e24c82d891fcc6126&chksm=e999e066deee69704d162b97be2ff0d33225fa9a3d12e4d3bec90a34996e7db7134535f36e8e&token=1398464113&lang=zh_CN#rd)
>
> [计算机网络 ARP 协议](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247487804&idx=1&sn=f001a24a308053b3723dfb12d36045ee&chksm=e999e42edeee6d383fbb411792e22e4028bb8c2441255786f50cf848443af7b1bd5e382078dc&token=1398464113&lang=zh_CN#rd)
>
> [计算机网络 DNS 协议](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247487880&idx=1&sn=fd38ce30ae82fa7d08e5f83fabb9d497&chksm=e999e49adeee6d8c1adacbfe27dc59097e4cb9d39c6a04802b0fe61877653330e75721cbde0b&token=1398464113&lang=zh_CN#rd)

我们之前的文章中了解过 TCP/IP 协议，我那时候码了一句

![image-20220427085343138](https://tva1.sinaimg.cn/large/e6c9d24ely1h1nzv65vpqj22da09277a.jpg)

原文链接见如下：

[TCP/IP 基础知识总结](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247486408&idx=1&sn=c332ae7ae448f3eb98865003ecade589&chksm=e999fedadeee77cc6281d1b170bd906b58220d6cd83054bc741821f4167f1f18ceee9ba0e449&token=2029952495&lang=zh_CN#rd)

下面我们就来真正认识一下 ICMP 协议。

## 什么是 ICMP

ICMP 的全称是 *Internet Control Message Protocol(互联网控制协议)*，它是一种互联网套件，它用于**IP 协议中发送控制消息**。也就是说，ICMP 是依靠 IP 协议来完成信息发送的，它是 IP 的主要部分，但是从体系结构上来讲，它位于 IP 之上，因为 ICMP 报文是承载在 IP 分组中的，就和 TCP 与 UDP 报文段作为 IP 有效载荷被承载那样。这也就是说，当主机收到一个指明上层协议为 ICMP 的 IP 数据报时，它会分解出该数据报的内容给 ICMP，就像分解数据报的内容给 TCP 和 UDP 一样。

ICMP 协议和 TCP、UDP 等协议不同，它不用于传输数据，只是用来发送消息。由于 IP 协议现在有两类版本：IPv4 和 IPv6 ，所以 ICMP 也有两个版本：**ICMPv4 和 ICMPv6**。

### ICMP 的主要功能

对于 ICMP 的功能，主要分为两个：

* ICMP 的第一个功能是**确认 IP 包是否能够成功到达目标地址**，当两个设备进行互联时，其中一个设备发送给另一个设备的 IP 包如果没有到达，就会生成 ICMP 数据包发送给设备共享。
* ICMP 的第二个功能是进行网络诊断，经常使用 ICMP 数据包的两个终端程序是 `ping` 和 `traceroute`，traceroute 程序用于显示两台互联网设备之间可能的路径并测量数据包在 IP 网络上的时延。ping 程序是 traceroute 的简化版本，我们经常使用 ping 命令来测试两台设备之间网络是否正常，ping 还可以显示两台设备之间的延迟情况，并准确报告数据包到达目的地并返回后所花费的时间。

![image-20220427100735542](https://tva1.sinaimg.cn/large/e6c9d24ely1h1o2018oudj20p80700uk.jpg)

<div align = "center">图 9-1</div>

现在我们知道了，如果在 IP 通信过程中由于某个 IP 包由于某种原因未能到达目标主机，那么这个具体的原因将由 ICMP 进行通知，下面是一个 ICMP 的通知示意图：

![image-20220427085402123](https://tva1.sinaimg.cn/large/e6c9d24ely1h1nzvi1hajj21e90u0wi3.jpg)

<div align = "center">图 9-2</div>

这张图显示了这样的一个过程：主机 A 想要给主机 B 发送一个 IP 数据包，主机 A 发送的数据包经过路由器 1 到达了路由器 2 ，由于路由器 2 不知道主机 B 的 MAC 地址，所以路由器 2 发送了一个 ARP 请求，没有回应，再经过重试时间后再次发送，还没有回应。。。。。。经过多次 ARP 请求后没有得到回应后，路由器 2 就会给主机 A 发送一个 ICMP 消息，告诉其发送的 IP 数据包没有到达主机 B 。

我们只是画出了路由器 2 给主机 A 发送了一个 ICMP 数据包，而没有画出具体的通知类型，但实际情况是，上面发送的是*目标不可达类型(Destination unreachable)*，ICMP 的通知类型也有很多，下面我为你汇总了 ICMP 数据包的具体通知类型。

![image-20220427085438631](https://tva1.sinaimg.cn/large/e6c9d24ely1h1nzw4wsl8j21kx0u042s.jpg)

<div align = "center">表 9-1</div>

上表显示的 ICMP 通知类型主要分为两类：有关 IP 数据报传递的 ICMP 报文，这类报文也叫做*差错报文(error message)*，以及有关信息采集和配置的 ICMP 报文，这类报文也被称为查询报文、信息类报文。

信息类报文包括回送请求和回送应答(类型 8 和 类型 0 )，路由器公告和路由器请求(类型 9 和 类型10 )。最常见的差错报文类型包括目标不可达(类型 3 )、重定向(类型 5)、超时(类型 11)。

### ICMP 在 IPv4 和 IPv6 的封装

我们知道，ICMP 是承载在 IP 内部的，而且 IPv4 和 IPv6 的封装位置不同。

ICMP 在 IPv4 协议中的封装：

![image-20220427085704470](https://tva1.sinaimg.cn/large/e6c9d24ely1h1nzynm0qdj22b40hodhs.jpg)

<div align = "center">图 9-3</div>

ICMP 在 IPv6 协议中的封装：

![image-20220427085735883](https://tva1.sinaimg.cn/large/e6c9d24ely1h1nzz9wgo0j22ec0h0q5k.jpg)

<div align = "center">图 9-4</div>

上面两张图显示了 ICMPv4 和 ICMPv6 的报文格式。开头的 4 个字节在所有的报文中都是一样的。但是其余部分在不同的报文中却不一样。

ICMP 头部包含了整个 ICMP 数据段的校验和，具体格式如下：

![image-20220427085819658](https://tva1.sinaimg.cn/large/e6c9d24ely1h1nzzywhm8j22440pewhx.jpg)

<div align = "center">图 9-5</div>

所有的 ICMP 报文都以 8 位的*类型(Type)*和*代码(Code)*字段开始，其后的 16 位校验和涵盖了整个报文，ICMPv4 和 ICMPv6 种的类型和代码字段是不同的。

## ICMP 的主要消息

### ICMP 目标不可达(类型 3)

我们知道，路由器无法将 IP 数据报发送给目标地址时，会给发送端主机返回一个`目标不可达(Destination Unreachable Message)` 的 ICMP 消息，并且会在消息中显示不可达的具体原因。

![image-20220427090026179](https://tva1.sinaimg.cn/large/e6c9d24ely1h1o026ga7zj22gg0s6gw4.jpg)

<div align = "center">图 9-6</div>

实际通信过程中会显示各种各样的不可达信息，比如错误代码时 1 表示主机不可达，它指的是路由表中没有主机的信息，或者主机没有连接到网络的意思。一些 ICMP 不可达信息的具体原因如下

![image-20220427090049836](https://tva1.sinaimg.cn/large/e6c9d24ely1h1o02kqz44j21960u043s.jpg)

<div align = "center">表 9-2</div>

### ICMP 重定向消息(类型 5)

如果路由器发现发送端主机使用了次优的路径发送数据，那么它会返回一个 *ICMP 重定向(ICMP Redirect Message)* 的消息给这个主机。这个 ICMP 重定向消息包含了最合适的**路由信息和源数据**。这种情况会发生在路由器持有更好的路由信息的情况下。路由器会通过这样的 ICMP 消息给发送端主机一个更合适的发送路由。

![image-20220427090252943](https://tva1.sinaimg.cn/large/e6c9d24ely1h1o04pi7zhj222g0u0wiv.jpg)

<div align = "center">图 9-7</div>

主机 Host 的 IP 地址为 10.0.0.100。主机的路由表中有一个默认路由条目，指向路由器 G1 的 IP 地址 10.0.0.1 作为默认网关。路由器 G1 在将数据包转发到目的网络 X 时，会使用路由器 G2 的 IP 地址 10.0.0.2 作为下一跳。 

当主机向目的网络 X 发送数据包时，会发生以下情况：

1. IP 地址为 10.0.0.1 的网关 G1 在其所连接的网络上接收来自 10.0.0.100 的数据包。
2. 网关 G1 检查其路由表，并在通往数据包目的网络 X 的路由中获取下一个网关 G2 的 IP 地址 10.0.0.2。

3. 如果 G2 和 IP 数据包的源地址标识的主机位于同一网络中(也就是 Host 主机)，那么 G1 会向主机发送 ICMP 重定向消息。ICMP 重定向消息建议主机直接将发送到网络 X 的数据包发送至 G2，因为 Host - G2 这是通往目的地的较短路径。

4. 网关 G1 将原始数据包转发到其目的地。

当然，根据主机的配置，Host 主机也可以选择忽略 G1 给它发送的 ICMP 重定向消息。但是，这样就享受不到 ICMP 重定向带来的两大好处，即

* 优化数据在网络中的转发路径；流量更快到达目的地
* 降低网络资源利用率，例如带宽和路由器 CPU 负载

如果 Host 主机采用了 ICMP 提供的重定向路径的话，那么 Host 就会直接把数据包发送至网络 X，如下图所示

![image-20220427090318204](https://tva1.sinaimg.cn/large/e6c9d24ely1h1o055r8rtj22ec0q40wy.jpg)

<div align = "center">图 9-8</div>

在主机为 G2 作为下一跳的网络 X 创建路由缓存条目后，这些优势在网络中可见：

- 交换机和路由器 G1 之间链路的带宽利用率在两个方向上都会降低。
- 由于从主机到网络 X 的流量不再流经此节点，因此路由器 G1 的 CPU 使用率降低。
- 主机和网络 X 之间的端到端网络延迟得到改善。 

ICMP 重定向示例如下：

![image-20220427090358911](https://tva1.sinaimg.cn/large/e6c9d24ely1h1o05v2czaj214k0u0grq.jpg)

<div align = "center">图 9-9</div>

### ICMP 超时消息(类型 11)

在 IP 数据包中有一个叫做 *TTL(Time To Live, 生存周期)* ，它的值在每经过路由器一跳之后都会减 1，IP 数据包减为 0 时会被丢弃。此时，IP 路由器会发送一个 ICMP 超时消息(ICMP TIme Exceeded Message, 错误号 0)发送给主机，通知该包已经被丢弃。

设置生存周期的主要目的就是为了防止路由器控制遇到问题发生循环状况时，避免 IP 包无休止的在网络上转发，如下图所示：

![image-20220427090758378](https://tva1.sinaimg.cn/large/e6c9d24ely1h1o0a048yoj21in0u0gph.jpg)

<div align = "center">图 9-10</div>

> 这里给大家推荐一款比较好用的追踪超时消息的工具 `traceroute`，它可以显示出由执行程序的主机到达特定主机之前需要经过多少路由器。 traceroute 的官网如下 http://www.traceroute.org

### ICMP 回送消息(类型 0 和 类型 8)

ICMP 回送消息用于判断相互通信的主机之间是否连通，也就是判断所发送的数据包是否能够到达目标主机。可以向对端主机发送*回送请求的消息(ICMP Echo Request Message,类型 8)*，也可以接收对端主机发送来的回送消息(ICMP Echo Reply Message, 类型 0 )。网络上最常用的 ping 命令就是利用这个实现的。

![image-20220427091635535](https://tva1.sinaimg.cn/large/e6c9d24ely1h1o0iz1r8vj21vu0u0td2.jpg)

<div align = "center">图 9-11</div>

### 其他 ICMP 消息

#### ICMP 源点抑制消息(类型 4)

在使用低速率网络的情况下，网络通信可能会遇到网络拥堵的情况下，ICMP 的源点抑制就是为了应对这种情况的。当路由器向低速线路发送数据时，其发送队列的残存数据报变为 0 从而无法发送时，可以向 IP 数据报的源地址发送一个 *ICMP 源点抑制(ICMP Source Quench Message)* 消息，收到这个消息的主机了解到线路某处发生了拥堵，从而抑制 IP 数据报的发送。

![image-20220427091658785](https://tva1.sinaimg.cn/large/e6c9d24ely1h1o0jdpl6sj21yd0u0gq6.jpg)

<div align = "center">图 9-12</div>

不过这个 ICMP 消息可能会引起不公平的网络通信，一般不被使用。

#### ICMP 路由器探索消息(类型 9、10)

ICMP 路由器探索消息主要用于*路由器发现(Router Discovery, RD)*，它主要分为两种，*路由器请求(Router Solicitation, 类型 10)*和*路由器响应(Router Advertisement, 类型 9)*。主机会在任意路由连接组播的网络上发送一个 RS 消息，想要选择一个路由器进行学习，以此来作为默认路由，而相对应的该路由会发送一个 RA 消息来作为默认路由的响应。

<img src="https://tva1.sinaimg.cn/large/e6c9d24ely1h1o0jptjboj20u00y50uw.jpg" alt="image-20220427091718607" style="zoom:50%;" />

<div align = "center">图 9-13</div>

#### ICMP 地址掩码消息(类型 17、18)

主要用于主机或者路由器想要了解子网掩码的情况。可以向那些目标主机或路由器发送 *ICMP 地址掩码请求消息(ICMP Address Mask Request, 类型 17)*和 *ICMP 地址掩码应答消息(ICMP Address Mask Reply, 类型 18)*获取子网掩码信息。

随着 ICMP 的不断更新，很多 ICMP 报文已经不在使用了，这些不再使用的报文类型包括：

* 信息请求报文和回答报文。
* 地址掩码请求报文和回答报文。
* 路由器请求和通告报文。
* 源点抑制报文。

所以大家只要了解下面这几个 ICMP 报文类型就可以了。

| ICMP 报文种类 | 类型值 | 报文类型         |
| ------------- | ------ | ---------------- |
| 差错报告报文  | 3      | 目标不可达       |
| 差错报告报文  | 5      | 重定向或路由改变 |
| 差错报告报文  | 11     | ICMP 超时        |
| 差错报告报文  | 12     | 参数问题         |
| 查询报文      | 0 或 8 | 回送请求或回答   |

<div align = "center">表 9-3</div>

这里需要解释一下这个参数问题，当路由器或者目标主机收到的数据包中的首部字段的值存在问题，就会丢弃数据报，同时发送 ICMP 响应消息。

这里需要注意下，遇到下面这些情况，是不会发送 ICMP 差错报文的。

* 对具有特殊地址的 IP 比如 127.0.0.1 ，0.0.0.0 是不发送 ICMP 差错报文的。
* 对具有多播地址的数据报，都不发送 ICMP 报文。
* 对 ICMP 差错报文，不再发送 ICMP 差错报文。
* 对第一个分片数据报的后续所有数据报，都不发送 ICMP 差错报文。

## ICMPv6

### ICMPv6 的作用

IPv4 中 ICMP 仅仅作为一个辅助作用支持 IPv4。也就是说，在 IPv4 时期，即使没有 ICMP，也能进行正常的 IP 数据包的发送和接收，也就是 IP 通信。但是在 IPv6 中，ICMP 的作用被放大了，如果没有 ICMP，则不能进行正常的 IP 通信。

尤其在 IPv6 中，从 IP 定位 MAC 地址的协议从 ARP 转为 ICMP 的*邻居探索消息(Neighbor Discovery)* 。这种邻居探索消息融合了 IPv4 的 ARP、ICMP 重定向以及 ICMP 的路由选择等功能于一体。甚至还提供了自动设置 IP 的功能。

在 IPv6 中，ICMP 消息主要分为两类：一类是错误消息，一类是信息消息。0 - 127 属于错误消息；128 - 255 属于信息消息。

RFC 2463 中描述了以下消息类型：

![image-20220427092600633](https://tva1.sinaimg.cn/large/e6c9d24ely1h1o0srrejvj20vf0u00wm.jpg)

<div align = "center">表 9-4</div>

ICMPv6 除了包含 ICMPv4 的所有功能外，还有两个额外的功能。

### ICMPv6 邻居探索

邻居探索是 ICMPv6 非常重要的功能，主要表示的类型是 133 - 137 之间的消息叫做**邻居探索消息**。这种邻居探索消息对于 IPv6 通信起到举足轻重的作用。邻居请求消息用于查询 IPv6 地址于 MAC 地址的对应关系。邻居请求消息利用 IPv6 的多播地址实现传输。

![image-20220427094754006](https://tva1.sinaimg.cn/large/e6c9d24ely1h1o1fjt53gj21pw0u0dko.jpg)

<div align = "center">图 9-14</div>

此外，由于 IPv6 实现了即插即用的功能，所以在没有 DHCP 服务器的环境下也能实现 IP 地址的自动获取。如果是一个没有路由器的网络，就使用 MAC 地址作为链路本地单播地址。如果在一个有路由器的网络环境中，可以从路由器获得 IPv6 地址的前面部分，后面部分使用 MAC 地址进行设置。此时可以利用路由器请求消息和路由器公告消息进行设置。

![image-20220427094830245](https://tva1.sinaimg.cn/large/e6c9d24ely1h1o1g6hvxlj21v00u0gq0.jpg)

<div align = "center">图 9-15</div>

### ICMPv6 的组播收听发现协议

*组播收听发现协议(MLD，Multicast Listener Discovery)* 由子网内的组播成员管理。MLD 协议定义了3条ICMPv6 消息：

* 组播收听查询消息：组播路由器向子网内的组播收听者发送此消息，以获取组播收听者的状态。
* 组播收听者报告消息：组播收听者向组播路由器汇报当前状态，包括离开某个组播组。
* 组播收听者。

## 与 ICMP 有关的攻击

涉及 ICMP 攻击主要分为 3 类：**泛洪(flood)、炸弹(bomb) 和信息泄露(information disclsure)**。

* 泛洪将会产生大量流量，导致针对一台或者多台计算机的有效 Dos 攻击。
* 炸弹指的是发送经过特殊构造的报文，这类报文能够导致 IP 或者 ICMP 的处理失效或者崩溃。
* 信息泄露本身不会造成危害，但是能够帮助辅助其他攻击。

针对 TCP 的 ICMP 攻击已经记录在了 RFC5927 中。

![image-20210717083948590](https://tva1.sinaimg.cn/large/008i3skNly1gsjnhb9f5xj319s0tsn4g.jpg)

![image-20210717084050334](https://tva1.sinaimg.cn/large/008i3skNly1gsjnidv1r3j315s0fs40g.jpg)



