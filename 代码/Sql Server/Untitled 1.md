# 如何实现SQL Server数据库的共享

SQL Server是本地电脑的数据库，要是其人想访问该电脑上数据库，就需要实现SQL Server的共享。

1、注册用户名，也就是对方使用的用户名

![img](https://img-blog.csdnimg.cn/20200518110158638.png)

2、设置登录名等信息

![img](https://img-blog.csdnimg.cn/20200518110338401.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FhYVBvc3RjYXJk,size_16,color_FFFFFF,t_70)

3、服务器角色

![img](https://img-blog.csdnimg.cn/20200518110512986.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FhYVBvc3RjYXJk,size_16,color_FFFFFF,t_70)

4、用户映射

![img](https://img-blog.csdnimg.cn/20200518110650911.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FhYVBvc3RjYXJk,size_16,color_FFFFFF,t_70)

5、状态

![img](https://img-blog.csdnimg.cn/20200518110742196.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FhYVBvc3RjYXJk,size_16,color_FFFFFF,t_70)

到这里，添加用户就已经完成了，然后

1、选择属性

![img](https://img-blog.csdnimg.cn/20200518111154217.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FhYVBvc3RjYXJk,size_16,color_FFFFFF,t_70)

2、安全性

![img](https://img-blog.csdnimg.cn/20200518111234789.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FhYVBvc3RjYXJk,size_16,color_FFFFFF,t_70)

这里有关SQL [Server](https://so.csdn.net/so/search?q=Server&spm=1001.2101.3001.7020)数据库配制就结束了，然后配制一下防火墙，把SQL Server加进去

1、控制面板找到[防火墙](https://so.csdn.net/so/search?q=防火墙&spm=1001.2101.3001.7020)

![img](https://img-blog.csdnimg.cn/20200518111612549.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FhYVBvc3RjYXJk,size_16,color_FFFFFF,t_70)

2、选择运行应用或功能通过。。。。。。

![img](https://img-blog.csdnimg.cn/2020051811165931.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FhYVBvc3RjYXJk,size_16,color_FFFFFF,t_70)

3、选择允许其他应用，浏览安装SQL Server的文件夹

4、搜索sqlservr，然后添加即可。

![img](https://img-blog.csdnimg.cn/20200518111913164.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FhYVBvc3RjYXJk,size_16,color_FFFFFF,t_70)

防火墙配制完毕，下面开始配制SQL Server配制管理器

1、我的电脑、右键管理，找到SQL Server服务，右键启动 SQL Server Browser

![img](https://img-blog.csdnimg.cn/20200518112220171.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FhYVBvc3RjYXJk,size_16,color_FFFFFF,t_70)

2、点击客户端协议，启动TCP/IP

![img](https://img-blog.csdnimg.cn/20200518112317996.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FhYVBvc3RjYXJk,size_16,color_FFFFFF,t_70)

3、找到SQL Server网络配制下面的的MSSQLSERVER协议，双击TCP/IP

![img](https://img-blog.csdnimg.cn/20200518112510134.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FhYVBvc3RjYXJk,size_16,color_FFFFFF,t_70)

4、点击IP地址，把所有TCP端口都改成1433，到此，配制完毕

然后在其他电脑上打开SQL Server之后，手动输入建立数据库电脑的服务器名称，然后选择SQL Server身份验证，登录名和密码就是上面设置的。

![img](https://img-blog.csdnimg.cn/20200518112922727.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FhYVBvc3RjYXJk,size_16,color_FFFFFF,t_70)