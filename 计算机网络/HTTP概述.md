# HTTP概述

## 目录
- [HTTP简介](#HTTP简介)
- [HTTP1.1相比于HTTP1.0的改进](#HTTP1.1相比于HTTP1.0的改进)
- [HTTP2.0相比于HTTP1.1的改进](#HTTP2.0相比于HTTP1.1的改进)
- [HTTP请求消息](#HTTP请求消息)
  - [HTTP请求行](#HTTP请求行)
  - [HTTP请求头](#HTTP请求头)
  - [HTTP请求体](#HTTP请求体)
- [HTTP响应消息](#HTTP响应消息)
  - [HTTP响应行](#HTTP响应行)
  - [HTTP响应头](#HTTP响应头)
  - [HTTP响应体](#HTTP响应体)

### HTTP简介
```
# 介绍
HTTP: Hyper Text Transfer Protocal的缩写, 即超文本传输协议
无状态指的是协议没有记忆能力, 如果后续处理需要前面的信息, 必须重新传输前面的信息, 这样可能导致每次传输的数据量增大

# 特点
HTTP是无状态协议
基于TCP/IP通信协议
请求/响应式
```

### HTTP1.1相比于HTTP1.0的改进
```
# HTTP1.0
client的每次HTTP请求都需要和server建立一个TCP连接, server响应完成断开TCP连接, 这样无法复用TCP连接

# HTTP1.1
1. HTTP1.1默认支持持久连接(增加了请求头Connection: keep-alive), 即一个TCP连接上可以传送多个HTTP请求和响应, 从而复用TCP连接
2. 增加管道机制(pipelining)
一个网页中含有多张图片, 这多张图片可以基于一个TCP连接
HTTP1.1允许client可以同时发送多个请求, 无须等待上次请求的返回结果
但server必须按照client的请求顺序依次返回响应结果
3. 增加Host请求头
HTTP1.0认为每台服务器绑定唯一的IP地址, 所以请求头中没有传递主机名
但是现在一台服务器上可以存在多个虚拟主机, 并且它们共享同一个IP地址
HTTP1.1增加Host请求头, server根据Host请求头区分客户端所要访问的虚拟WEB站点
如果请求中没有Host请求头, 会报 400 Bad Request 错误

# 比如:
www.baidu.com 的Host是 www.baidu.com
xueshu.baidu.com 的Host是 xueshu.baidu.com
```

### HTTP2.0相比于HTTP1.1的改进
采用二进制格式而非文本格式
```
HTTP1.1请求头是文本, 请求体可以是文本或二进制.
HTTP2无论是请求头还是请求体都是二进制.
基于文本格式的协议解析存在天然缺陷, 文本的的表现形式有多样性, 要做到健壮性需要考虑很多场景.
基于二进制格式的协议解析只认0和1的组合, 实现方便且健壮.
```

多路复用
```
在一个TCP连接里, client和server都可以同时发送多个请求和响应, 而且不用按照顺序一一对应.
```
![http-multi](https://raw.githubusercontent.com/duiying/img/master/http-multi.png)   

首部压缩
```
HTTP1.X的请求头带有大量信息, 而且每次都要重新发送.
HTTP2.0使用encoder来减少需要传输的header的大小, client和server各自cache一份header fields表,
既避免了重复header的传输, 又减小了需要传输的大小.
```

服务端推送
```
服务端推送是一种在客户端请求之前发送数据的机制.
当代网页使用了许多资源: HTML、样式表、脚本、图片等等, 在HTTP/1.x中这些资源每一个都必须明确地请求.
这可能是一个很慢的过程, 浏览器从获取HTML开始, 然后在它解析和评估页面的时候, 增量地获取更多的资源.
因为服务器必须等待浏览器做每一个请求, 网络经常是空闲的和未充分使用的.

为了改善延迟, HTTP/2引入了server push, 它允许服务端推送资源给浏览器.
在浏览器明确地请求之前, 一个服务器经常知道一个页面需要很多附加资源, 在它响应浏览器第一个请求的时候, 可以开始推送这些资源.
这允许服务端去完全充分地利用一个可能空闲的网络, 改善页面加载时间.
```
![http-push](https://raw.githubusercontent.com/duiying/img/master/http-push.png)   

### HTTP请求消息
```
# 组成
请求行 请求头 请求体
```
#### HTTP请求行
```
# 实例
GET / HTTP/1.1

# 组成
请求方式 资源路径 HTTP版本

# 格式
请求行中每个部分用空格分隔, 最后以换行结束
```
#### HTTP请求头
```
# 请求头作用
请求头用于向server传递附加信息, 例如client可以接受的 响应内容的类型(Accept)、响应内容的编码方式(Accept-Encoding)等

# 实例
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
Cache-Control: max-age=0
Connection: keep-alive
Cookie: BAIDUID=08CF60B6DF3FB9883C5F5B6467F64BF6:FG=1; BIDUPSID=08CF60B6DF3FB9883C5F5B6467F64BF6; PSTM=1550841784; delPer=0; BD_HOME=0; H_PS_PSSID=2453_1462_21121_28724_28557_28697_28585_28639_26350_28518_28625_22157; BD_UPN=12314753
Host: www.baidu.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.102 Safari/537.36

# 组成
每个请求头由一个头字段名称和一个值组成

# 格式
头字段名称和值之间用冒号和空格分隔, 每个请求头之后使用换行结束
头字段名称不区分大小写, 但习惯上将单词第一个字母大写

# 常用的请求头
Accept: 
客户端可以接受的响应内容类型 
   
Accept-Charset: 
客户端可以接受的字符集

Accept-Encoding: 
客户端能够进行解码的数据编码方式, 这里的编码方式通常指某种压缩方式

Accept-Language: 
客户端可以接受的语言列表

Host: 
指定资源所在的主机名和端口号, 80端口可以省略

If-Modified-Since: 
If-Modified-Since作为一个请求条件, 只有服务器资源的修改时间比If0Modified-Since请求头指定的时间新
服务器才会返回资源, 否则服务器将返回304状态码而不是返回资源

User-Agent: 
用户代理, 用于指定客户端使用的操作系统、浏览器等信息

Referer:
如果直接在浏览器地址栏输入URL, 浏览器不会发送Referer请求头
如果是点击网页上的超链接进入其它网页, 浏览器会使用Referer请求头字段标识发出请求的超链接所在网页的URL
Referer头字段可以用于网站的防盗链
```
#### HTTP请求体


### HTTP响应消息
```
# 组成
响应行 响应头 响应体
```

#### HTTP响应行
```
# 实例
HTTP/1.1 200 OK

# 组成
HTTP版本 状态码 状态码的描述信息

# 格式
响应行中每个部分用空格分隔, 最后以换行结束
```

#### HTTP响应头
```
# 响应头作用
响应头用于向client传递附加信息, 例如服务器软件名(Server)、重定向地址(Location)等

# 实例
HTTP/1.1 304 Not Modified
Cache-Control: max-age=0, must-revalidate
Date: Sat, 23 Mar 2019 08:09:41 GMT
Etag: 75e615640c596c76d8d3d3241c4f0170
Server: apache
Strict-Transport-Security: max-age=172800

# 常用的响应头
Location:
通知客户端获取请求资源的新的URL, 该URL是绝对路径
Location头字段和大多数3XX状态码配合使用, 以便通知客户端自动重新连接到新资源的请求URL

Server:
Server头字段用于指定服务器软件产品的名称
```

#### HTTP响应体

### 参考
- [https://www.doc11.com/p/2834.html](https://www.doc11.com/p/2834.html)