# DevExpress18.1.5下载安装教程



## 前言

最近要参加一个开发比赛，需要做一个系统。现在界面一般都用DevExpress来搭建，好久之前用过一次，感觉界面比c#自带的要好看一百倍。今天把这次的一个安装过程记录一下。

## 文件下载

链接: [https://pan.baidu.com/s/19DQm9_APKu6SHnfpzuCU0A](https://link.zhihu.com/?target=https%3A//pan.baidu.com/s/19DQm9_APKu6SHnfpzuCU0A) 提取码: g97z

## 安装教程

0、安装之前首先确保已经关闭所有vs项目。

1、下载解压安装包，双击运行“DevExpressComponentsBundle-18.1.5.exe”进行安装。

![img](https://pic4.zhimg.com/80/v2-a0f646a523ee76c71c40ce6955257daf_720w.jpg)

2、弹出窗口，点击Trial Installation(试用安装)即可。

![img](https://pic4.zhimg.com/80/v2-42c5aebfce409fd5136f2d43a24a5ef3_720w.jpg)

3、进入安装界面，然后点击next下一步。

![img](https://pic1.zhimg.com/80/v2-ffab73bbc5efbbe6c6102c570db1c0c0_720w.jpg)

4、(自定义安装目录)，点击“Accept&Continu”开始安装。

![img](https://pic1.zhimg.com/80/v2-5bf88b3affaa7cfe198e08d9b794206c_720w.jpg)

5、选择Yes，然后选择Install进行安装。

![img](https://pic2.zhimg.com/80/v2-4452bb6685872c0fa49284d5dba0f581_720w.jpg)

6、正在安装中，有点缓慢请耐心等待。

![img](https://pic3.zhimg.com/80/v2-f90ee155752b530c2327078c4829bad2_720w.jpg)

7、安装成功，点击finish之后退出安装界面。

![img](https://pic2.zhimg.com/80/v2-56614c0ef97893a02ad050ce41bc8a75_720w.jpg)

8、打开下载好的pojie文件夹Crack 2，然后双击运行“DevExpress.Patch.exe”打开注册机,点击Apply patch进行注册。

![img](https://pic2.zhimg.com/80/v2-4da78c1d63372f1d191aa19a3f92af59_720w.png)

![img](https://pic3.zhimg.com/80/v2-eace6538eefb4ea9db0537dc1e4b3072_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-f4c30ac527ce85863f4d62ff46a5000f_720w.jpg)

9、打开vs2015，会发现vs会自动添加DevExpress的dll。

![img](https://pic1.zhimg.com/80/v2-691f634005c9cb1a70f698832228fa60_720w.jpg)

10、新建项目可以发现多了一个DevExpress v18.1 Template Gallery模板，选中它，自定义项目名称，点击确认。出现DevExpress Template Gallery对话框，可以在左边选择初始模板，然后单击Create Project，即可进入项目设计界面。

![img](https://pic2.zhimg.com/80/v2-185d9806408356e54f3dfc0f1e4f4ac5_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-21016ae39197cd9c1000414848763a4d_720w.jpg)

11、接下来我们在添加控件或者运行的时候，会弹出试用版提示框：

![img](https://pic4.zhimg.com/80/v2-c2d5049b70b60e1f1fb2a812cfdd68df_720w.jpg)

出现这个原因是license.licx这个控件凭证文件导致的，解决的方案有两个：

- 直接删除properties下的license.licx文件，重新编译，虽然会再生成，但不会再出现那个试用提示框。
- 如果想彻底删除这个文件，请右击license.licx文件》点击属性》把生成操作这属性改成“无”，再删除这个文件，就不会自动生成这个文件了。

![img](https://pic2.zhimg.com/80/v2-81608f25e240946d9fab8287ea225221_720w.jpg)

## 后记

OK，现在可以愉快的使用DevExpress了^-^