## HTML <frameset>不同frame之间传值

### 布局

左右30%--70%，点击左边的复选框，右边显示相应的反应。

![img](https://www.programminghunter.com/images/224/bc/bceab1d35350c252500e77d009c00670.png)

### 代码

main2.html

```
<html>

<frameset cols="30%, 70%">
    <frame id="leftfrm" src="left2.html">
    <frame id="rightfrm" src="right2.html">
</frameset>

</html>
```

left2.html

```
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" /> 

<script>
function myfunc()
{
    parent.frames["rightfrm"].document.getElementById("rightnode").innerHTML = "hello*****************";
}
</script>
</head>

<body>
<input type="checkbox" name="checkbox" value="plyer1" onclick="myfunc()">tiger_roads

</body>
</html>
```

right2.html

```
<html>
<body>
<div id="rightnode">hi</div>
</body>
</html>
```

### 效果

没点复选框之前

![img](https://www.programminghunter.com/images/799/70/70a1f90d17108f4ab3f5d71eed837c7f.png)

点之后

![img](https://www.programminghunter.com/images/456/20/2082049e473e378dc3f51e88ac00b568.png)

