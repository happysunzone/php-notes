# MyISAM和InnoDB的区别

```
1. InnoDB支持事务; MyISAM不支持
2. MyISAM强调的是性能, 查询速度比InnoDB快; InnoDB适合频繁修改以及涉及到安全性较高的应用
3. InnoDB支持外键; MyISAM不支持
4. MySQL5.5以后, 默认存储引擎是InnoDB
5. InnoDB不支持全文索引; MyISAM支持全文索引
6. MyISAM仅支持表锁,并发较小; InnoDB支持行锁, 并发较大
7. 使用delete删除表的时候, InnoDB是逐行删除; 而MyISAM是先DROP表, 然后重新建表, MyISAM的效率快
8. 对于select count(*) from 表名;
   MyISAM因为保存了表的行数可以直接取出;
   而InnoDB会遍历整个表来计算行数;
   但是对于加了WHERE条件, select count(*) from 表名 WHERE 条件; MyISAM和InnoDB都会遍历整个表来计算行号
```