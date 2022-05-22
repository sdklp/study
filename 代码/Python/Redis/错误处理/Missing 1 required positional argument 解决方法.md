# Missing 1 required positional argument 解决方法

用scrapy写爬虫异步存mysql数据库的时候遇到了这个问题，找了一天也没找到解决方法，后来发现是没有加修饰符。

![img](https://img-blog.csdnimg.cn/2019072418313030.png)

![img](https://img-blog.csdnimg.cn/20190724182902669.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NlcmlzZV9f,size_16,color_FFFFFF,t_70)

```
 
刚开始在借鉴别人方法的时候没有敲@classmethod这个修饰符，一直从外部找解决方法，重新装python还有库都没有用，最后加上修饰符就跑通了。百度到了这个修饰符的作用：
```

1、@classmethod声明一个类方法，而对于平常我们见到的则叫做实例方法。

2、类方法的第一个参数cls（class的缩写，指这个类本身），而实例方法的第一个参数是self，表示该类的一个实例

3、可以通过类来调用，就像C.f()，相当于java中的静态方法'''

![img](https://img-blog.csdnimg.cn/20190724183456628.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NlcmlzZV9f,size_16,color_FFFFFF,t_70)

修改后的代码如下

![img](https://img-blog.csdnimg.cn/20190724183539202.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NlcmlzZV9f,size_16,color_FFFFFF,t_70)