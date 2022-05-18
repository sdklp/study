# mongodb备份和恢复：mongodump/mongorestore （ 一）

网络上mongorestore都写成mongorerstore抄袭成性

### 1 备份(mongodump)

该命令能够导出全部数据到指定文件夹中。
-h:指明数据库宿主机的IP

-u:指明数据库的用户名

-p:指明数据库的密码

-d:指明数据库的名字

-c:指明collection的名字

-o:指明到要导出的文件名

-q:指明导出数据的过滤条件

### 2 恢复(mongorestore)

参数说明：

-h:指明数据库宿主机的IP

-u:指明数据库的用户名

-p:指明数据库的密码

-d:指明数据库的名字

-c:指明collection的名字

-o:指明到要备份的文件名

-q:指明备份数据的过滤条件

### 3 基本例子

#### 3.0之前例子

备份指定数据库（之前文件夹要自己创建）:
备份本地的annosys数据库到/data/backup/dump文件，会自动出现annosys文件夹
mongodump -h localhost:27017 -d annosys -o /data/backup/dump

从指定目录恢复数据库annosys，注意目录是否包含数据库名
mongorestore -h localhost:27017 -d annosys --directoryperdb /data/backup/dump
mongorestore -h localhost:27017 -d annosys --drop data/backup/test/

#### 3.0之后例子

mongodb3.0后mongorestore的--directoryperdb被砍了

mongodump -h localhost:27017 -d annosys -o /data/backup/dump
mongorestore -h localhost:27017 /data/backup/dump

### 4 备份全库的重要选项是--oplog：

mongodump有一个值得一提的选项是--oplog，注意这是replica set或者master/slave模式专用，standalone模式运行mongodb并不推荐。

来看看mongodump的选项：
--oplog选项只对全库导出有效，所以不能指定-d选项
--oplog的作用：oplog的幂等性：已存在的数据，重做oplog不会重复；不存在的数据重做oplog就可以进入数据库。所以当做完截止到某个时间点的oplog时，数据库就恢复到了截止那个时间点的状态。

来看看mongorestore的选项。
--oplogReplay：可以重放oplog.bson中的操作内容
--oplogLimit：回放的时间节点，即此时间之前的数据恢复，假设你后面有误操作，误操作的不回复

mongodump -h localhost:27017 -o /data/backup/dump --oplog
mongorestore -h localhost:27017 --drop data/backup/db/
mongorestore -h localhost:27017 --oplogReplay dump

查询误操作的最后时间
bsondump oplog.bson | grep "\"op\":\"d\"" | head
mongorestore -h localhost:27017 --oplogReplay --oplogLimit "1443024507:1" dump/

### 5 mongodb的mongodump/mongorestore和是mongoexport/mongoimport区别

mongodump/mongorestore之外还有一对组合是mongoexport/mongoimport，他们本质是存储BSON格式和JSON格式，所以BSON格式和JSON格式区别在哪里？

（1）mongodump/mongorestore导入/导出的是BSON格式， mongoexport/mongoimport导入/导出的是JSON格式。
（2）BSON则是二进制文件，体积小但对人类几乎没有可读性，JSON可读性强但体积较大。
（3）跨版本的mongodump/mongorestore要做请先检查文档看两个版本是否兼容（大部分时候）。在一些mongodb版本之间，BSON格式可能会随版本不同而有所不同，所以不同版本之间用mongodump/mongorestore可能不会成功，具体要看版本之间的兼容性。当无法使用BSON进行跨版本的数据迁移的时候，使用JSON格式即mongoexport/mongoimport是一个可选项。。
（4）JSON虽然具有较好的跨版本通用性，但其只保留了数据部分，不保留索引，账户等其他基础信息。使用时应该注意。

总结：
如果使用mongodump/mongorestore，可以保留索引和数据和账户信息，省心省力。
如果使用mongodump/mongorestore， 要检查mongo文档版本是否兼容。记得把mongodb的版本记好，尤其是使用docker/mongo镜像，很容易出现版本问题，版本要写在配置文件中。


如果使用mongoexport/mongoimport，不能保留索引和数据和账户信息等。
如果使用mongoexport/mongoimport， 不用检查mongo文档版本是否兼容，有跨版本通用性。

但严格地说，mongoexport/mongoimport的主要作用还是导入/导出数据时使用，并不是一个真正意义上的备份工具，因此还是检查mongo版本，采用mongodump/mongorestore。

### 6 用shell脚本实现MongoDB数据库自动备份，没有pymongo脚本实现备份

```
一、创建MongoDB备份目录
mkdir -p /data/www/backup/temp
mkdir -p /data/www/backup/final
/data/backup/mongo_backup.sh
 
 
二、创建MongoDB数据库备份脚本mongo_backup.sh
#!/bin/bash
 
# 将版本信息写入文件
mongo --version > mongo_version.txt
 
# mac
DUMP=mongodump    #mongodump命令路径
 
# linux
# DUMP=/usr/local/mongodb/bin/mongodump    #mongodump命令路径
 
OUT_DIR=/data/backup/temp   #临时备份目录
TAR_DIR=/data/backup/final    #备份存放路径
 
DATE=`date +%Y_%m_%d`   #获取当前系统时间
TAR_BAK="mongodb_backup_$DATE.tar.gz"    #最终保存的数据库备份文件
 
 
DB_HOST=localhost:27017
DB_USER=    #数据库账号
DB_PASS=    #数据库密码
DAYS=15    #DAYS=15代表删除15天前的备份，即只保留近15天的备份
 
# 进入临时目录删空文件夹内容，根据当前时间重建文件
cd $OUT_DIR
rm -rf $OUT_DIR/*
mkdir -p $OUT_DIR/$DATE
 
# 备份全部数据库，并压缩
echo "backup start"
# $DUMP -h $DB_HOST -u $DB_USER -p $DB_PASS --authenticationDatabase "admin" -o # $OUT_DIR/$DATE
 
$DUMP -h $DB_HOST --authenticationDatabase "admin" -o $OUT_DIR/$DATE
 
 
# 压缩为.tar.gz格式  
tar -czvf $TAR_DIR/$TAR_BAK $OUT_DIR/$DATE
# 解压 tar -xzvf $OUT_DIR/$DATE 竟然解压不了
   
 
#删除DAYS天前的备份文件
find $TAR_DIR/ -mtime +$DAYS -delete  
 
echo "backup end"
exit
 
三、sudo chmod +x mongo_backup.sh
四、运行 sh mongo_backup.sh
五、目录查看
六、数据恢复
备份时间下的文件有：annosys，admin，mydb
mongorestore -h 127.0.0.1:27017 -d annosys /data/backup/temp/2019_01_09
 
 
 
七：进入数据库查看
show dbs
use annoys
db.annosys.find()
 
八、添加定时任务：
 
crontab -e
1 0 * * * /bin/bash /data/backup/mongo_backup.sh >> m.log
```

### 7 crontab

```
Cron 各项的描述
以下是 crontab 文件的格式：
 
 
分时日月周
{minute} {hour} {day-of-month} {month} {day-of-week} {full-path-to-shell-script}
（1）minute: 区间为 0 – 59分
（2）hour: 区间为0 – 23 时
（3）day-of-month: 区间为0 – 31日
（4）month: 区间为1 – 12月
（5）Day-of-week: 区间为0 – 7，周日可以是0或7
（6）* 代表全部
 
crontab -e
（1）每天凌晨1分开始备份，这是一个恰当的进行备份的时间，因为此时系统负载不大。
1 0 * * * /data/backup/monbp.sh
 
（2）每个工作日(Mon – Fri) 11:59 p.m 都进行备份作业。
59 11 * * 1,2,3,4,5 /data/backup/mongo_backup.sh
59 11 * * 1-5 /data/backup/mongo_backup.sh
 
 
（3）每5分钟运行一次命令
 
*/5 * * * * /data/backup/monbp.sh
 
（4）每个月的第一天 13:10 p.m 运行
 
10 13 1 * * /root/bin/full-backup.sh
 
（5）每个工作日 11 p.m 运行。
0 23 * * 1-5 /root/bin/incremental-backup.sh
 
 
 
crontab -l #查看你的任务
crontab -e#编辑你的任务
crontab -r#删除用户的crontab的内容
```

　

假如要每4个小时的第1分钟备份一次，那么应该是

1 */4 * * * /bin/bash /data/www/backup/monbp.sh >> /data/www/backup/bp.log 2>&1 

如果写成那么每4个小时的那一个小时的0-59分钟都备份一次也就是60次：

\* */4 * * * /bin/bash /data/www/backup/monbp.sh >> /data/www/backup/bp.log 2>&1 

### 8 删除之前的日志文件find -mtime

```
find $TAR_DIR/ -mtime +$DAYS -delete
 
#mtime参数的理解应该如下：
-mtime n 按照文件的更改时间来找文件，n为整数。
n表示文件更改时间距离为n天， -n表示文件更改时间距离在n天以内，+n表示文件更改时间距离在n天以前。
例如：
-mtime 0 表示文件修改时间距离当前为0天的文件，即距离当前时间不到1天（24小时）以内的文件。
-mtime 1 表示文件修改时间距离当前为1天的文件，即距离当前时间1天（24小时－48小时）的文件。
-mtime＋1 表示文件修改时间为大于1天的文件，即距离当前时间2天（48小时）之外的文件
-mtime -1 表示文件修改时间为小于1天的文件，即距离当前时间1天（24小时）之内的文件
 
为什么-mtime＋1 表示文件修改时间为大于1天的文件，即距离当前时间48小时之外的文件，而不是24小时之外的呢？
因为n值只能是整数，即比1大的最近的整数是2,所有-mtime＋1不是比当前时间大于1天（24小时），而是比当前时间大于2天（48小时）。
```

　　