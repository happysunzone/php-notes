# yield(生成器)

PHP >= 5.5.0  
生成器允许你在foreach代码块中写代码来迭代一组数据而不需要在内存中创建一个数组, 那会使你的内存达到上限, 或者会占据可观的处理时间.  
相反, 你可以写一个生成器函数, 就像一个普通的自定义函数一样, 和普通函数只返回一次不同的是, 生成器可以根据需要yield多次, 以便生成需要迭代的值.  
总之, yield可以节省内存使用, 提高PHP性能, 适合计算大量数据和读取超大文件.

### Example: 生成指定数组并遍历
#### 传统方案
代码:
```php
<?php

/**
 * 生成一个长度为$num的数组
 *
 * @param $num
 * @return array
 */
function createRange($num)
{
    $arr = [];
    for ($i = 0; $i < $num; $i++) {
        sleep(1);
        $arr[] = time();
    }
    return $arr;
}

$arr = createRange(10);

foreach ($arr as $val) {
    echo $val;
}
```
分析:
```
# 执行时, 程序停顿10s, 然后一次性输出foreach的结果
# $arr是一次性生成完毕, 然后返回
# 当$num是100万或更大数据时, $arr都会一次性生成然后加载到内存中, 造成内存大量占用
```

#### 使用yield优化

代码:
```php
<?php

/**
 * 生成一个长度为$num的数组
 *
 * @param $num
 * @return array
 */
function createRange($num)
{
    for ($i = 0; $i < $num; $i++) {
        sleep(1);
        yield time();
    }
}

$arr = createRange(10);

foreach ($arr as $val) {
    echo $val;
}
```
分析:
```
# 执行时, 程序每间隔1s输出一个$val
# $arr的生成依赖于foreach, foreach每循环一次, $arr就生成一个值
# 无论$num是100万或更大数据时, $arr都会只在内存中记录一条数据, 节约大量的内存
```

### 参考
- [https://www.php.net/manual/zh/language.generators.overview.php](https://www.php.net/manual/zh/language.generators.overview.php)