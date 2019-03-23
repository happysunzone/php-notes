# 如何平滑重启PHP-FPM

```
# 首先了解下面几个命令
INT, TERM: 立刻终止
QUIT: 平滑终止
USR1: 重新打开日志文件
USR2: 平滑重载所有worker进程并重新载入配置和二进制模块
```

```
# 启动PHP-FPM
/usr/sbin/php-fpm

# 关闭PHP-FPM
kill -INT `cat /run/php-fpm/php-fpm.pid`

# 平滑重启PHP-FPM
kill -USR2 `cat /run/php-fpm/php-fpm.pid`
```