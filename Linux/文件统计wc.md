# 文本统计wc

wc: world count, 统计指定文件中的字节数、行数、字数

### 语法
```
格式: wc [-clw] [文件...]

-c: 字节数
-l: 行数
-w: 字数
```

### Example
```
[root@localhost /]# cat 1.txt
php
php php
extension
[root@localhost /]# wc -l 1.txt
3 1.txt
[root@localhost /]# wc -w 1.txt
4 1.txt
[root@localhost /]# wc -c 1.txt
22 1.txt
```
