# 写一个函数将字符串'make_by_id'装换成'MakeById'

> 考察是否能够灵活运行函数  

知识准备:
```
ucfirst() 把字符串首字符转换成大写
explode() 把字符串分割成数组
implode() 把数组合并成字符串
```
代码:
```php
<?php

function convertStr($str)
{
    $arr = explode('_', $str);
    foreach ($arr as $k => $v) {
        $arr[$k] = ucfirst($v);
    }
    return implode('', $arr);
}

$str = 'make_by_id';
echo convertStr($str);
```
