# VS2019下安装和破解DevExpress20.1的教程

> 我今天先给大家演示如何在VS2019下安装最新版的DevExpress并破解，我的VS版本是2019企业版，本教程同样适用于其它版本。

![vs](https://i0.hdslb.com/bfs/article/48320d994754a38c53be7cf6c52dc6d6a3525f3e.png@942w_234h_progressive.webp)图1-VS2019

**1.官网下载最新版DevExpress插件**

https://www.devexpress.com/products/net/controls/winforms/

![img](https://i0.hdslb.com/bfs/article/3fa5c28d054d2ac6cc0759d11a4b0b6a20f062d4.png@942w_431h_progressive.webp)图2-官网下载DevExpress插件

**2.打开下载好的安装程序进行安装**

2.1)点击“Trial Installation”按钮，如图3所示；

![img](https://i0.hdslb.com/bfs/article/6d1890965f51ebbb94e908ebaf8941a88ec284dc.png@942w_642h_progressive.webp)图3-安装步骤1

2.2)默认已勾选主流应用，直接点击”Next”按钮，如图4所示；

![img](https://i0.hdslb.com/bfs/article/cb621a4060b180f3c91beb3e4b383efd6ad92be2.png@942w_644h_progressive.webp)图4-安装步骤2

2.3）根据个人喜好选择默认路径或修改安装路径，点击”Accetp & Continu”按钮，如图5所示；

![img](https://i0.hdslb.com/bfs/article/6897940a3d2b03a1a01556007a6231f340746978.png@942w_650h_progressive.webp)图5-安装步骤3

2.4）选择”Yes”按钮（助人为乐），点击“Install”开始安装，如图6所示；

![img](https://i0.hdslb.com/bfs/article/7e0b0aea0bd240854177f3e02277868988f7fe6f.png@942w_647h_progressive.webp)图6-安装步骤4

2.5)如果安装时打开了VS，会提示关闭VS后再继续，如图7所示；

![img](https://i0.hdslb.com/bfs/article/0090c53c3519d3d6203541fdbeb51902b099d1a8.png@942w_641h_progressive.webp)图7-安装提示

2.6）安装结束，点击“Finish”,如图8所示。

![img](https://i0.hdslb.com/bfs/article/560fcaab5489eea14c58826906ec584254eb9cf0.png@942w_648h_progressive.webp)图8-安装结束

**3.下载DevExpress.Patch.8.exe破解程序**

链接：https://pan.baidu.com/s/1JCIODwKpiSDzQacRQ53-8Q
提取码：zakc

3.1）文件解压后执行DevExpress.Patch.8.exe，点击”Apply patch”按钮，如图9所示，破解过程需要几分钟时间，请耐心等待；

![img](https://i0.hdslb.com/bfs/article/2beae691ebcfd1d04a29e614ee82ea0533c63ecc.png@468w_203h_progressive.webp)图9-破解步骤1

3.2）破解成功，点击”I’m done.”按钮，如图10所示。

![img](https://i0.hdslb.com/bfs/article/146d03120ff7c4e95d6280be8018642c92032352.png@465w_204h_progressive.webp)图10-破解步骤2

4.打开VS2019工具箱右键勾选全部显示，查看有没有显示DX.20.1系列控件，如图11所示， 若没有显示，则执行步骤5注册。

![img](https://i0.hdslb.com/bfs/article/d6f966ebc9494b07d3c779b605d379c3c4532d2c.png@387w_900h_progressive.webp)图11-DX.20.1控件

**5.DevExpress插件注册**

5.1)找到DevExpress 20.1的安装目录，如图12所示；

![guide-11](https://i0.hdslb.com/bfs/article/a9a128ba41bf413b495887a6bbdbd73071da8fa1.png@893w_87h_progressive.webp)图12-DevExpress安装路径

5.2)把文件地址切换为cmd，如图13所示；

![guide-12](https://i0.hdslb.com/bfs/article/48320c151c9425c5abdbda0304e389ae4e3ef8e9.png@879w_80h_progressive.webp)图13-路径切换为cmd

5.3)执行以下注册命令，结束后再打开VS工具箱查看DX.20.1系列控件是否显示；

1. ToolboxCreator.exe /ini:toolboxcreator.ini

5.4）如果要删除，执行以下删除命令或在控制面板中删除，如图14所示；

1. ToolboxCreator.exe /ini:toolboxcreator.ini /remove

![guide-13](https://i0.hdslb.com/bfs/article/fbf9d667554d9acc6e9cd2e54a305150ff7ed20b.png@942w_42h_progressive.webp)图14-控制面板中的DevExpress

**6.消除控件弹框提示**

6.1）在解决方案管理器列表中找到 license.licx，右键属性将[生成操作]选项改为[无]，如图15所示；

![img](https://i0.hdslb.com/bfs/article/1c0b77bb264fe838b7b9e958f2f7d2363e9f14e2.png@380w_866h_progressive.webp)图15-修改生成操作为”无”

6.2）这样就可以了，再次打开VS项目 时不会再弹出版权提示。