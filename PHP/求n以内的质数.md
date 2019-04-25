# 求n以内的质数

分析
```
质数: 又称素数, 在大于1的自然数中, 除了1和它本身以外不再有其它因数, 如 2,3,5,7,11 等

思路:
1. 2以外的偶数都不是质数
2. n不能被 小于根号n 的任何质数整除, 则n是一个质数
```
代码
```php
<?php

/**
 * 求n以内的质数
 *
 * @param $n
 * @return array
 */
function prime($n)
{
    // 质数数组
    $prime = [2];

    for ($i = 3; $i <= $n; $i += 2) {
        $sqrt = intval(sqrt($i));
        for ($j = 3; $j <= $sqrt; $j += 2) {
            if ($i % $j == 0) {
                // 跳到外层循环
                continue 2;
            }
        }
        array_push($prime, $i);
    }

    return $prime;
}

// 测试
var_dump(prime(100));
```