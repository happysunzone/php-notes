# PHP弱类型是如何实现的

## 目录
- [PHP5](#PHP5)
  - [PHP弱类型是如何实现的?](#PHP弱类型是如何实现的?)
  - [PHP变量的值是采用联合体而不采用结构体的原因?](#PHP变量的值是采用联合体而不采用结构体的原因?)

### PHP5
首先需要知道PHP5中变量是如何存储的, 参考 [PHP5中的zval](PHP5中的zval.md)  
所有的变量都用zval结构体保存, zval结构体中结构成员zvalue_value value保存变量的值.

#### PHP弱类型是如何实现的?
```
PHP将数据的类型保存在zval结构体中的type结构成员中.
举例:
如果zval.type = IS_LONG, 就去取zval.value.lval

如果zval.type = IS_BOOL, 就去取zval.value.lval, lval属于0或1

如果zval.type = IS_RESOURCE, 就去取zval.value.lval, 
此时lval是个整型的指示器, PHP根据指示器在PHP内建的一个资源列表中查询相应的资源.

如果zval.type = IS_ARRAY, 就去取zval.value.ht
```
#### PHP变量的值是采用联合体而不采用结构体的原因?
```
一个变量只能属于一种类型, 如果采用结构体会浪费内存.

结构体和联合体的区别?
struct, 各成员占有自己的内存, 它们是同时存在的, struct变量所占内存的总大小等于所有成员所占内存大小之和.
union, 也叫共用体, union中同一时刻只能存在一个成员.
```

### 参考
- [http://www.laruence.com/2008/08/22/412.html](http://www.laruence.com/2008/08/22/412.html)
- [http://www.helpergarden.com/tag/php弱类型原理](http://www.helpergarden.com/tag/php弱类型原理)
