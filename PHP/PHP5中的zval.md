# PHP5中的zval

```
zval是PHP中最重要的数据结构之一(另一个重要的数据结构是HashTable).
zval中保存了PHP中变量的值和类型等相关信息.
```

#### zval定义
```c
struct _zval_struct {
    zvalue_value value;         /* 变量的值 */
    zend_uint refcount__gc;     /* 引用计数 */
    zend_uchar type;            /* 变量类型 */
    zend_uchar is_ref__gc;      /* 是否被引用 */
};
typedef struct _zval_struct zval;
```
### 结构成员
```
# zvalue_value value
zvalue_value value存储变量的值, zvalue_value是一个联合体(union).
# zvalue_value定义
typedef union _zvalue_value {
    long lval;                 // 用于bool类型、整型和资源类型
    double dval;               // 用于浮点类型
    struct {                   // 用于字符串
        char *val;
        int len;
    } str;
    HashTable *ht;             // 用于数组
    zend_object_value obj;     // 用于对象
    zend_ast *ast;             // 用于常量表达式(PHP5.6 才有)
} zvalue_value;

# zend_uchar type
zend_uchar type存储变量的类型, PHP中数据类型对应下面的宏:
#define IS_NULL     0
#define IS_LONG     1
#define IS_DOUBLE   2
#define IS_BOOL     3
#define IS_ARRAY    4
#define IS_OBJECT   5
#define IS_STRING   6
#define IS_RESOURCE 7
#define IS_CONSTANT 8
#define IS_CONSTANT_ARRAY   9
#define IS_CALLABLE 10

# zend_uint refcount__gc
zend_uint refcount__gc存储有多少变量指向该zval

# zend_uchar is_ref__gc
zend_uchar is_ref__gc表示该zval是否被引用(&), 0表示普通变量 1表示引用变量
```