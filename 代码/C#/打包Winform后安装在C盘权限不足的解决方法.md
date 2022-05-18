# 【C#】 打包Winform后安装在C盘权限不足的解决方法

打包安装到C盘，打开快捷方式，提示对路径***的访问被拒绝

解决方法：

（1）VS中右键项目=》属性=》安全性，勾选【启用ClickOne安全设置】；

（2）找到app.manifest，将

```xml
<requestedExecutionLevel level="asInvoker" uiAccess="false" />
```

修改为

```xml
<requestedExecutionLevel level="requireAdministrator" uiAccess="false" />
```

（3）再次找到项目属性的安全性，去掉【启用ClickOne安全设置】的勾选；

（4）保存后编译，再次打包，即可在Win7+的C盘中安装后正常运行。