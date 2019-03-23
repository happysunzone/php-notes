# HTTP概述

## 目录
- [HTTP简介](#HTTP简介)
- [HTTP响应消息](#HTTP响应消息)

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
    # Accept
    
Accept-Charset
Accept-Encoding
Accept-Language
Host
User-Agent
```
#### HTTP请求体


### HTTP响应消息
```
# 组成
响应行 响应头 响应体
```