# Scrapy系列十二：把爬取到的数据保存到redis数据库

 

# 1.安装redis

pip install -U redis==2.10.6

 

# 2.配置数据库连接信息

在settings.py文件加入数据库连接，属性名没有规定可以随便起

![img](https://img-blog.csdnimg.cn/20200410153632383.png)

 

# 3.获取数据库配置，连接数据库

![img](https://img-blog.csdnimg.cn/20200410153710482.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjIyNjAz,size_16,color_FFFFFF,t_70)

 

# 4.执行数据库操作

![img](https://img-blog.csdnimg.cn/20200410153731337.png)

 

# 5.关闭客户端连接

![img](https://img-blog.csdnimg.cn/20200410153751507.png)

 

# 6.加入到数据清洗的管道

![img](https://img-blog.csdnimg.cn/20200410153811158.png)

 

# 完整代码

```ruby
class RedisPipeline(object):



    def open_spider(self,spider):



        #第一个参数是settings.py里的属性，第二个参数是获取不到值的时候的替代值



        host = spider.settings.get("REDIS_HOST","localhost")



        port = spider.settings.get("REDIS_PORT",6379)



        db_index = spider.settings.get("REDIS_DB_INDEX",0)



        db_psd = spider.settings.get("REDIS_PASSWORD","")



        #连接数据库



        self.db_conn = redis.StrictRedis(host=host,port=port,db=db_index,password=db_psd)



 



    def process_item(self, item, spider):



        # 将item转换成字典



        item_dict = dict(item)



        # 将数据插入到集合



        self.db_conn.rpush("novel",item_dict)



        return item



 



    def close_spider(self,spider):



        #关闭连接



        self.db_conn.connection_pool.disconnect()
```

 