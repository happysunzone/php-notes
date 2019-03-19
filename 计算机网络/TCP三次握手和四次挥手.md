# TCP三次握手和四次挥手
### 基础概念
```
TCP: 面向连接的/可靠的传输层通信协议(Transmission Control Protocal 传输控制协议)
6种TCP标志位(位码): 
    SYN: synchronization, 置1表示建立连接
    ACK: acknowledgement, 置1表示确认确认序号有效
    FIN: finish, 置1表示数据发送完毕, 希望释放连接
    RST: reset, 置1表示出现严重错误, 必须释放连接, 然后再重新建立
    PSH: push, 传送
    URG: urgent, 紧急
Sequence number: 序列号
Acknowledgement number: 确认号
MSL: Maximum Segment Lifetime, 任何报文在网络上存在的最长时间, 超过这个时间报文将被丢弃
```

### 三次握手
![三次握手](https://raw.githubusercontent.com/duiying/img/master/三次握手.png)  
```
# 第一次握手
客户端将SYN位置1, 随机生成int数值sequence number, 将数据包发送给服务端
客户端进入SYN_SENT(同步已发送)状态, 等待服务端确认

# 第二次握手
服务端收到数据包后根据标志位SNY=1得知客户端请求建立连接, 设置acknowledgement number为sequence number加1
服务端随机生成int数值sequence number, 服务端将SYN和ACK位都置1
服务端将数据包发送给客户端, 服务端进入SYN_RCVD(同步已收到)状态, 等待客户端确认

# 第三次握手
客户端收到数据包后需要检查(1)ACK标志位是否为1 (2)acknowledgement number是否正确(即第一次发送的sequence number加1)
客户端检查完成后发送 标志位ACK=1, sequence number=第二次握手的acknowledgement number, acknowledgement number=第二次握手的sequence number+1的数据包给服务端
客户端进入ESTABLISHED状态
服务端收到数据包后需要检查(1)ACK标志位是否为1 (2)acknowledgement number是否正确(即第二次发送的sequence number加1)
服务端检查完成后进入ESTABLISHED状态

完成三次握手后, 客户端和服务端开始进行传送数据
```

### 为什么采用三次握手而不是两次握手
```
为了防止已失效的连接请求延迟到达服务器而产生的问题
场景: 
client发送连接请求报文段1, 连接请求报文段1在网络节点中长时间滞留, client又重新发送连接请求报文段2
server接收到连接请求报文段2, client和server建立连接

但是对于连接请求报文段1, client认为自己没有发送连接请求, client处于CLOSED状态, 接到任何包都会丢弃
此时延迟的请求1到达了server, server认为是client发送了新的请求, 于是向client发送确认报文段

# 两次握手
如果是两次握手, server认为已经建立了连接, 一直等待和client传输数据, 但是client是关闭状态, 不会和server建立连接
这就导致了server资源的浪费

# 三次握手
如果是三次握手, client不会向server的确认发出确认报文段, server收不到确认就知道了client没有要求建立连接
```

### 四次挥手
![四次挥手](https://raw.githubusercontent.com/duiying/img/master/四次挥手.png)
```
# 数据传输完毕后, client和server双方都可以释放连接, 最开始的时候client和server都是处于ESTABLISHED状态

# 第一次挥手
client向server发送释放连接报文段(FIN=1, 序列号seq=u), 进入FIN-WAIT-1(终止等待1)状态, 等待server的确认
client完成第一次挥手后, 就不能再向server发送数据了

# 第二次挥手
server收到client发送的释放连接报文段后即发出确认报文段(ACK=1, 序列号seq=v, 确认号ack=u+1), server进入CLOSE-WAIT(关闭等待)状态
client收到server发送的确认报文段后, 进入FIN-WAIT-2(终止等待2)状态, 等待server发送连接释放报文段
(在收到释放报文段之前还需要接受server发送的最后的数据)

# 第三次挥手
server将最后的数据发送完毕之后, 向client发送连接释放报文段(FIN=1, ACK=1, 序列号seq=w, 确认号ack=u+1)
server进入LAST-ACK(最后确认)状态，等待client的确认

# 第四次挥手
client收到server发送的连接释放报文段后, 向server发出确认报文段(ACK=1, seq=u+1, ack=w+1)
client进入TIME-WAIT(时间等待)状态; 此时TCP未释放掉, 需要经过时间等待计时器设置的时间2MSL后, A才进入CLOSED状态
server只要收到client的确认报文段, 立即进入CLOSED状态, 可以看到, server结束TCP连接的时间要早于client
```
