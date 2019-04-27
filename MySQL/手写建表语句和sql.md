# 手写建表语句和sql

一张雇员表employee, 一张部门表department, 结构如下, 写出建表语句  

id|emp_name|dept_id
--|--|--
1|张三|1
2|李四|1
3|王五|2

id|dept_name
--|--
1|售前
2|客服
3|开发

```sql
# employee表
CREATE TABLE `employee` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `emp_name` varchar(255) NOT NULL DEFAULT '',
  `dept_id` int(11) unsigned DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

# department表
CREATE TABLE `department` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `dept_name` varchar(255) NOT NULL DEFAULT '',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

如果要查询出如下结果, 请写出sql语句  

id|dept_name|num
--|--|--
1|售前|2
2|客服|1
3|开发|0

```sql
SELECT
	dept.id,
	dept.dept_name,
	(
		SELECT
			count(emp.id)
		FROM
			employee emp
		WHERE
			emp.dept_id = dept.id
	) AS num
FROM
	department dept;
```