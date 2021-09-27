#### 什么是域名
域名是什么，域名就是 IP 地址，IP 地址是一个很大的数字，通常使用点分十进制来书写，例如：`127.0.0.1`，但是这并不方便人们记忆，你想要用百度搜索就要访问百度的服务器，可是让你记住这样一串魔幻数字，真的很困难，因此域名就诞生了

域名和IP地址对应，这样我们就不用记住那一串魔幻数字，只需要记住 `www.baidu.com`，它同样能够唯一的标识一台主机

#### hosts 文件
为了维护域名和IP地址的映射关系，早期使用的方法是：每台主机上都有一个hosts文件，这个文件里面记录了映射关系，我们在浏览器输入域名，浏览器就会去hosts文件找到对应IP地址，从而进行访问
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191105012401688.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwODYwODUy,size_16,color_FFFFFF,t_70)
但是随着域名的增多，一个hosts文件似乎搞不定了，而且一旦有了新的域名出现，必须要所有主机都更新hosts文件才可以保证正常访问，这未免也太麻烦了，而且怎么能要求所有用户都会更新hosts文件呢！

#### DNS 服务器
DNS 服务器也就是域名服务器，如果你需要通过某个域名访问服务器，那么只需要先访问下DNS服务器（DNS服务器维护一个主机名和IP地址之间对应关系的数据库），DNS服务器就会将域名对应的IP返回给你，就可以通过IP地址正常访问了

理论上来讲这样的DNS服务器，一台就够了，在一台机器上存储所有的域名和IP的对应关系，所有的主机访问时都先访问这台机器

但是这样做并不够高明，因为一旦这一台DNS服务器挂了，整个因特网不就瘫痪了

因此，`因特网采用的是层次树状的命名方法，并使用分布式的域名系统DNS`，即使某台DNS服务器出现问题，依旧保证域名解析不会受到影响

#### 域名到 IP 地址的解析过程
当一个应用进程需要把主机名解析为IP地址时，该进程就会调用`解析程序`，并成为DNS的一个客户，把待解析的域名放入DNS请求报文中，通过`UDP`用户数据报的方式发送给本地域名服务器。本地域名服务器在查找报文后，把对应的IP地址放入回答报文中返回。应用进程获取到目的主机的IP地址后就可以进行通信

若本地域名服务器不能回答该请求，则次域名服务器就暂时成为一个DNS客户，并向其他域名服务器发送查询请求，直到找到能够回答该请求的域名服务器为止。

#### 因特网的域名结构
因特网采用的是`层次树状`的命名方法，任何一个连接在因特网上的主机或路由器都有一个唯一的层次结构的名字，即`域名`，这里的域是名字空间中可被管理的划分，域还可以划分为子域，而子域还可以划分为子域的子域，这样就形成了顶级域，二级域，三级域等等...

每一个域名都是由标号序列组成，各个标号之间用`点`隔开
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191105130204639.png)
上图中的这个域名由三个标号组成，其中标号com是顶级域名、标号cctv是二级域名、标号mail是三级域名

域名中的标号由英文字母和数字组成，不区分大小写

- **顶级域名分为三大类：**
1）国家顶级域名：cn代表中国、us代表美国、uk代表英国
2）通用顶级域名：com公司企业、net网络服务机构、org非营利性组织
3）基础机构域名：arpa反向域名

- **我国把二级域名划分为`类别域名`和`行政区域名`：**
1）类别域名：com工商金融等企业、gov政府机构、org非营利性组织
2）行政区域名：bj北京市、js江苏省

用域名树表示因特网的域名系统：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191105131422293.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwODYwODUy,size_16,color_FFFFFF,t_70)

#### 域名服务器
我们不可能为每一级域名都配备一个相对应的域名服务器，这样的话域名服务器数量太多，效率会很低

实际上一个DNS服务器的管辖范围并不是以域为单位，而是以`区`为单位，区是DNS服务器的实际管辖范围，区可能小于域，但一定不可能大于域。

举例下图：
一个公司abc，下属部门x和y，部门x下又有u和v，y下又有t
图a中：abc公司只设定了一个区abc.com，区`abc.com`和域`abc.com`指的是同一件事
图b中：公司设定了两个`区abc.com`和`区y.abc.com`，这两个区都隶属于`域abc.com`，两个区都设置相应的权限域名服务器，可以看出区是域的子集
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019110513242497.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwODYwODUy,size_16,color_FFFFFF,t_70)
因特网上的DNS域名服务器同样是按照层次安排的，每一个域名服务器都只对域名体系中的一部分进行管辖

根据域名服务器所起的作用，可以将域名服务器划分为以下四种类型

**1）根域名服务器：** 所有的根域名服务器都知道所有的顶级域名服务器的域名和IP地址。根域名服务器是最重要的域名服务器，因为任何的本地域名服务器当不能解析域名的时候，必须要向根域名服务器进行求助，假设所有的根域名服务器都出现问题，那么整个因特网也就瘫痪了。

**2）顶级域名服务器：** 顶级域名服务器负责管理，在自己下面注册的所有二级域名服务器，当收到DNS请求时会做出相应应答

**3）权限域名服务器：** 权限域名服务器就是负责一个区的域名服务器，当权限域名服务器还不能给出查询结果时，会告诉发出查询请求的DNS用户，下一步去找哪个权限域名服务器

**4）本地域名服务器：** 当一个主机发出DNS请求时，这个查询请求报文就是发送给了本地域名服务器，

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191105133053569.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwODYwODUy,size_16,color_FFFFFF,t_70)

#### 域名解析过程（更详细）
首先注意两点：
**第一：** 主机向本地域名服务器的查询一般都是采用`递归查询`。递归是指如果本地域名服务器不知道被查询域名的IP地址，本地域名服务器就成为了一个新的DNS客户，**代替主机进行查询**，最后返回查询结果
**第二：** 本地域名服务器向根域名服务器的查询通常采用的是`迭代查询`。迭代查询是指当根据域服务器接收到本地域名服务器发送的迭代查询请求报文后时，要么给出所查询的IP地址，要么告诉服务器 "你下一步应该到哪个顶级域名服务器进行查询"，然后本地域名服务器**亲自去**顶级域名服务器查询；同样，顶级域名服务器接收到查询请求后，要么给出所查询的IP地址，要么告诉本地域名服务器 "你下一步应该到哪个权限域名服务器查询"，最后本地域名服务器得到IP地址，返回给主机。同样：本地域名服务器也可以采用递归查询。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191105153309551.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwODYwODUy,size_16,color_FFFFFF,t_70)
如上图a：假设域名为m.xyz.com的主机想要知道域名为y.abc.com的IP地址，步骤如下
**1）** 主机m.xyz.com先向本地域名服务器dns.xyz.com进行递归查询
**2）** 本地域名服务器采用迭代查询，首先向根域名服务器查询
**3）** 根域名服务器告诉本地域名服务器，下一次应该到顶级域名服务器dns.com查询
**4）** 本地域名服务器向顶级域名服务器dns.com查询
**5）** 顶级域名服务器dns.com告诉本地域名服务器，下一次应该到权限域名服务器dns.abc.com进行查询
**6）** 本地域名服务器向权限域名服务器dns.abc.com查询
**7）** 权限域名服务器dns.abc.com告诉本地域名服务器所查询的IP地址
**8）** 本地域名服务器将IP地址告诉主机m.xyz.com

#### 为提高效率的高速缓存
为了提高DNS的查询效率，减轻根域名服务器的负荷，并减少因特网上DNS查询数据报的数量，在域名服务器中使用了`高速缓存`，**高速缓存是用来存放最近查询过的域名以及从何处获取域名映射信息的记录**

例如：在上述查询过程中，假设不久前已经有其他用户查询过域名为y.abc.com的IP地址，那么当我查询的时候，本地域名服务器就不必要向根域名服务器重新查询，只需要将高速缓冲中上次的查询结果告诉我。

或者：本地域名服务器中并没有y.abc.com的IP地址，但是存放着顶级域名服务器dns.com的IP地址，那么同样不需要向根域名服务器查询，只需要直接向顶级域名服务器进行查询

高速缓存存在着一定的声明周期，一旦超过了声明周期，就需要向根域名服务器重新请求

> 主要参考书籍 《计算机网络第五版》