## HTTP缓存

保存资源副本并在下次请求时直接使用该副本，当web缓存发现请求的资源已经被存储，它会拦截请求，返回该资源的拷贝，而不会去源服务器重新下载，从而达到缩短网页请求资源的距离，减少延迟，节省网络流量，并且由于缓存文件可以重复利用，降低网络负荷，提高客户端响应。

### 缓存策略

用户访问行为：

- url直接访问
- F5刷新页面
- Ctrl-F5强制刷新（不使用HTTP缓存）

不同的访问手段，会导致浏览器使用不同的缓存策略。

HTTP缓存主要是通过请求和响应报文头中的对应Header信息，来控制缓存的策略。相关字段为`Expires、Cache-Control、Last-Modified、Etag`

### 强制缓存

发起请求后会到缓存中查询是否命中，如果命中则直接返回（即使此时缓存数据已经和服务器数据不一致），如果未命中则到服务器查询返回结果，并记录缓存。

```shell
Expires（HTTP1.0）:
	该值为服务器返回的到期时间。存在问题：服务器和客户端可能时间不一致。
Cache-Control：
	常见取值：private、public、no-cache、max-age，no-store，默认为private
	1.max-age： 资源可以被缓存多长时间
	2.s-maxage：提供给代理服务器使用，和max-age含义一致
	3.public：  可以被任何缓存区缓存
	4.private： 针对个人用户缓存
	5.no-cache：由服务器决定是否可以用缓存，304则表示缓存可用
	6.no-store：禁止一切缓存
```

### 对比缓存

拿着缓存标识到服务器查询缓存是否可用，可用则返回304，否则返回新数据并更新缓存。

```shell
Last-Modified / If-Modified-Since
	Last-Modified：服务器返回资源的最后修改时间
	If-Modified-Since：浏览器拿着最后修改时间请求服务器，如果判断为资源未被修改则返回304
Etag / If-None-Match（优先级更高）
	Etag：服务器返回资源的唯一标识
	If-None-Match：浏览器拿着资源唯一标识请求服务器，如果判断为资源未被修改则返回304
```



## Cookie/Session

Cookie 和 Session 是为了在无状态的HTTP协议之上维护会话状态，使得服务器可以知道当前是和哪个客户在打交道。

### Cookie 实现机制

```shell
Cookie是由客户端保存的小型文本文件，其内容为一系列的键值对，Cookie是由HTTP服务器设置的，保存在浏览器中。
服务器通过`Set-Cookie`字段返回Cookie值，每一项都可以设置过期时间。
```

### Cookie 防篡改机制

```shell
Cookie是明文传输，容易被恶意篡改发送到服务器。
解决方案：
	在服务端为Cookie生成签名，由于除了服务端没有人知道怎样生成签名，一旦Cookie内容被修改，服务端可以通过校验得知。
存在问题：
	假设`Cookie:authed=true|6hTiBl7lVpd1P`，用户想篡改`authed=false`，由于不能计算签名所以无法得逞。
	但是如果Cookie中出现过`authed=false`的情况，用户就可以将签名记录下来，从而实现篡改。
	因此Cookie中一般不会存储敏感数据，而是只存储SessionId，真正内容存储在服务器。
```

### Session 实现机制

```shell
1.用户提交密码进行登陆。
2.服务器验证用户名、密码。
3.验证成功后将用户信息存储起来，并生成SessionId。
4.设置`Cookie为sessionId=xxxxxx|checksum`并返回HTTP响应，仍然为每一项Cookie都设置签名。
5.此时用户看不到任何敏感信息
```



## 跨域问题

## HTTP2.0/3.0

## HTTPS

## 队头阻塞

## HTTP代理