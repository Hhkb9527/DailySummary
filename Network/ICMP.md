## 介绍

ICMP协议是IP协议的附属协议，由于IP协议并不提供可靠传输，即使数据丢失了也不知道。为了提高IP数据报的交付成功的机会，在网络层提供了`ICMP协议`。在IP丢包的时候ICMP可以告诉源主机丢包的原因

ICMP的报文作为IP数据报的数据，加上IP首部组成IP数据报发送出去。ICMP报文格式如图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019110615170716.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwODYwODUy,size_16,color_FFFFFF,t_70)

#### ICMP报文种类
ICMP报文分为两种：`ICMP差错报文` 和 `ICMP询问报文`

ICMP报文的前4个字节是统一的格式，共有三个字段：类型、代码、校验和。接着四个字节和ICMP类型有关，最后面是数据字段，长度取决于ICMP报文类型。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019110615281041.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwODYwODUy,size_16,color_FFFFFF,t_70)
在IP数据报的首部、以太网帧首部、传输层TCP/UDP首部都存在校验和

其中IP数据报，以太网帧的首部校验和只校验首部。传输层和这里的ICMP首部校验和将首部和数据部分共同校验。

#### ICMP差错报文
所有的ICMP差错报告报文都具有同样的格式。
把收到的需要进行差错报告的IP数据报的首部和前8个字节提取出来，作为ICMP报文的数据字段。再加上ICMP报文的前8个字节，就构成了ICMP差错报告报文。

提取IP数据报数据部分的前8个字节是为了获取传输层的端口号（对于TCP/UDP），以及传输层报文的发送序号（对于TCP）。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191106153200331.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwODYwODUy,size_16,color_FFFFFF,t_70)

#### ICMP询问报文
常用的ICMP询问报文有两种：`回送请求与回答报文` 和 `时间戳请求与回答报文`

**1）** 回送请求与回答报文，主机或路由器向一个特定的目的主机发出的询问，收到请求的主机必须给发送请求的主机或路由器发送ICMP回送回答报文。`这种询问用于测试目的站是否可达以及了解有关状态。`
**2）** 时间戳请求与回答报文，请求某个主机或路由器回答当前的日期和时间，可用来进行时钟同步和测量时间。

#### ICMP应用举例
`ping`应用程序，用于测试两个主机之间的连通性。ping 使用的就是ICMP询问报文中的`回送请求与回答报文`。ping应用程序并`不经过传输层`，而是直接应用层使用网络层。

windows下测试结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191106155744990.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwODYwODUy,size_16,color_FFFFFF,t_70)

`traceroute`应用，用来跟踪一个IP数据报从源主机到目的主机的`路径`。windows下是`tracert`命令。

原理：
traceroute 从源主机向目的主机发送一连串的IP数据报，数据报中封装的是无法交付的UDP数据报。设置第一个IP数据报的TTL=1，当IP数据报到达路径上的第一个路由器后（第一跳），TTL-=1，由于TTL==0，因此路由器将IP数据报丢弃并向源主机发送`ICMP时间超时`差错报告报文。

源主机收到ICMP差错报告报文后，发送第二个IP数据报，这时TTL=2，同样经过两跳后TTL变为0，路由器将IP数据报丢弃并向源主机发送`ICMP时间超时`差错报告报文。

一直重复此步骤，直到TTL的大小可以让数据递达目的端。但由于IP数据报中封装的是不可交付的UDP数据报，因此目的主机向源主机发送`ICMP终点不可达`差错报告报文。

这样，源主机通过每次收到的ICMP报文达到了目的。

> 主要参考书籍 《计算机网络第五版》