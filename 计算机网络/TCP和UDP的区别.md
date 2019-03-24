# TCP和UDP的区别

## 目录
- [TCP与UDP的介绍](#TCP与UDP的介绍)
- [TCP和UDP的对比](#TCP和UDP的对比)
- [是否面向连接](#是否面向连接)
- [是否支持广播和多播](#是否支持广播和多播)
- [对应用层报文的处理](#对应用层报文的处理)
- [是否提供可靠传输服务](#是否提供可靠传输服务)
- [首部开销](#首部开销)
- [参考](#参考)

### TCP与UDP的介绍
```
TCP和UDP是 传输层 的两个重要协议
TCP: Transmission Control Protocal, 传输控制协议
UDP: User Datagram Protocal, 用户数据报协议
```

### TCP和UDP的对比
```
1. 
TCP: 面向连接
UDP: 无连接

2.
TCP: 每一条TCP连接只能有两个端点, 只能是一对一通信
UDP: 支持一对一、一对多、多对一、多对多通信

3. 
TCP: 面向字节流
UDP: 面向报文, 对应用层交付的报文直接打包

4.
TCP: 可靠传输, 使用流量控制和拥塞控制
UDP: 尽最大努力交付, 即不可靠传输, 不使用流量控制和拥塞控制

5.
TCP: 首部最小20字节, 最大60字节
UDP: 首部开销小, 仅8个字节
```

### 是否面向连接
```
TCP面向连接, 在传输数据之前必须建立连接(三次握手), 传输完数据之后释放连接(四次挥手)
UDP无连接, 在传输数据之前不需要建立连接
```

### 是否支持广播和多播
```
TCP只支持单播, 不支持广播和多播, 即只支持一对一通信
UDP支持单播、广播和多播, 即支持一对一、一对多、多对一、多对多通信
```
![tcp-udp](https://raw.githubusercontent.com/duiying/img/master/tcp-udp.png)

### 对应用层报文的处理
```
TCP面向字节流传输, 将应用层交付的数据看成无结构的字节流, 每个TCP套接口有一个发送缓冲区和接收缓冲区
当字节流太长时, TCP会将字节流拆分再构成报文进行发送
当字节流太短时, TCP会等待缓冲区的字节流达到一定长度再构成报文进行发送

UDP面向报文传输, 对于应用层交付的报文, 不合并也不拆分, 只是在报文上加上首部就交给下面的网际层
也就是说, 无论应用层交给UDP多长的报文, UDP统统发送, 一次发送一个报文, 发送多少次接收方就接收多少次
接收方接收到报文以后, 去除首部交给上层的应用层
因此, UDP传输需要应用层控制报文的大小 
``` 
![tcp-udp-data](https://raw.githubusercontent.com/duiying/img/master/tcp-udp-data.png)

### 是否提供可靠传输服务
```
TCP可靠传输的原理
每个Tcp socket在内核中都有一个发送缓冲区和一个接收缓冲区
TCP协议要求对端在接受到tcp数据报之后, 要对其序号进行ACK
只有当接受到一个tcp数据报的ACK之后, 才可以把这个TCP数据报从socket的发送缓冲区清除

UDP不可靠传输的原理
UDP只有一个socket接收缓冲区, 没有socket发送缓冲区, 即只要有数据就发, 不管对方是否可以正确接收
而在对方的socket接收缓冲区满了之后, 新来的数据报无法进入到socket接收缓冲区, 此数据报就会被丢弃
```
![tcp-udp-kekao](https://raw.githubusercontent.com/duiying/img/master/tcp-udp-kekao.png)

### 首部开销
![首部](https://raw.githubusercontent.com/duiying/img/master/首部.png)

### 参考
- [面向报文（UDP）和面向字节流（TCP）的区别](https://blog.csdn.net/liuyanfeier/article/details/52787037) 
- [UDP和TCP的对比](https://www.bilibili.com/video/av40163489)
- [Tcp可靠Udp不可靠原理](https://www.cnblogs.com/ZhenhaoYu/p/9346335.html) 
