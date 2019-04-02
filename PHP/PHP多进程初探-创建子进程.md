# PHP多进程初探-创建子进程

## 目录
- [准备知识](#准备知识)
- [创建子进程](#创建子进程)
- [理解pcntl_wait()](#理解pcntl_wait())

### 准备知识
```
# pcntl_fork()函数
系统从当前进程生成一个新的进程, 原来的进程叫做父进程, 新生成的进程叫做子进程.
子进程获得父进程数据空间、堆和栈的复制.
pcntl_fork()执行一次, 返回两次, 一次返回到新进程(0), 一次返回到父进程(子进程的pid).
子进程从pcntl_fork()返回后的位置开始执行, 但是拥有父进程前面定义的变量名和变量值.
pcntl_fork()执行失败返回-1.
一般来说, 在pcntl_fork()之后是父进程先执行还是子进程先执行是不确定的, 这取决于内核所使用的调度算法.

# posix_getpid()
返回当前进程ID

# posix_getppid()
返回父进程ID

# pcntl_wait()
阻塞当前的进程直到子进程退出, 父进程回收子进程的资源, 防止产生僵尸进程
```

### 创建子进程
代码:
```php
<?php

echo '父进程ID: ' . posix_getpid() . PHP_EOL;

$pid = pcntl_fork();

switch ($pid) {
    case -1:
        die('fork failed');
        break;
    case 0:
        echo '子进程ID: ' . posix_getpid() . PHP_EOL;
        sleep(2);
        echo '子进程已经退出';
        break;
    default:
        pcntl_wait($status);
        echo '父进程已经退出' . PHP_EOL;
        break;
}
```
运行结果:
```
[root@e2963c647c8b www]# php threads.php
父进程ID: 573
子进程ID: 574
子进程已经退出父进程已经退出
```

### 理解pcntl_wait()
```
pcntl_wait()在子进程退出之前会阻塞, 直到一个子进程退出后再创建另外一个子进程
```
代码:
```php
<?php

echo '父进程ID: ' . posix_getpid() . PHP_EOL;

for ($i = 0; $i < 2; $i++) {
    $pid = pcntl_fork();

    switch ($pid) {
        case -1:
            die('fork failed');
            break;
        case 0:
            echo '子进程ID: ' . posix_getpid() . '父进程ID: ' . posix_getppid() . PHP_EOL;
            break;
        default:
            pcntl_wait($status);
            echo '父进程ID: ' . posix_getpid() . PHP_EOL;
            break;
    }
}
```
运行结果:
```
[root@e2963c647c8b www]# php pcntl_wait.php
父进程ID: 593
子进程ID: 594父进程ID: 593
子进程ID: 595父进程ID: 594
父进程ID: 594
父进程ID: 593
子进程ID: 596父进程ID: 593
父进程ID: 593
```
分析:
```
1. 进程593fork()出一个进程594, 进程593阻塞, 进程594输出: 子进程ID: 594父进程ID: 593
2. 进程594 i++=1, 进程594fork()出一个进程595, 进程594阻塞, 进程595输出: 子进程ID: 595父进程ID: 594
3. 进程595 i++=2(拷贝父进程594的变量i=1), 循环终止, 进程退出, 回到进程594, 进程594解除阻塞, 输出: 父进程ID: 594
4. 进程594 i++=2, 循环终止, 进程退出, 回到进程593, 进程593解除阻塞, 输出: 父进程ID: 593
5. 进程593 i++=1, fork()出一个进程596, 进程593阻塞, 进程596输出: 子进程ID: 596父进程ID: 593
6. 进程596 i++=2(拷贝父进程593的变量i=1), 循环终止, 进程退出, 回到进程593, 进程593解除阻塞, 输出: 父进程ID: 593
```