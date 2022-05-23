提取到数据后，编写pipeline.py文件，保存数据到mysql。

**1、保存数据库有两种方法：**

- 同步操作：数据量少的时候采用
- 异步操作：数据量大时采用，scrapy爬取的速度大于数据库插入的速度，当数据量大时就会出现堵塞，就需要采用异步保存。

这里多大的数据量才可定义为大？？

**2、须知mysql知识点**

- 数据库与表的创建，基本操作；      参考https://blog.csdn.net/shalyniu/article/details/79247423
- 数据库与表的删除，使用频率少；   参考https://blog.csdn.net/shalyniu/article/details/79247423
- 插入数据，在爬虫时执行频率非常之高； insert into test_table(colname1,colname2,……) values(value1,value2,……)
- 查询数据；select * from test_table

**3、mysql数据库的安装**

参考https://www.cnblogs.com/raind/p/8977135.html

**4、python提供的MySQLdb/pymysql模块**

pymsql是Python中操作MySQL的模块，其使用方法和MySQLdb几乎相同。

pymysql支持python3.x而MySQLdb不支持3.x版本。

①连接数据库

```python
db = MySQLdb.connect(host = "localhost",user = "root",passwd = "*****",db = "test",port = 3306,charset = 'utf8')
```

连接数据库过程中传入多个参数：数据库主机名（默认为本地主机），数据库登录名（默认为当前用户），数据库密码（默认为空），要打开的数据库名称（无默认，可缺省），MySQL使用的TCP端口（默认为3306，可缺省），数据库字符编码（可缺省）

②获取游标

```sql
cursor = db.cursor()
```

游标就像是鼠标一样，后续操作数据库全部靠游标，使用游标的execute命令来执行。

③执行数据库命令

```sql
cursor.execute("select * from test")
```

注：

 

- 在括号内填写sql语句的时候不需要加分号来提示语句结束。

④提交数据库执行

```sql
db.commit()
```

针对增删改的操作，需要提交后才会真正的保存到数据库中。

⑤关闭数据库

```python
db.close()
```

⑥数据库回滚

在语句执行错误的时候，使用回滚操作来撤回该语句的执行。

```python
try:
    cursor.execute("create database python")
    db.commit()
except:
    db.rollback()
```

**5、【方法一：同步操作】pipelines.py文件（处理数据的python文件）**

```python
import pymysql

class MysqlPipeline(object):
    """
    同步操作
    """
    def __init__(self):
        # 建立连接
        self.conn = pymysql.connect('localhost','root','Abcd1234','test')  # 有中文要存入数据库的话要加charset='utf8'
        # 创建游标
        self.cursor = self.conn.cursor()



    def process_item(self,item,spider):
        # sql语句
        insert_sql = """
        insert into test_zxf(quote,author,tags,born_date,born_location) VALUES(%s,%s,%s,%s,%s)
        """
        # 执行插入数据到数据库操作
        self.cursor.execute(insert_sql,(item['quote'],item['author'],item['tags'],item['born_date'],
                                        item['born_location']))
        # 提交，不进行提交无法保存到数据库
        self.conn.commit()


    def close_spider(self,spider):
        # 关闭游标和连接
        self.cursor.close()
        self.conn.close()
```

配置settings文件：

![img](https://img-blog.csdn.net/2018071613514631?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xvbmVyX2Zhbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 

**结果：**

 

**![img](https://img-blog.csdn.net/2018071613532658?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xvbmVyX2Zhbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)**

**【方法二：异步操作】pipelines.py文件（处理数据的python文件）**

通过twisted实现数据库异步插入，twisted模块提供了 twisted.enterprise.adbapi

　　1. 导入adbapi

　　2. 生成数据库连接池

　　3. 执行数据数据库插入操作

　　4. 打印错误信息，并排错

```python
import pymysql
from twisted.enterprise import adbapi



class MysqlPipelineTwo(object):
    def __init__(self,dbpool):
        self.dbpool = dbpool
    @classmethod
    def from_settings(cls,settings):  # 函数名固定，会被scrapy调用，直接可用settings的值
        """
        数据库建立连接
        :param settings: 配置参数
        :return: 实例化参数
        """
        adbparams = dict(
            host=settings['MYSQL_HOST'],
            db=settings['MYSQL_DBNAME'],
            user=settings['MYSQL_USER'],
            password=settings['MYSQL_PASSWORD'],
            cursorclass=pymysql.cursors.DictCursor  # 指定cursor类型
        )
        # 连接数据池ConnectionPool，使用pymysql或者Mysqldb连接
        dbpool = adbapi.ConnectionPool('pymysql',**adbparams)
        # 返回实例化参数
        return cls(dbpool) 


    def process_item(self,item,spider):
        """
        使用twisted将MySQL插入变成异步执行。通过连接池执行具体的sql操作，返回一个对象
        """
        query = self.dbpool.runInteraction(self.do_insert,item)  # 指定操作方法和操作数据
        # 添加异常处理
        query.addCallback(self.handle_error)  # 处理异常
        

    def do_insert(self,cursor,item):
        # 对数据库进行插入操作，并不需要commit，twisted会自动commit
        insert_sql = """
        insert into test_zxf(quote,author,tags,born_date,born_location) VALUES(%s,%s,%s,%s,%s)
                    """
        cursor.execute(insert_sql,(item['quote'],item['author'],item['tags'],item['born_date'],
                                        item['born_location'])) 



    def handle_error(self,failure):
        if failure:
            # 打印错误信息
            print(failure)
```

 

**6、所遇到的坑**

 

1、python 3.x 不再支持MySQLdb，它在py3的替代品是： import pymysql。

2、报错pymysql.err.ProgrammingError: (1064, ……

原因：当item['quotes']里面含有引号时，可能会报上述错误

解决办法：使用pymysql.escape_string()方法

例如：

```sql
sql = """INSERT INTO video_info(video_id, title) VALUES("%s","%s")""" % (video_info["id"],pymysql.escape_string(video_info["title"]))
```

3、存在中文的时候，连接需要添加charset='utf8'，否则中文显示乱码。

4、每执行一次爬虫，就会将数据追加到数据库中，如果多次的测试爬虫，就会导致相同的数据不断累积，怎么实现增量爬取？

- scrapy-deltafetch
- scrapy-crawl-once（与1不同的是存储的数据库不同）
- scrapy-redis
- scrapy-redis-bloomfilter(3的增强版，存储更多的url,查询更快)