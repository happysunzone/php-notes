# PHP7中的zend_reference

PHP7中引用成为一种数据结构(zend_reference), 而不是PHP5中的标志位(is_ref).  
对于IS_REFERNCE类型的zval, zval.value.ref是一个指向zend_reference的指针, 它包含了引用计数和一个zval.
```c
# _zend_reference
struct _zend_reference {
    zend_refcounted_h gc;
    zval              val;
};

# _zend_refcounted_h
typedef struct _zend_refcounted_h {
    uint32_t         refcount;          /* reference counter 32-bit */
    union {
        struct {
            ZEND_ENDIAN_LOHI_3(
                zend_uchar    type,
                zend_uchar    flags,    /* used for strings & objects */
                uint16_t      gc_info)  /* keeps GC root number (or 0) and color */
        } v;
        uint32_t type_info;
    } u;
} zend_refcounted_h;
```

### Change On Write

#### PHP5
```php
$a = [];  // $a         -> zval_1(type=IS_ARRAY, refcount=1, is_ref=0) -> HashTable_1(value=[])
$b = $a;  // $a, $b     -> zval_1(type=IS_ARRAY, refcount=2, is_ref=0) -> HashTable_1(value=[])
$c = $b   // $a, $b, $c -> zval_1(type=IS_ARRAY, refcount=3, is_ref=0) -> HashTable_1(value=[])

$d =& $c; // $a, $b -> zval_1(type=IS_ARRAY, refcount=2, is_ref=0) -> HashTable_1(value=[])
          // $c, $d -> zval_1(type=IS_ARRAY, refcount=2, is_ref=1) -> HashTable_2(value=[])
          // 因为is_ref不同, 这个时候虽然$c/$d的值还是和$a/$b相同, 但我们不得不拷贝一份HashTable_2
```
这样的设计在于没有办法把同一个数据在引用和非引用的变量上共享.
#### PHP7
```php
$a = [];  // $a         -> zend_array_1(refcount=1, value=[])
$b = $a;  // $a, $b,    -> zend_array_1(refcount=2, value=[])
$c = $b   // $a, $b, $c -> zend_array_1(refcount=3, value=[])

$d =& $c; // $a, $b                                 -> zend_array_1(refcount=3, value=[])
          // $c, $d -> zend_reference_1(refcount=2) ---^
          // 创建引用变量的时候, 不需要再复制原始的数据了, 只需要创建一个引用类型数据并且指向原始数据就可以了

$d[] = 1; // $a, $b                                 -> zend_array_1(refcount=2, value=[])
          // $c, $d -> zend_reference_1(refcount=2) -> zend_array_2(refcount=1, value=[1])
          // 只有值发生改变时, 才拷贝一份新数据, 符合Change On Write的原则
```
PHP7的设计保证了可以在引用和非引用的变量之间共享数据, 做到了真正的Change On Write

### 参考
- [http://www.laruence.com/2018/04/08/3179.html](http://www.laruence.com/2018/04/08/3179.html)
- [http://joshuais.me/php7-nei-he-fen-xi-bian-liang-de-she-ji/](http://joshuais.me/php7-nei-he-fen-xi-bian-liang-de-she-ji/)