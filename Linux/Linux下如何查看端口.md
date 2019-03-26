# Linux下如何查看端口
Linux下如何查看端口?

## 目录
- [lsof](#lsof)
- [netstat](#netstat)

### lsof
```
# lsof -i命令用于查看端口占用情况

# 格式
lsof -i:port

# 安装lsof命令
[root@localhost ~]# yum -y install lsof

# Example
[root@localhost ~]# lsof -i:3306
COMMAND    PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
docker-pr 1768 root    4u  IPv6  23462      0t0  TCP *:mysql (LISTEN)
```

### netstat
```
# Example
[root@localhost ~]# netstat -anp | grep 3306
tcp6       0      0 :::3306                 :::*                    LISTEN      1768/docker-proxy-c

# 参数说明
-a: 显示所有选项, 默认不显示LISTEN相关
-n: 拒绝显示别名, 能显示数字的全部转化成数字
-p: 显示进程标识符和程序名称
-t: 仅显示tcp相关选项
-u: 仅显示udp相关选项
-l: 仅列出有在Listen(监听)的服务状态

# 注意
LISTEN和LISTENING的状态只有用-a或者-l参数才能看到
```