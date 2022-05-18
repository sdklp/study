# 文章一、将高版本MSSQL数据库恢复到低版本数据库的方法



有时候我们需要将MS SQL SERVER数据库的数据从高版本恢复到低版本，因为MS SQL Server的备份功能并不保持向下兼容，所以通过在高版本数据库系统生成备份然后还原备份文件到低版本数据库系统的办法（从低版本到高版本可以用这个办法）是不能成功的，但还是有其它几个办法如下：

1. 数据导入导出，这个就没什么好说了，很简单，可以在线也可以通过数据文件进行离线导入导出。

2. 通过脚本，操作步骤如下：
   a. 打开SSMS即Microsoft SQL Server Management Studio连接要转移的数据库，在数据库上点右键然后在弹出菜单上依次点任务Task、生成脚本Generate Scripts；

   ![img](https://upload-images.jianshu.io/upload_images/14575015-dc59ffac3609cbed.png?imageMogr2/auto-orient/strip|imageView2/2/w/729/format/webp)

   弹出菜单

   b. 在弹出的下图的界面上点下一步Next按钮（注意某些系统中可能因为被关闭所以本功能介绍页面不会显示）；

   ![img](https://upload-images.jianshu.io/upload_images/14575015-03566a06e765e62d.png?imageMogr2/auto-orient/strip|imageView2/2/w/729/format/webp)

   功能介绍界面

   c. 如果我们需要把该数据库所有内容都恢复到新的数据库系统上去，那么我们直接选上面这个“为所有数据库和其数据库对象创建脚本Script entire database and all database objects”，如果只需要部分的话，选下面的选项指定需要操作的内容就好了，这里我们选所有内容如图，然后点下一步Next按钮；

   ![img](https://upload-images.jianshu.io/upload_images/14575015-e19e1b784df89663.png?imageMogr2/auto-orient/strip|imageView2/2/w/729/format/webp)

   选择需要操作的对象

   d. 这里我们选择将脚本生成到一个新的查询窗口Save to new query window，当然也可以选其它选项直接生成文件或者生成到剪贴板甚至直接发布到Web服务。点击按钮高级Advanced进行更高级的设定。

   ![img](https://upload-images.jianshu.io/upload_images/14575015-3be74aba64e1d26c.png?imageMogr2/auto-orient/strip|imageView2/2/w/729/format/webp)

   设置生成脚本的选项

   e.在高级脚本选项对话框中指定脚本支持的目标版本“为服务器版本编写脚本Script for Server Version”，根据需要指定脚本里包含的内容“要编写脚本的数据类型Types of data to script”这里可选“仅架构Schema ”、“仅数据Data Only”和“架构和数据Schema and data”，这里我们选第三个然后点确定OK按钮。

   ![img](https://upload-images.jianshu.io/upload_images/14575015-60b5a1a22547e356.png?imageMogr2/auto-orient/strip|imageView2/2/w/479/format/webp)

   设定目标版本和要处理的内容

   f. 回到第d步的界面按下一步Next，审视一下所有选项是否正确，如果正确就点下一步Next按钮

   ![img](https://upload-images.jianshu.io/upload_images/14575015-0e07186d78b278bd.png?imageMogr2/auto-orient/strip|imageView2/2/w/729/format/webp)

   审视一下选项

   g. 在结果页如果无异常，点击完成Finish按钮就得到了我们需要的查询了。

   ![img](https://upload-images.jianshu.io/upload_images/14575015-f96fa37b689fe822.png?imageMogr2/auto-orient/strip|imageView2/2/w/729/format/webp)

   无异常就按下完成Finish按钮吧

   h.得到了需要的脚本文件，保存下来对目标数据库系统上跑一跑，需要的东西就都有了。

   ![img](https://upload-images.jianshu.io/upload_images/14575015-c5ecdcf2a84e27d1.png?imageMogr2/auto-orient/strip|imageView2/2/w/340/format/webp)

   这就是我们要的



# 文章二

## 如何在SQL Server中高版本向低版本导入数据？

​    在我们日常系统迁移中，经常出现新系统的数据库版本比生产环境的数据库版本要低，必然会出现默认备份的数据无法导入到低版本数据库中，就SQL Server而言，我们需要在导出数据库脚本的时候就需要进行相应设置，按如下步骤导出生产环境的数据：任务—》生成脚本—》，下一步选择"高级"，选择数据库版本和编写脚本数据类型为架构和数据，可以保留数据。选择当前要导入的低版本的数据库脚本，在服务器版本中进行设置，然后点击下一步进行导出，等导出完成后，把脚本拷贝到需要导入数据的服务器上。

![如何在SQL Server中高版本向低版本导入数据？_数据](https://s8.51cto.com/images/blog/202107/23/47853bf4d1c0d4293f2d1e68d5458497.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

 

 

​    在服务器上打开CMD窗口，允许命令  osql -S 主机名 -U 用户名 -P 密码 -i E:\20190723.sql，运行完成后，完美导入成功。

 

**注意**：我测试的是从SQL Server2019向2012中迁移数据。如果其他版本出现问题，可以在评论中回复，我会完善上面的步骤



# 文章三

Sqlserver高版本还原到低版本方法（Sqlserver2012到SqlServer2008 R2）
低版本的sqlserver数据库备份文件是能直接还原到高版本的sqlserver数据库中的。然而将高版本的数据库文件还原到低版本中，就会报如下错误：


那应该如何解决呢？以sqlserver2012 和 sqlserver2008 r2为例
一、给sqlserver2012数据库设置兼容
1、trasen_nurse_base数据库上右键，选择属性，点击选项


2、选择兼容级别为SQL Server 2008 (100)


二、Sqlserver2012 导出sql脚本
1、trasen_nurse_base数据库上右键，选择任务，点击生成脚本


2、点击下一步，直到设置脚本编写选项


3、点击高级，设置Script for Server Version为SQL Server 2008 R2


4、设置数据类型为 架构和数据


三、导入sql脚本到 SQL Server 2008 R2中
1、打开sql脚本，可批量修改需要导入的数据库的名字，以及数据库文件地址


2、新建查询，复制sql，执行sql，执行成功，数据库创建完毕



