# mysql基本语法大全

[![backlion](https://pica.zhimg.com/da8e974dc_xs.jpg?source=172ae18b)](https://www.zhihu.com/people/backlion)

[backlion](https://www.zhihu.com/people/backlion)

web安全

18 人赞同了该文章

## 1.备份数据库：

### 1.1备份数据库中的表:

```text
 mysqldump  -u  root  -p  test  a b  >d:\bank_a.sql
 //分别备份数据库test下a和b表
```

### 1.2备份一个数据库

```text
mysqldump -u root -p test > d:\testbk.sql
```

### 1.3备份多个数据库

```text
mysqldump -u root -p --databases test mysql > D:\data.sql
```

### 1.4备份所有的数据库

```text
mysqldump -u -root -p  --all-databases > D:\all.sql
```

### 1.5直接复制整个数据库目录（物理备份）

```text
前提条件是停止mysql服务，然后复制mysql下的data目录下数据库目录。
```

## 2.还原数据：

### 2.1使用mysql命令恢复

```text
还原数据库文件:
mysql -u root -p  < d:\backup.sql
还原数据库表文件：
mysql -u  root  -p  test <d:\backup.sql
```

### 2.2使用source命令恢复数据

```text
还原数据库文件：
mysql>source d:\testbk.sql 
还原数据库表文件：
mysql>use test
mysql>source d:\testbk.sql
```

### 2.3先停止mysql服务，然后拷贝备份的整个test数据库目录到目标目录。

### 2.4网络上远程还原数据可以用

```text
mysqldump   -h   x.x.x.x  -u   root  -p   test >tesbk.sql
```

### 2.5mysql忘记密码：

```text
1.停止mysql服务
net stop mysql或者相应的进程
2.进入mysql下bin目录：
mysqld --skip-grant-tables  //跳过验证登录
3.另外窗口打开：
mysql   //直接可用进入系统
4.更改密码
use mysql;
update user set password=password('123456') where user='root' and host='localhost';
5.注销系统，再进入，开MySQL，使用用户名root和刚才设置的新密码123456登陆
```

### 2.6修改密码：

```text
1.你的root用户现在没有密码，你希望的密码修改为123456，那么命令是：
mysqladmin -u root password 123456
2.如果你的root现在有密码了（123456），那么修改密码为abcdef的命令是：
mysqladmin -u root -p password 123456
```

### 2.7添加用户：

```text
grant select,insert,update,delete,create,drop on stud.* to user1@localhost identified by "user1";  //添加用户名user1密码为user1具有插入，更新，删除，创建，删除对于数据库所有表
GRANT ALL PRIVILEGES ON *.* TO 'backlion'@'%' IDENTIFIED BY 'backlion123' WITH GRANT OPTION;
grant  all  privileges on  *.*  to  test@loclhost identified  by "test";
创建主键：
Alter table test add primary key(code,curlum);  //用code和curlum作为一组联合主键来约束
```

## 3.数据库操作：

### 3.1显示数据库

```text
show databases;
```

### 3.2选择数据库

```text
use examples;
```

### 3.3创建数据库并设置编码utf8 多语言

```text
create database bk  default character set utf8 collate utf8_general_ci;
```

### 3.4修改数据库字符集为utf8

```text
use  mysql;
alter  database  character  set utf8;
```

### 3.5删除数据库

```text
drop database  bk;
```

### 3.6查看数据库状态：

```text
status;
```

## 4.数据表的操作：

### 4.1显示数据表

```text
show tables;
```

### 4.2查看数据表的结构属性（字段，类型）

```text
describe  test;
desc  test;
```

### 4.3复制表结构（里面没有数据，结构一样）

```text
create table  newtest like  oldtest; //创建新表newtest和旧表oldtest数据表结构一样
```

### 4.4复制表中的数据

```text
insert into  nettest select  *  from  oldtest;  //将旧表oldtest的数据复制到新表newtest里面
```

### 4.6重命名表名

```text
alter table  old_name  rename  new_name
```

### 4.7显示当前mysql版本和当前日期

select version(),current_date;

### 4.8创建表：

```text
create table bk(
id int(10) unsigned zerofill not null auto_increment,
email varchar(40) not null,
ip varchar(15) not null,
state int(10) not null default '-1',
primary key (id)
);
/*
1.常用的数据类型为int,varchar,date,text这4个数据类型
2.字段（行）的属性有：数据类型（数据长度） 是否为空 是否为主键  是否为自动增加  默认值
如：int(25)  not null   primary key   auto_increment default  12
3.每个字段之间用逗号分开，最后那个字段不需要用逗号
4.以分号结束
5.可以设置简单数据表结构如：
字段名数据类型   数据长度   是否为空   是否主键是否自动增加   默认值
 id  int  12  NOT NULL  primary key  auto_increment
 name varchar   30NOT NULL
 password varchar30   NOT NULL
 time  date   30  NOT NULL 
 jianyi text 400
*/
```

### 4.9删除数据表：

```text
drop  table  bk; //包括结构和数据都删除
```

## 10.数据库字段的操作：

### 11.1添加表字段

```text
alter  table  test  add bk varchar(32) not null;  //向表test中添加bk字段（列）
```

### 11.2修改表字段

```text
alter table test change id id1 varchar(10) not null;  //将表test中字段（列）id更改为id1
```

### 11.3删除字段（列）

```text
alter  table  test drop cn;
```

### 11.4插入表数据

```text
insert into test (id11,email,ip,state,bk)value(2,'601462930@qq.com','10.192.16.12',1314,567);  
//如果是字符型对应的值需要用单引号引起来，数字型不需要
```

### 11.5删除数据

```text
delete  from  test  //删除整个表test的数据，结构保留
delete  from  test  where id11=2; 删除数据表来自某个主键字段。就等于删除整条数据
```

### 11.6修改表字段数据信息(数据）

```text
Update table_name set 字段名=’新值’ [, 字段2 =’新值’ , …..][where id=id_num] [order by 字段 顺序] 
update test set email='895098355@qq.com'  where id11=2;
```

### 11.7修改字段的属性（数据类型）

```text
alter table  test  modify class  varchar(30) not null;   //修改表test中字段class的属性为varchar(30)
```

## 12.查询数据：

### 12.1查询所有数据：

```text
select  *  form  test;
```

### 12.2查询两个字段的数据

```text
select  id,number   from test;
```

### 12.3查询前2行数据：

```text
select  *  from  test  limit 0,2;
```

### 12.4按增序排列查询

```text
select *  from  test order by  id11  asc;
select *  from  test order by  id11  //默认为增序查询
```

### 12.5按降序排列查询

```text
select *  from  test order by id11 desc;
```

### 12.6模糊查询

```text
select  * from  test  where  email  '%qq%';  //查询test表中，条件是email的数据中包含qq的数据
```

### 12.7查询某个字段下面的数据

```text
select  email  as  emaildata  from  test ;  //选择email字段作为emalidata统计显示出的数据
```

### 12.8.条件查询

```text
select  *  form  test  where  id=12;
```

## 13.多表查询：

### 13.1用法一：where条件联合查询

```text
select 表1.字段 [as 别名],表n.字段 from 表1 [别名],表n where 条件;
select testA.username  as username,testB.id from testA,testB  where testA.uid=testB.uid
```

### 13.2用法二：inner join on 条件联合查询

```text
select 表1.字段 [as 别名],表n.字段 from 表1 INNER JOIN 表n on 条件;
select  testA.username  as  uername ,testB.id from testA inner join testB on  testA.uid=testB.uid
```

### 13.3记录联合:

```text
select语句1 union[all] select语句2
select *  from  testA  union  select  id  from testB;
```