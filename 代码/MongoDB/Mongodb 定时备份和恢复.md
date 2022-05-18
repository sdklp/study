# Mongodb 定时备份和恢复





> 定时对数据库进行备份可以有效地保护数据

## MongoDB 数据备份

在 MongoDB 中我们使用 mongodump 命令来备份 MongoDB 数据

语法如下：

```
> mongodump -h dbhost -d dbname -o dbdirectory -u user -p password
```

- -h MongDB所在服务器地址，例如：127.0.0.1，当然也可以指定端口号：127.0.0.1:27017
- -d 需要备份的数据库实例，例如：test
- -o 备份的数据存放位置，例如：c:datadump，当然该目录需要提前建立，在备份完成后，系统自动在dump目录下建立一个test目录，这个目录里面存放该数据库实例的备份数据。
- -u -p 如果有设置用户和密码，需要设置对应的用户名和密码，否则没有权限

## MongoDB 数据恢复

mongodb 使用 mongorestore 命令来恢复备份的数据

```
>mongorestore -h <hostname><:port> -d dbname <path>
```

- --host , -h ：
  MongoDB所在服务器地址，默认为： localhost:27017
- --db , -d ：
  需要恢复的数据库实例，例如：test，当然这个名称也可以和备份时候的不一样，比如test2
- --drop：
  恢复的时候，先删除当前数据，然后恢复备份的数据。就是说，恢复后，备份后添加修改的数据都会被删除，慎用哦！
- ：

1. 最后的一个参数，设置备份数据所在位置，例如：c:datadumptest。

你不能同时指定 和 --dir 选项，--dir也可以设置备份目录。

- --dir：
  指定备份的目录

你不能同时指定 和 --dir 选项。

## 定时备份 mongodb

### 环境

操作系统： Centos 7

### 一、 备份 shell 脚本 (/home/crontab/mongobk.sh)

```
#!/bin/sh
# dump 命令执行路径，根据mongodb安装路径而定
DUMP=/usr/bin/mongodump
# 临时备份路径
OUT_DIR=/home/backup/mongod_bak/mongod_bak_now
# 压缩后的备份存放路径
TAR_DIR=/home/backup/mongod_bak/mongod_bak_list
# 当前系统时间
DATE=`date +%Y-%m-%d`
# 数据库账号
DB_USER=user
# 数据库密码
DB_PASS=password
# 代表删除7天前的备份，即只保留近 7 天的备份
DAYS=7
# 最终保存的数据库备份文件
TAR_BAK="mongod_bak_$DATE.tar.gz"
cd $OUT_DIR
rm -rf $OUT_DIR/*
mkdir -p $OUT_DIR/$DATE
$DUMP -h 127.0.0.1:27017 -u $DB_USER -p $DB_PASS -d dbname -o $OUT_DIR/$DATE
# 压缩格式为 .tar.gz 格式
tar -zcvf $TAR_DIR/$TAR_BAK $OUT_DIR/$DATE
# 删除 15 天前的备份文件
find $TAR_DIR/ -mtime +$DAYS -delete

exit
```

### 二、创建对应的备份目录

```
mkdir -p /home/backup/mongodb_bak/mongodb_bak_now
mkdir -p /home/backup/mongodb_bak/mongodb_bak_list
```

### 三、修改文件属性，使其可执行

```
chmod +x MongoDB_bak.sh
```

### 四、添加到计划任务

cron服务是Linux的内置服务，但它不会开机自动启动。可以用以下命令启动和停止服务：

```
/sbin/service crond start

/sbin/service crond stop

/sbin/service crond restart

/sbin/service crond reload
```

以上1-4行分别为启动、停止、重启服务和重新加载配置。

要把cron设为在开机的时候自动启动，在 /etc/rc.d/rc.local 脚本中加入 /sbin/service crond start 即可

查看当前用户的crontab，输入 crontab -l；

编辑crontab，输入 crontab -e；

删除crontab，输入 crontab -r

#### 1 进入编辑界面

```
crontab -e
```

#### 2 添加任务

```
30 18 * * * /home/crontab/mongobk.sh
```

基本格式 :

*　　command

分　时　日　月　周　命令

第1列表示分钟1～59 每分钟用或者 /1表示

第2列表示小时1～23（0表示0点）

第3列表示日期1～31

第4列表示月份1～12

第5列标识号星期0～6（0表示星期天）

#### 3 保存后退出，启动服务

```
service crond start
```

#### 4 设置开机自启动

```
chkconfig crond on
```