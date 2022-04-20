  SQL的自增列挺好用,只是开发过程中一旦删除数据,标识列就不连续了 写起来 也很郁闷,所以查阅了一下标识列重置的方法 发现可以分为三种:

#### --- 删除原表数据,并重置自增列

```mysql
truncate table tablename
--truncate方式也可以重置自增字段
```

####   --重置表的自增字段,保留数据

```mssql
DBCC CHECKIDENT (tablename,reseed,0)
```

#### -- 设置允许显式插入自增列

```mssql
SET IDENTITY_INSERT tablename  ON
```

#### -- 当然显式插入完毕记得要设置不允许显式插入自增列

```mssql
SET IDENTITY_INSERT tablename  Off  
```


