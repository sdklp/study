iframe.php 后台管理代码：
复制代码 代码以下:
/*这里放同用解决代码*/
switch($src)
{
case "top":
/*这里放解决代码*/
smarty_Output('top.html');
break;
case "menu":
/*这里放解决代码*/
smarty_Output('menu.html');
break;
case "main":
/*这里放解决代码*/
smarty_Output('main.html');
break;
default:
break;
}

iframe.html:
复制代码 代码以下:
<frame src="iframe.php?src=top" name="topFrame" id="topFrame" scrolling="no">
<frameset cols="180,*" name="btFrame" id="btFrame" frameborder="NO" border="0" framespacing="0">
<frame src="iframe.php?src=menu" id="leftbar" noresize name="menu" scrolling="yes">
<frame src="iframe.php?src=main" id="rightbar" noresize name="main" scrolling="yes">
</frameset>





另一种写法：

```php+HTML
public function index()
{
$top=$this->view->display('index.top.html');
$menu=$this->view->display('index.menu.html');
$main=$this->view->display('index.main.html');
$this->view->assign('frame',array(
'main'=>$main;
'top'=>$top;
'menu'=>$menu
));
$this->view->display('index.index.html')
}
```





第三种写法

可以做三个PHP文件，和三个模版
1.框架文件 用于生成框架的PHP文件，把框架的地址输出的框架模版

index.php

```
$smarty->assign($left,"left.php");
 $smarty->display("index.html");
```

 模版(index.html)：

```html
 <frameset cols="170,*" frameborder="no" border="0" framespacing="0" rows="*"> 
 <frame name="left" noresize scrolling="yes" src="{left}">
 <frameset rows="20,*" frameborder="no" border="0" framespacing="0" cols="*"> 
 <frame name="header" noresize scrolling="no" src="top.htm">
 <frame name="right" noresize scrolling="yes" src="right">
 </frameset>
 </frameset>
```

2.leftPHP文件 left.php

```php+HTML
 $smarty->assign($a,"ss");
 $smarty->display("left.html");
```

3.rightPHP文件 right.php

```php+HTML
 $smarty->assign($b,"xx");
 $smarty->display("right.html");
```

这样的话就把三个文件给分离出来，各自负责各自的，