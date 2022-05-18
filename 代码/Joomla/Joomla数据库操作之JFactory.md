# Joomla数据库操作之JFactory::getDBO用法



本文实例讲述了Joomla数据库操作之JFactory::getDBO用法。分享给大家供大家参考，具体如下：

JFactory 是一个静态类,用来获取各种系统对象的引用

getDBO为取得数据库对象的方法,取得数据库连接对象代码:

```
$db=& JFactory::getDBO();
```

有了数据库对象那么就可进行数据库操作了,执行查询代码:

```
<?php$db =& JFactory::getDBO();$query = 'SELECT FirstName FROM #tablename';$db->setQuery( $query );($db->query();//执行更改、添加、删除 )$Result = $db->loadObjectList();//得到数据集?>
```

得到数据集,输出看看:

```
<?php  foreach ($Result as $key=>$value)  {   echo $value->FirstName.',';  }?>
```