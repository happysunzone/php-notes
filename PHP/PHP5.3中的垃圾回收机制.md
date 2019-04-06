# PHP5.3中的垃圾回收机制

知识准备  
- [PHP5引用计数基本知识](PHP5引用计数基本知识.md)  

## 目录
- [PHP5.3的同步周期回收算法](#PHP5.3的同步周期回收算法)
- [性能方面的因素](#性能方面的因素)
  - [比较PHP5.2和PHP5.3环境下的内存占用情况](#比较PHP5.2和PHP5.3环境下的内存占用情况)
  - [开启垃圾回收机制会使脚本执行时间加长](#开启垃圾回收机制会使脚本执行时间加长)
- [参考](#参考)

5.3以前的PHP用到的引用计数内存机制, 无法处理循环的引用内存泄露.  
PHP5.3的垃圾回收算法仍然以引用计数为基础, 但PHP5.3使用了一种同步周期回收算法来解决循环的引用内存泄露问题.  

### PHP5.3的同步周期回收算法

首先, 建立基本规则:  
- 如果一个引用计数增加, 它将继续被使用, 当然就不再在垃圾中.
- 如果引用计数减少到零, 所在变量容器将被清除(free).
- 仅仅在引用计数减少到非零值时, 才会产生垃圾周期(garbage cycle).
- 在一个垃圾周期中, 通过检查引用计数是否减1, 并且检查哪些变量容器的引用次数是零, 来发现哪部分是垃圾.

![php53-gc](https://raw.githubusercontent.com/duiying/img/master/php53-gc.png)  

PHP5.3在引用计数基础上提出下面概念:  
```
可能根(possible root): 
当一个zval容器引用计数减少并且refcount不为0, 该zval容器就是可能根.
在gc_zval_check_possible_root()函数中, 仅对 数组 和 对象 执行可能根操作.

根缓冲区(root buffer):
根缓冲区用于存放所有可能根, 它是固定大小的, 默认可存10000个可能根.
如需修改可以通过修改PHP源码文件Zend/zend_gc.c中的常量GC_ROOT_BUFFER_MAX_ENTRIES, 再重新编译.

回收周期:
当缓冲区满时, 才对缓冲区中的所有可能根进行垃圾回收处理.
```

同步周期回收算法的过程:
```
1. 根缓冲区存放所有可能根.
2. 根缓冲区满时, 按照深度优先遍历算法遍历所有能遍历到的zval, 并将每个zval的refcount减1.
   同时为了避免对同一zval多次减1(因为可能不同的根能遍历到同一个zval), 每次对某个zval减1后就对其标记为'已减'.
3. 如果某个zval的refcount不为0, 则对其加1, 否则保持其为0.
4. 清空根缓冲区中的所有根(注意是把这些zval从缓冲区中清除而不是销毁它们), 然后销毁所有refcount为0的zval, 并收回其内存.
```

PHP5.3的垃圾回收算法有以下特性:
```
1. 不是每次refcount减少时都进入回收周期, 只有根缓冲区满后在开始垃圾回收.
2. 可以解决循环引用问题.
3. 可以总将内存泄露保持在一个阈值以下.
4. 仅对 数组 和 对象 执行执行可能根操作
```

### 性能方面的因素
#### 比较PHP5.2和PHP5.3环境下的内存占用情况
```php
class Foo
{
    public $var = '3.1415962654';
}

$baseMemory = memory_get_usage();

for ( $i = 0; $i <= 100000; $i++ )
{
    $a = new Foo;
    $a->self = $a;
    if ( $i % 500 === 0 )
    {
        echo sprintf( '%8d: ', $i ), memory_get_usage() - $baseMemory, "\n";
    }
}
```
![gc-memory](https://raw.githubusercontent.com/duiying/img/master/gc-memory.png)  

分析:
```
创建一个对象, 对象的属性指向对象本身, 发生典型的内存泄露问题.
上述代码产生两个变量容器(对象容器和属性容器), 但是仅产生一个可能根.

从图可以看出:
对于PHP5.3, 在一万次循环后(产生10000个可能根), 此时根缓冲区满了, 执行垃圾回收机制, 释放那些关联可能根的内存.
对于PHP5.2, 内存一直增加.
```
#### 开启垃圾回收机制会使脚本执行时间加长

```php
class Foo
{
    public $var = '3.1415962654';
}

for ( $i = 0; $i <= 1000000; $i++ )
{
    $a = new Foo;
    $a->self = $a;
}

echo memory_get_peak_usage(), "\n";
```
运行这个脚本两次, 一次是通过配置zend.enable_gc打开垃圾回收机制时，另一次是它关闭时
```
time php -dzend.enable_gc=0 -dmemory_limit=-1 -n example2.php
# and
time php -dzend.enable_gc=1 -dmemory_limit=-1 -n example2.php
```
分析:
```
在我的机器上, 第一个命令持续执行时间大概为10.7秒, 而第二个命令耗费11.4秒, 时间上增加了7%.
然而, 执行这个脚本时内存占用的峰值降低了98%, 从931Mb降到10Mb.
这个基准不是很科学, 或者并不能代表真实应用程序的数据, 但是它的确显示了垃圾回收机制在内存占用方面的好处.
好消息就是, 对这个脚本而言, 在执行中出现更多的循环引用变量时, 内存节省的更多的情况下, 每次时间增加的百分比都是7%.
通常, PHP中的垃圾回收机制, 仅仅在循环回收算法确实运行时会有时间消耗上的增加, 但是在平常的(更小的)脚本中根本就没有性能影响. 
```

### 参考
- [https://www.php.net/manual/zh/features.gc.php](https://www.php.net/manual/zh/features.gc.php)
- [http://www.phppan.com/2010/11/php-source-code-34-arbage-collection/#comments](http://www.phppan.com/2010/11/php-source-code-34-arbage-collection/#comments)
- [http://www.cnblogs.com/leoo2sk/archive/2011/02/27/php-gc.html](http://www.cnblogs.com/leoo2sk/archive/2011/02/27/php-gc.html)