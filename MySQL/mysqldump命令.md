# mysqldump命令

mysqldump命令用于备份数据库

```
# 格式
mysqldump -h主机地址 -u用户名 -p密码 数据库名 > 文件名

# Example
mysqldump -uroot -proot mysql > /mysql_bak.sql
```