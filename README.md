<h1 align="center"> php-notes </h1>
<p align="center"> 
Linux、计算机网络、数据结构、算法、PHP、MySQL、设计模式、Redis、MQ、安全、思维题、报错总结......
</p>

### 目录

- Linux
  - [inode](Linux/inode.md)
  - [软链接和硬链接](Linux/软链接和硬链接.md)  
  - [LinuxIO模型](Linux/LinuxIO模型.md)
  - [select、poll、epoll](Linux/select、poll、epoll.md)
  - [并发和并行的区别](Linux/并发和并行的区别.md)
  - [进程和线程的区别](Linux/进程和线程的区别.md)
  - [协程](Linux/协程.md)
  - [孤儿进程和僵尸进程](Linux/孤儿进程和僵尸进程.md)
  - [Linux基本命令](Linux/Linux基本命令.md)
  - [文本处理工具sed](Linux/文本处理工具sed.md)
  - [文件统计wc](Linux/文件统计wc.md)
  - [定时任务crontab](Linux/定时任务crontab.md)
  - [文本编辑器Vim](Linux/文本编辑器Vim.md)
  - [如何平滑重启PHP-FPM](Linux/如何平滑重启PHP-FPM.md)
  - [Linux下如何查看端口](Linux/Linux下如何查看端口.md)
  - [CentOS7搭建samba实现与Win共享目录](Linux/CentOS7搭建samba实现与Win共享目录.md)
  - [取出Nginx日志里访问量前N的IP地址](Linux/取出Nginx日志里访问量前N的IP地址.md)

- 计算机网络
  - [计算机网络体系结构](计算机网络/计算机网络体系结构.md)
  - [TCP三次握手和四次挥手](计算机网络/TCP三次握手和四次挥手.md)
  - [TCP和UDP的区别](计算机网络/TCP和UDP的区别.md)
  - [HTTP概述](计算机网络/HTTP概述.md)
  - [HTTP和HTTPS的区别](计算机网络/HTTP和HTTPS的区别.md)
  - [一次完整的HTTP请求过程](计算机网络/一次完整的HTTP请求过程.md)
  - [GET和POST请求方式的区别](计算机网络/GET和POST请求方式的区别.md)
  - [常见的HTTP状态码](计算机网络/常见的HTTP状态码.md)
  - [会话技术(Cookie和Session)](计算机网络/会话技术(Cookie和Session).md)
  
- 数据结构

- 算法
  - [认识时间复杂度](算法/认识时间复杂度.md)
  - [对数器](算法/对数器.md)
  - [递归行为的实质和递归行为时间复杂度的计算](算法/递归行为的实质和递归行为时间复杂度的计算.md)
  - 排序
    - [冒泡排序](算法/冒泡排序.md)
    - [选择排序](算法/选择排序.md)
    - [插入排序](算法/插入排序.md)
    - [归并排序](算法/归并排序.md)
    
  - [小和问题](算法/小和问题.md)
  - [荷兰国旗问题](算法/荷兰国旗问题.md)

- PHP
  - [PHP数据类型](PHP/PHP数据类型.md)
  - [CGI、FastCGI、PHP-FPM、PHP-CGI](PHP/CGI、FastCGI、PHP-FPM、PHP-CGI.md)
  - [Trait](PHP/Trait.md)
  - [yield](PHP/yield.md)
  - PHP多进程
    - [PHP多进程初探-创建子进程](PHP/PHP多进程初探-创建子进程.md)
    - [PHP进程间通信](PHP/PHP进程间通信.md)
      - [消息队列](PHP/消息队列.md)
      - [信号量和共享内存](PHP/信号量和共享内存.md)
      - [管道](PHP/管道.md)
  - PHP内核
    - [PHP5中的zval](PHP/PHP5中的zval.md)
    - [PHP7中的zval](PHP/PHP7中的zval.md)
    - [PHP7中数组的实现原理](PHP/PHP7中数组的实现原理.md)
    - [PHP弱类型是如何实现的](PHP/PHP弱类型是如何实现的.md)
    - [PHP是如何实现二进制安全的](PHP/PHP是如何实现二进制安全的.md)
    - [PHP7中的zend_reference](PHP/PHP7中的zend_reference.md)
  - PHP垃圾回收
    - [PHP5引用计数基本知识](PHP/PHP5引用计数基本知识.md)
    - [PHP5.3中的垃圾回收机制](PHP/PHP5.3中的垃圾回收机制.md)
    - [PHP7中的垃圾回收机制](PHP/PHP7中的垃圾回收机制.md)
  - 编程题
    - [给出多种方法反转字符串](PHP/给出多种方法反转字符串.md)
    - [写一个函数, 能够遍历一个文件夹下的所有文件和子文件夹](PHP/写一个函数,%20能够遍历一个文件夹下的所有文件和子文件夹.md)
    - [写一个函数将字符串'make_by_id'装换成'MakeById'](PHP/写一个函数将字符串'make_by_id'装换成'MakeById'.md)
    - [写一个函数, 统计一个字符串中另一个字符串出现的次数](PHP/写一个函数,%20统计一个字符串中另一个字符串出现的次数.md)
    - [求n以内的质数](PHP/求n以内的质数.md)
    - [求两个有序数组的公共元素](PHP/求两个有序数组的公共元素.md)

- MySQL
  - [mysqldump命令](MySQL/mysqldump命令.md)
  - [数据库三范式](MySQL/数据库三范式.md)
  - [MySQL索引](MySQL/MySQL索引.md)
  - [表的约束](MySQL/表的约束.md)
  - [MyISAM和InnoDB的区别](MySQL/MyISAM和InnoDB的区别.md)
  - [char和varchar数据类型的区别](MySQL/char和varchar数据类型的区别.md)
  - [TINYINT(M)中M表示的含义是什么](MySQL/TINYINT(M)中M表示的含义是什么.md)
  - [浮点型和定点型](MySQL/浮点型和定点型.md)
  - [手写建表语句和sql](MySQL/手写建表语句和sql.md)
  - [MySQL事务的4个特性](MySQL/MySQL事务的4个特性.md)
  - [事务的隔离级别](MySQL/事务的隔离级别.md)

- 设计模式
  - [设计模式简介](设计模式/设计模式简介.md)
  - [单例模式](设计模式/单例模式.md)
  - [工厂模式](设计模式/工厂模式.md)
  - [代理模式](设计模式/代理模式.md)
  - [适配器模式](设计模式/适配器模式.md)

- Redis

- MQ

- 安全

- 思维题
  - [小鼠喝牛奶](思维题/小鼠喝牛奶.md)
  - [判断4个坐标点能否组成一个矩形](思维题/判断4个坐标点能否组成一个矩形.md)
  - [判断扑克牌顺子](思维题/判断扑克牌顺子.md)
  
- 报错总结
  - [HTTP推送服务400错误](报错总结/HTTP推送服务400错误.md)