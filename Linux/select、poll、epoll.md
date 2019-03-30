# select、poll、epoll

select、poll、epoll都是多路复用的机制, 用来监听多个描述符, 如果其中任意一个描述符处于就绪的状态, 就会返回消息给对应的进程通知其采取下一步的操作

### 概念解释
```
fd_set: 
实际是一个long类型的数组, 数组的每个元素都能与一个打开的文件句柄(socket、文件、管道、设备等)建立联系
当调用了select()时, 由内核根据IO状态修改fd_set的内容, 由此来通知执行了select()的进程哪个文件句柄可读
```

### select
```
# select的执行过程
1. 使用copy_from_user将fd_set从用户空间拷贝到内核空间

2. select循环遍历fd_set内所有文件描述符对应的驱动程序的poll函数, poll函数将当前进程current挂到设备的等待队列中,
然后返回一个描述读写操作是否就绪的bitmask, 根据这个mask掩码给fd_set赋值, 当select遍历完fd_set内所有指定文件描述符的poll函数后,
如果还没有返回一个可读写的mask掩码, select会让current睡眠, 当设备驱动资源可读写后, 会唤醒该进程,
如果超过一定的时间(timeout)调用select的进程还没有被唤醒, 则该进程会重新被唤醒获得CPU, 进而重新遍历fd_set

3. 将fd_set从内核空间拷贝到用户空间

# select的缺点
1. 每次调用select, 需要把fd_set从用户空间拷贝到内核空间, 造成一定的开销
2. 不管文件句柄是否活跃, 每次select都会轮询fd_set, 效率很低
3. 单个进程所打开的fd数量有限制, 由FD_SETSIZE设置, 默认值是1024 
```

### poll
```
poll和select没有本质的区别, 但是poll打开的fd数量没有限制, 因为poll使用链表保存fd
```

### epoll
```
epoll提供了三个函数: epoll_create, epoll_ctl和epoll_wait

epoll_create: 创建一个epoll句柄, 建红黑树存储epoll_ctl传来的socket, 建立用来存储就绪事件的链表

epoll_ctl: 
1. 注册要监听的事件类型, 将刚建立的socket加入epoll让其监控或者把epoll正在监控的某个socket句柄移出epoll, 不再让epoll监控它
2. 让current睡眠, 
3. 除了将socket加入epoll文件系统对应的红黑树, 还会给内核中断处理程序注册一个回调函数
4. 该回调函数告诉内核, 如果这个socket句柄的中断到了, 就把该socket放在存储就绪事件的链表, 同时唤醒等待队列上的current进程

           
epoll_wait: 等待事件的发生, 轮询就绪链表, 将就绪的socket拷贝到用户空间
```

### 水平触发和边缘触发
```
水平触发: LT, Level Triggered
边缘触发: ET, Edge Triggered

select和poll仅支持LT, epoll支持LT和ET

LT:
当被监控的文件描述符上有可读写事件发生时,epoll_wait()会通知处理程序去读写
如果第一次没有把数据一次性全部读写完(如读写缓冲区太小), 那么下次调用 epoll_wait()时，
它还会通知你在上没读写完的文件描述符上继续读写, 当然如果你一直不去读写, 它会一直通知你
ET:
当被监控的文件描述符上有可读写事件发生时, epoll_wait()会通知处理程序去读写
如果这次没有把数据全部读写完(如读写缓冲区太小), 那么下次调用epoll_wait()时，
它不会通知你, 也就是它只会通知你一次, 直到该文件描述符上出现第二次可读写事件才会通知你
```

### 比较
```
1. 针对select和poll的第一个缺点, 即每次调用select, 需要把fd_set从用户空间拷贝到内核空间
epoll在内核空间建立了红黑树来存储进程需要的句柄, 每次调用epoll_ctl只是往内核空间的红黑树加入新的socket

2. 针对select和poll的第二个缺点, 即管文件句柄是否活跃, 每次select都会轮询fd_set
epoll为每个fd指定了回调函数, 当设备就绪调用回调函数, 将该设备加入就绪链表, 
即使监控百万的句柄, 不需要轮询百万次, 只需要通过回调将就绪状态的句柄加入就绪链表, epoll_wait会一直轮询该就绪链表
所以epoll的IO效率不会像select和poll那样随着fd数量的增加IO效率呈线性下降

3. poll和epoll对于单个进程打开的fd数量没有限制
```

### 参考
- [https://blog.csdn.net/zymill/article/details/79998593](https://blog.csdn.net/zymill/article/details/79998593)
- [https://blog.51cto.com/9291927/1825225](https://blog.51cto.com/9291927/1825225)
- [https://blog.csdn.net/hdutigerkin/article/details/7517390](https://blog.csdn.net/hdutigerkin/article/details/7517390)
- [https://my.oschina.net/xianggao/blog/663655](https://my.oschina.net/xianggao/blog/663655)