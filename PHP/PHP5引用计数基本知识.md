# PHP5引用计数基本知识

## 目录
- [创建一个变量时,会生成一个新的zval容器](#创建一个变量时,会生成一个新的zval容器)
- [变量赋值给另外一个变量时,会增加zval的refcount](#变量赋值给另外一个变量时,会增加zval的refcount)
- [unset()时,会减少zval的refcount](#unset()时,会减少zval的refcount)
- [复合类型会为每个item项生成一个zval容器](#复合类型会为每个item项生成一个zval容器)
- [添加一个元素到数组中,该元素指向数组其它元素指向的值](#添加一个元素到数组中,该元素指向数组其它元素指向的值)
- [从数组中删除元素](#从数组中删除元素)
- [数组元素指向数组本身](#数组元素指向数组本身)
- [清除zval容器的问题](#清除zval容器的问题)
- [参考](#参考)

```
每个PHP变量都被保存在zval中, zval中包含变量的值、类型、是否被引用、指向该zval容器的变量(即符号也称symbol)个数.
所有的符号存在一个符号表中, 其中每个符号都有作用于(scope).
```

### 创建一个变量时,会生成一个新的zval容器
```php
$a = 'new string';
xdebug_debug_zval('a');
```
运行结果:
```
a: (refcount=1, is_ref=0)='new string'
```
分析:
```
当创建$a变量时, 会在当前作用域的符号表插入新的符号(a)和创建一个zval容器.
由于该变量是普通变量而不是引用变量, 所以is_ref__gc为0.
由于只有1个变量指向该zval容器, 所以refcount__gc为1.
zval容器结构如下:
zval.value='new string';
zval.is_ref__gc=0;
zval.type="string";
zval.refcount__gc=1;
```

### 变量赋值给另外一个变量时,会增加zval的refcount
```php
$a = 'new string';
$b = $a;
xdebug_debug_zval('a');
```
运行结果:
```
a: (refcount=2, is_ref=0)='new string'
```
分析:
```
当没有必要时, PHP不会复制已经生成的zval容器, 上面的代码中$a和$b指向了同一个zval容器.
由于该变量是普通变量而不是引用变量, 所以is_ref__gc为0.
由于2个变量指向该zval容器, 所以refcount__gc为2.
```

### unset()时,会减少zval的refcount
```php
$a = 'new string';
$c = $b = $a;
xdebug_debug_zval('a');
unset($b, $c);
xdebug_debug_zval('a');
```
运行结果:
```
a: (refcount=3, is_ref=0)='new string'
a: (refcount=1, is_ref=0)='new string'
```
分析:
```
当没有必要时, PHP不会复制已经生成的zval容器, 上面的代码中$a、$b、$c指向了同一个zval容器.
由于该变量是普通变量而不是引用变量, 所以is_ref__gc为0.
由于unset()后只有1个变量指向该zval容器, 所以refcount__gc为1.
```

### 复合类型会为每个item项生成一个zval容器
复合数据类型包括Array和Object, array和object类型的变量会把它们的成员或属性存在自己的符号表中, 而且会为每个item项生成一个zval容器.
```php
$a = array('meaning' => 'life', 'number' => 42);
xdebug_debug_zval('a');
```
运行结果:
```
a: (refcount=1, is_ref=0)=array (
   'meaning' => (refcount=1, is_ref=0)='life',
   'number' => (refcount=1, is_ref=0)=42
)
```
图示:  
![zval-array](https://raw.githubusercontent.com/duiying/img/master/zval-array.png)  
分析:
```
生成了3个zval容器, a、meaning、number.
对于每个zval, 其refcount的增减规则和普通变量相同.
``` 

### 添加一个元素到数组中,该元素指向数组其它元素指向的值
```php
$a = array('meaning' => 'life', 'number' => 42);
$a['life'] = $a['meaning'];
xdebug_debug_zval('a');
```
运行结果:
```
a: (refcount=1, is_ref=0)=array (
   'meaning' => (refcount=2, is_ref=0)='life',
   'number' => (refcount=1, is_ref=0)=42,
   'life' => (refcount=2, is_ref=0)='life'
)
```
图示:  
![zval-array-repeat](https://raw.githubusercontent.com/duiying/img/master/zval-array-repeat.png)  
分析:
```
meaning和life指向同一个zval容器
``` 

### 从数组中删除元素
```php
$a = array('meaning' => 'life', 'number' => 42);
$a['life'] = $a['meaning'];
unset($a['meaning'], $a['number']);
xdebug_debug_zval('a');
```
运行结果:
```
a: (refcount=1, is_ref=0)=array (
   'life' => (refcount=1, is_ref=0)='life'
)
```
分析:
```
从数组中删除元素时, 会从符号表中删除相应的符号, 同时减少对应zval的refcount值.
同样, 如果zval的refcount值减少到0, 那么就会从内存中删除该zval容器.
``` 

### 数组元素指向数组本身
```php
$a = array('one');
$a[] = &$a;
xdebug_debug_zval('a');
```
运行结果:
```
a: (refcount=2, is_ref=1)=array (
   0 => (refcount=1, is_ref=0)='one',
   1 => (refcount=2, is_ref=1)=...
)
```
图示:  
![zval-array-self](https://raw.githubusercontent.com/duiying/img/master/zval-array-self.png)  
分析:
```
数组下标为1的元素指向数组的zval容器.
``` 
如果我们unset($a), 变量$a和数组下标为1的元素所指向的zval容器refcount减1.
```
(refcount=1, is_ref=1)=array (
   0 => (refcount=1, is_ref=0)='one',
   1 => (refcount=1, is_ref=1)=...
)
```
图示:  
![zval-array-unset](https://raw.githubusercontent.com/duiying/img/master/zval-array-unset.png)  

### 清除zval容器的问题
```
unset($a)之后, 除了数组下标为1的元素之外没有某个符号指向该zval容器.
但是该zval容器却不能被GC(指的是PHP5.3之前的单纯引用计数的机制的GC)清理掉, 因为zval的refcount大于0.
PHP脚本执行结束后会清除该zval容器, 但是在此之前, 无法释放的内存导致了内存泄露.
如果这种内存泄露仅仅发生了一次或者少数几次, 倒也还好, 但如果是成千上万次的内存泄露, 便是很大的问题了.
尤其在长时间运行的脚本中(例如守护程序, 一直在后台执行不会中断), 由于无法回收内存, 最终会导致系统再无内存可用.
```

### 参考
- [http://www.cnblogs.com/leoo2sk/archive/2011/02/27/php-gc.html](http://www.cnblogs.com/leoo2sk/archive/2011/02/27/php-gc.html)
- [https://www.php.net/manual/zh/features.gc.php](https://www.php.net/manual/zh/features.gc.php)
- [https://learnku.com/articles/15582/php-garbage-collection-and-memory-management-guidelines](https://learnku.com/articles/15582/php-garbage-collection-and-memory-management-guidelines)
- [https://www.cnblogs.com/ohmygirl/p/internal-variable-1.html](https://www.cnblogs.com/ohmygirl/p/internal-variable-1.html)