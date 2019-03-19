# 定时任务crontab

### 语法
```
格式: f1 f2 f3 f4 f5 program
解释: 
f1(0-59): 分 
f2(0-23): 时 
f3(1-31): 日 
f4(1-12): 月 
f5(0-7 0和7表示星期天): 星期 
program: 命令

# 列出当前用户的定时任务
crontab -l
# 编辑当前用户的crontab
crontab -e
```

### Example
```
# 每分钟向access.log追加date
# crontab -e 编辑内容为 * * * * * date >> access.log
[root@localhost ~]# crontab -e
[root@localhost ~]# crontab -l
* * * * * date >> access.log
# 查看access.log
[root@localhost ~]# tail -f access.log
Tue Mar 19 21:21:01 KST 2019
Tue Mar 19 21:22:01 KST 2019
```
```
# 每分钟
* * * * *
# 每小时的第3分钟和第5分钟
3,5 * * * *
# 每天上午8点到10点的第3分钟和第5分钟
3,5 8-10 * * *
# 每两天上午8点到10点的第3分钟和第5分钟
3,5 8-10 */2  *  *
# 每天的21:30
30 21 * * *
# 每周日的23点
0 23 * * 7
```

### 在线计算crontab执行时间
[在线计算crontab执行时间](https://tool.lu/crontab/)