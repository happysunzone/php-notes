# inode

### 什么是inode
```
inode: 索引节点(index node)

理解inode, 要从文件存储说起
文件存储在硬盘上, 硬盘的最小存储单位叫做扇区(Sector), 每个扇区存储512字节(0.5KB)
操作系统读取硬盘的时候, 不会一个一个扇区读取, 这样效率太低, 而是一次性读取多个扇区, 即一次性读取一个块(block)
这种由多个扇区组成的块, 是文件存取的最小单位
块的大小, 最常见的是4KB, 即连续8个sector组成一个block

文件数据都储存在块中, 我们还需要一个地方储存文件的元信息(文件的创建者/创建日期/大小等)
这种储存文件元信息的区域叫做inode, 即索引节点
```

### inode的内容
```
文件的字节数
文件拥有者的User ID
文件的Group ID
文件的读、写、执行权限
文件的时间戳, 共有三个: ctime指inode上一次变动的时间; mtime指文件内容上一次变动的时间; atime指文件上一次打开的时间
链接数, 即有多少文件名指向这个inode
文件数据block的位置
```

### inode的大小
```
inode也会消耗硬盘空间, 所以硬盘格式化的时候, 操作系统自动将硬盘分为两个区域: 
数据区, 存放文件数据 和 inode区(inode table), 存放inode所包含的信息

每个inode节点的大小, 一般是128字节或256字节
inode节点的总数, 在格式化的时候就已给定, 一般是每1KB或每2KB就设置一个inode
假设: 1GB的硬盘, 每个inode节点大小为128字节, 每1KB就设置一个inode, 那么inode table大小为 128/1024 * 1G = 128M
```

```
# 查看每个硬盘分区的inode总数和和已经使用的数量, 可以使用 df -i 命令
[root@localhost ~]# df -i
Filesystem               Inodes IUsed   IFree IUse% Mounted on
/dev/mapper/centos-root 8910848 88471 8822377    1% /
devtmpfs                 479892   389  479503    1% /dev
tmpfs                    482946     1  482945    1% /dev/shm
tmpfs                    482946  1449  481497    1% /run
tmpfs                    482946    16  482930    1% /sys/fs/cgroup
/dev/sda1                524288   327  523961    1% /boot
tmpfs                    482946     1  482945    1% /run/user/0
```

```
由于每个文件都必须要有一个inode, 因此有可能出现inode已经用光, 但硬盘还未存满的情况
这时, 就无法在硬盘上创建新文件
```

### inode id
```
每个inode都有一个号码, 操作系统通过inode id来识别文件

表面上, 用户通过文件名, 打开文件
实际上, 打开文件分为3步:
1. 找到文件名对应的inode id
2. 通过inode id, 获取inode信息
3. 通过inode信息, 找到文件数据对应的块(block), 读取内容
```

```
# 通过 ls -i 命令, 可以看到文件名对应的inode id
[root@localhost wyx]# ls -i 1.txt
34518626 1.txt
```

### 目录文件
```
Unix/Linux系统中, 目录也是一种文件
目录的读和写权限, 是针对目录本身
每个目录项, 由两部分组成: 文件名和文件名对应的inode id
```

### 硬链接
```
inode信息中有一项叫做链接数, 当一个文件增加一个硬链接时, 链接数+1, 否则-1
当链接数减为0时, 表示没有文件名指向这个inode, 系统就会回收这个inode以及对应的block区域
```


### 参考
[理解inode](http://www.ruanyifeng.com/blog/2011/12/inode.html)