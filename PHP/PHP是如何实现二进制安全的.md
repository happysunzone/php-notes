# PHP是如何实现二进制安全的

前言
```
PHP是用C语言开发的, PHP代码会被Zend引擎编译成opcode, 最终作为C语言去执行.
```

什么叫二进制安全?
```
程序在操作二进制数据时, 数据写入时和读取时是相同的.
```

C语言中, '\0'是字符串的结束符, 当读到'\0'时会停止读取, 所以C语言是二进制不安全的.
```c
#include <stdio.h>

int main()
{
    char str[] = "Hello\0Hello";
    printf("%s\n", str);
    return 0;
}
```
运行结果:
```
[root@localhost c]# gcc binary-safe.c -o binary-safe
[root@localhost c]# ./binary-safe
Hello
```
PHP是二进制安全的, 下面的代码输出-1, 表示两个字符串不相等.
```php
<?php
$a = "Hello\0Hello";
$b = "Hello\0World";
var_dump(strcmp($a, $b));
```
PHP是用C语言开发的, 它是如何实现二进制安全的?
```
# PHP5中字符串的定义
struct{
	char *val;
	int len;
} str;

# PHP7中字符串的定义
struct _zend_string {
    zend_refcounted_h gc;       /* 字符串类别及引用计数 */
    zend_ulong        h;        /* 字符串的哈希值 */
    size_t            len;      /* 字符串的长度 */
    char              val[1];   /* 柔性数组, 字符串存储位置 */
};
```
因为len记录的是字符串的长度, 所以PHP处理字符串的时候不需要像C语言那样通过'\0'来判断字符串是否结束, 正是len的存在, 使得PHP实现了二进制安全.
