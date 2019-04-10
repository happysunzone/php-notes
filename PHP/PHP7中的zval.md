# PHP7中的zval

### zval
```c
struct _zval_struct {
    zend_value        value;                /* value */
    union {
        struct {
            ZEND_ENDIAN_LOHI_4(
                zend_uchar    type,         /* active type */
                zend_uchar    type_flags,
                zend_uchar    const_flags,
                zend_uchar    reserved)     /* 保留字段 */
        } v;
        uint32_t type_info;
    } u1;
    union {
        uint32_t     var_flags;
        uint32_t     next;                  /* hash collision chain */
        uint32_t     cache_slot;            /* literal cache slot */
        uint32_t     lineno;                /* line number (for ast nodes) */
        uint32_t     num_args;              /* arguments number for EX(This) */
        uint32_t     fe_pos;                /* foreach position */
        uint32_t     fe_iter_idx;           /* foreach iterator index */
    } u2;
};
```

### 结构成员
zend_value
```c
typedef union _zend_value {
    zend_long         lval;             /* 整型 */
    double            dval;             /* 浮点型 */
    zend_refcounted  *counted;          /* 引用计数 */
    zend_string      *str;              /* 字符串 */
    zend_array       *arr;              /* 数组 */
    zend_object      *obj;              /* 对象 */
    zend_resource    *res;              /* 资源 */
    zend_reference   *ref;              /* 引用 */
    zend_ast_ref     *ast;              /* 抽象语法树 */
    zval             *zv;               /* zval类型 */
    void             *ptr;              /* 指针类型 */
    zend_class_entry *ce;               /* class类型 */
    zend_function    *func;             /* function类型 */
    struct {
        uint32_t w1;
        uint32_t w2;
    } ww;
} zend_value;
```
u1.v.type
```
/* regular data types */
#define IS_UNDEF                    0
#define IS_NULL                     1
#define IS_FALSE                    2
#define IS_TRUE                     3
#define IS_LONG                     4
#define IS_DOUBLE                   5
#define IS_STRING                   6
#define IS_ARRAY                    7
#define IS_OBJECT                   8
#define IS_RESOURCE                 9
#define IS_REFERENCE                10

/* constant expressions */
#define IS_CONSTANT                 11
#define IS_CONSTANT_AST             12

/* fake types */
#define _IS_BOOL                    13
#define IS_CALLABLE                 14

/* internal types */
#define IS_INDIRECT                 15
#define IS_PTR                      17
```

### PHP7中zval相比于PHP5的变化
```
1. PHP7中的zval由于使用union, 占用16字节. PHP5中的zval占用24字节. 内存占用减少了1/3.
2. zval_struct中的zend_long、double直接存储具体的值, 其他复杂数据类型存储指针.
3. 引用计数器存储在zend_value而不是zval_struct, 引用计数器记录有多少zval指向同一个zend_value.
4. NULL(IS_NULL)、布尔型(IS_FALSE、IS_TRUE)属于没有值的数据类型, 没有引用计数.
5. 引用成为一种数据结构(zend_reference), 而不是PHP5中的标志位(is_ref).
```

### PHP7的引用计数
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
整型、浮点型数据, 直接保存在zend_value中的zend_long、double中, 不需使用引用计数, 这样可以省掉引用计数的相关操作.
```php
$a = 1;
$b = $a;
// (refcount=0, is_ref=0)int 1
xdebug_debug_zval('a');
```

内部字符串(函数名、类名、变量名、静态字符串), 字符串的内容不会发生变化, 无需使用引用计数.
```php
// 静态字符串
$a = 'str';
$b = $a;
// (refcount=0, is_ref=0)string 'str' (length=3)
xdebug_debug_zval('a');

// 普通字符串
$c = 'str' . time();
$d = $c;
// (refcount=2, is_ref=0)string 'str1554866026' (length=13)
xdebug_debug_zval('c');
```

引用赋值
```php
$a = 1;
// (refcount=0, is_ref=0)int 1
xdebug_debug_zval('a');
$b = &$a;
// (refcount=2, is_ref=1)int 1
xdebug_debug_zval('a');
```

$a = 1;  
![zval-int](https://raw.githubusercontent.com/duiying/img/master/zval-int.png)  

$b = &$a;  
![zval-ref](https://raw.githubusercontent.com/duiying/img/master/zval-ref.png)  

引用赋值的执行过程
```
1. PHP为&操作符操作的变量申请一个zend_reference结构
2. 将zend_reference.value指向原来的zval_struct.value
3. zval_struct.value的type改为IS_REFERENCE
4. zval_struct.value指向zend_reference
5. 新变量申请一个zval_struct结构, 将他的value指向zend_reference
```



### 参考
- [https://www.cnblogs.com/enochzzg/p/9626946.html](https://www.cnblogs.com/enochzzg/p/9626946.html)
- [http://www.laruence.com/2018/04/08/3170.html](http://www.laruence.com/2018/04/08/3170.html)
- [https://www.cnblogs.com/sohuhome/p/9800977.html](https://www.cnblogs.com/sohuhome/p/9800977.html)
- [http://joshuais.me/php7-nei-he-fen-xi-bian-liang-de-she-ji/](http://joshuais.me/php7-nei-he-fen-xi-bian-liang-de-she-ji/)
- [https://segmentfault.com/a/1190000012980509](https://segmentfault.com/a/1190000012980509)
- [https://juejin.im/post/5bbf50e86fb9a05ce02a9c19](https://juejin.im/post/5bbf50e86fb9a05ce02a9c19)