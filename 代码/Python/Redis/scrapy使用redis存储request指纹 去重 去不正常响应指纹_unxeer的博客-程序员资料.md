## scrapy使用redis存储request指纹 去重 去不正常响应指纹

Scrapy==2.2.0

​    在使用scrapy时，遇到一个小问题。如果开启了去重持久化，不管返回结果是否正常，都会被记录request指纹。再次爬取该链接时都会被忽略，如果使用dont_dupefilter=True，又起不到去重的作用。

```bash
scrapy crawl somespider -s JOBDIR=crawls/somespider-1  # 加入持久化路径以开启去重
yield scrapy.Request(url, callback=self.parse, dont_dupefilter=True)  # 请求时不去重
```

​    归根结底，应该在开启去重的同时，在返回结果不正常时，将已经记录的request指纹删除。

​    此次持久化使用redis，scrapy默认是使用文件，上面的JOBDIR就是持久化文件路径。此次不使用scrapy-redis包，只使用了redis包用于链接数据库。**以下是增量代码**

​    

```python
# settings.py

DOWNLOADER_MIDDLEWARES = {
   '<project>.middlewares.<project>DownloaderMiddleware': 543,  # 开启下载中间件
}

DUPEFILTER_CLASS = '<project>.middlewares.DupefiltersMiddleware'  # 去重类，下面会定义
DUPEFILTER_DEBUG = True

REDISHOST = "127.0.0.1"
REDISPORT = "6379"
REDISNAME = "0"
```



```python
# middlewares.py

import redis
from scrapy.dupefilters import RFPDupeFilter  # 默认的去重类
from scrapy.utils.request import request_fingerprint  # 指纹方法

from <project>.settings import REDISHOST, REDISPORT, REDISNAME


class DupefiltersMiddleware(RFPDupeFilter):  # 定义去重类，继承自默认去重类
    def __init__(self, path=None, debug=False):
        pool = redis.ConnectionPool(host=REDISHOST, port=REDISPORT, db=REDISNAME)
        self.rd = redis.Redis(connection_pool=pool)
        super().__init__(path=path, debug=debug)

    def request_seen(self, request):
        fp = request_fingerprint(request)
        if self.rd.sismember('fp', fp):
            return True
        self.rd.sadd('fp', fp)

    @classmethod
    def remove_fp(cls, fp):
        cls().rd.srem('fp', fp)

# 原文件有此类，只需在process_response方法中加入判断
class <project>DownloaderMiddleware:
    def process_response(self, request, response, spider):
        # Called with the response returned from the downloader.
        if response.status > 400:
            fp = request_fingerprint(request)
            DupefiltersMiddleware.remove_fp(fp)  #删除已经记录的指纹
```