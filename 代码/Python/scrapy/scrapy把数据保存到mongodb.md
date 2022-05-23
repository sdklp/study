# 第一步，settings.py添加

```makefile
ITEM_PIPELINES = {
   # 'scrapy_runklist.pipelines.ScrapyRunklistPipeline': 300,
   'scrapy_runklist.pipelines.ScrapyWeiBoPipeline': 300,
}

# mongodb配置
MONGO_HOST = "127.0.0.1"  # 主机IP
MONGO_PORT = 27017  # 端口号
MONGO_DB = "ranklist"  # 库名
MONGO_COLL_WEIBO = "weibo"  # collection名
# MONGO_USER = "simple" #用户名
# MONGO_PSW = "test" #用户密码
```

# 第二步，item.py添加

```haskell
class WeiboItem(scrapy.Item):
    id = scrapy.Field()
    word = scrapy.Field()
    url = scrapy.Field()
```

# 第三步，spider.py添加

```dart
    def parse(self, response):
        json_data = response.json()
        num = 0
        for i in json_data["data"]["realtime"]:
            weiboItem = WeiboItem()
            if num == 10:
                break
            weiboItem["id"] = num
            weiboItem["word"] = i["word"]
            url = "https://s.weibo.com/weibo?q=%23{}%23".format(i["word"])
            weiboItem["url"] = url
            num += 1
            yield weiboItem
```

# 第四步，pipline.py添加

```python
import pymongo
from scrapy.utils.project import get_project_settings
settings = get_project_settings()

class ScrapyWeiBoPipeline:

    def __init__(self):
        # 链接数据库
        client = pymongo.MongoClient(host=settings['MONGO_HOST'], port=settings['MONGO_PORT'])
        self.db = client[settings['MONGO_DB']]  # 获得数据库的句柄
        self.coll = self.db[settings['MONGO_COLL_WEIBO']]  # 获得collection的句柄
        # 数据库登录需要帐号密码的话
        # self.db.authenticate(settings['MONGO_USER'], settings['MONGO_PSW'])

    def process_item(self, item, spider):
        print("pipline item ==== ", item)
        postItem = dict(item)  # 把item转化成字典形式
        self.coll.insert(postItem)  # 向数据库插入一条记录
        return item  # 会在控制台输出原item数据，可以选择不写
```