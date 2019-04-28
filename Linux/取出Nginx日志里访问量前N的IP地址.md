# 取出Nginx日志里访问量前N的IP地址
access.log内容如下
```
222.178.10.177 - - [22/Feb/2019:12:07:10 +0000] "GET / HTTP/1.1" 200 109 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:65.0) Gecko/20100101 Firefox/65.0" "-"
222.178.10.178 - - [22/Feb/2019:12:07:10 +0000] "GET /favicon.ico HTTP/1.1" 200 109 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:65.0) Gecko/20100101 Firefox/65.0" "-"
222.178.10.179 - - [22/Feb/2019:12:23:04 +0000] "GET / HTTP/1.1" 302 5 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:65.0) Gecko/20100101 Firefox/65.0" "-"
222.178.10.177 - - [22/Feb/2019:12:23:05 +0000] "GET /install/index.html HTTP/1.1" 302 5 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:65.0) Gecko/20100101 Firefox/65.0" "-"
222.178.10.178 - - [22/Feb/2019:12:23:05 +0000] "GET /install/index.html HTTP/1.1" 302 5 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:65.0) Gecko/20100101 Firefox/65.0" "-"
222.178.10.179 - - [22/Feb/2019:12:23:05 +0000] "GET /install/index.html HTTP/1.1" 302 5 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:65.0) Gecko/20100101 Firefox/65.0" "-"
222.178.10.177 - - [22/Feb/2019:12:23:05 +0000] "GET /install/index.html HTTP/1.1" 302 5 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:65.0) Gecko/20100101 Firefox/65.0" "-"
222.178.10.178 - - [22/Feb/2019:12:23:05 +0000] "GET /install/index.html HTTP/1.1" 302 5 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:65.0) Gecko/20100101 Firefox/65.0" "-"
222.178.10.179 - - [22/Feb/2019:12:23:05 +0000] "GET /install/index.html HTTP/1.1" 302 5 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:65.0) Gecko/20100101 Firefox/65.0" "-"
222.178.10.179 - - [22/Feb/2019:12:23:05 +0000] "GET /install/index.html HTTP/1.1" 302 5 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:65.0) Gecko/20100101 Firefox/65.0" "-"
```
awk命令
```shell
awk '{print $1}' access.log | sort | uniq -c | sort -nr -k1 | head -n N
```
### 说明
awk '{print $1}' access.log
```
# 将一行中的内容以空格为分隔符, 打印第一个字段
```

sort
```shell
# 由于access.log文件中的第一个字段是IP, 所以sort是对IP进行排序
[root@VM_0_15_centos proxy]# awk '{print $1}' access.log | sort
222.178.10.177
222.178.10.177
222.178.10.177
222.178.10.178
222.178.10.178
222.178.10.178
222.178.10.179
222.178.10.179
222.178.10.179
222.178.10.179
```

uniq -c
```shell
# uniq用于检查及删除在文本文件中重复出现的行
# 删除的重复的行必须是相邻行, 所以在使用uniq之前, 请先使用sort使重复行相邻
# -c用于在每行前面显示重复出现的次数
[root@VM_0_15_centos proxy]# awk '{print $1}' access.log | sort | uniq -c
      3 222.178.10.177
      3 222.178.10.178
      4 222.178.10.179
```
sort -nr -k1
```shell
# 按照重复出现的次数(第一列)降序排序
# -n用于依照数值的大小排序
# -r用于降序排序
# -k1用于按照第一列排序
[root@VM_0_15_centos proxy]# awk '{print $1}' access.log | sort | uniq -c | sort -nr -k1
      4 222.178.10.179
      3 222.178.10.178
      3 222.178.10.177
```
head -n N
```
# 显示前N行
[root@VM_0_15_centos proxy]# awk '{print $1}' access.log | sort | uniq -c | sort -nr -k1 | head -n 2
      4 222.178.10.179
      3 222.178.10.178
```

