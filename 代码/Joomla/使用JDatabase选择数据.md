**使用****JDatabase****选择数据**

**注意版本**

注意许多在线使用的例子$db->query() 而不是$db->execute(). 这是Joomla 1.5和2.5中的旧方法，将在Joomla 3.0+中抛出一个弃用的通知。

本教程分为两个独立部分:

·     插入、更新和删除数据库中的数据。

·     从一个或多个表中选择数据并以各种不同的形式检索数据。

文档的这一部分着眼于从数据库表中选择数据并以各种格式检索数据。看另一部分。[点击这里](https://docs.joomla.org/Special:MyLanguage/Inserting,_Updating_and_Removing_data_using_JDatabase)

**目录**

 [[隐藏](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn)] 

·     [1 介绍](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#.E4.BB.8B.E7.BB.8D)

·     [2 查询](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#.E6.9F.A5.E8.AF.A2)

·     [3 从单个表中选择记录](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#.E4.BB.8E.E5.8D.95.E4.B8.AA.E8.A1.A8.E4.B8.AD.E9.80.89.E6.8B.A9.E8.AE.B0.E5.BD.95)

·     [4 从多个表中选择记录](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#.E4.BB.8E.E5.A4.9A.E4.B8.AA.E8.A1.A8.E4.B8.AD.E9.80.89.E6.8B.A9.E8.AE.B0.E5.BD.95)

·     [5 Using OR in queries](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#Using_OR_in_queries)

·     [6 查询结果](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#.E6.9F.A5.E8.AF.A2.E7.BB.93.E6.9E.9C)

o  [6.1 单值的结果](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#.E5.8D.95.E5.80.BC.E7.9A.84.E7.BB.93.E6.9E.9C)

§ [6.1.1 loadResult()](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#loadResult.28.29)

o  [6.2 单行结果](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#.E5.8D.95.E8.A1.8C.E7.BB.93.E6.9E.9C)

§ [6.2.1 loadRow()](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#loadRow.28.29)

§ [6.2.2 loadAssoc()](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#loadAssoc.28.29)

§ [6.2.3 loadObject()](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#loadObject.28.29)

o  [6.3 单行结果](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#.E5.8D.95.E8.A1.8C.E7.BB.93.E6.9E.9C_2)

§ [6.3.1 loadColumn()](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#loadColumn.28.29)

§ [6.3.2 loadColumn($index)](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#loadColumn.28.24index.29)

o  [6.4 多行结果](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#.E5.A4.9A.E8.A1.8C.E7.BB.93.E6.9E.9C)

§ [6.4.1 loadRowList()](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#loadRowList.28.29)

§ [6.4.2 loadAssocList()](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#loadAssocList.28.29)

§ [6.4.3 loadAssocList($key)](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#loadAssocList.28.24key.29)

§ [6.4.4 loadAssocList($key, $column)](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#loadAssocList.28.24key.2C_.24column.29)

§ [6.4.5 loadObjectList()](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#loadObjectList.28.29)

§ [6.4.6 loadObjectList($key)](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#loadObjectList.28.24key.29)

o  [6.5 其他结果集方法](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#.E5.85.B6.E4.BB.96.E7.BB.93.E6.9E.9C.E9.9B.86.E6.96.B9.E6.B3.95)

§ [6.5.1 getNumRows()](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#getNumRows.28.29)

·     [7 Sample Module Code](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#Sample_Module_Code)

o  [7.1 另请参阅](https://docs.joomla.org/Selecting_data_using_JDatabase/zh-cn#.E5.8F.A6.E8.AF.B7.E5.8F.82.E9.98.85)

**介绍**

Joomla提供了一个复杂的数据库抽象层，以简化第三方开发人员的使用。Joomla平台API的新版本提供了额外的功能，可以进一步扩展数据库层，并包括一些特性，例如连接器到更多种数据库服务器和查询链接，以提高连接代码的可读性和简化SQL编码。

Joomla可以使用不同类型的SQL数据库系统，并在不同的环境中运行不同的表前缀。除了这些函数之外，该类还自动创建数据库连接。除了实例化对象之外，还需要两行代码以各种格式从数据库获取结果。使用Joomla数据库层确保您的扩展具有最大的兼容性和灵活性。

**查询**

Joomla的数据库查询随着Joomla 1.6的引入而改变。构建数据库查询的建议方法是通过“查询链接”(尽管仍然支持字符串查询)。

查询链接是一种连接多种方法的方法，一个接一个，每个方法返回一个对象，该对象可以支持下一个方法，提高可读性和简化代码。

为了获得JDatabaseQuery类的新实例，我们使用JDatabaseDriver getQuery方法:

$db = JFactory::getDbo();

 

$query = $db->getQuery(**true**);

JDatabaseDriver::getQuery接受一个可选的参数,$new, 这可能是真或假(默认为假)。

要查询我们的数据源，我们可以调用一些JDatabaseQuery方法;这些方法封装了数据源的查询语言(在大多数情况下是SQL)，从开发人员那里隐藏了查询特定的语法，并提高了开发人员源代码的可移植性。

一些比较常用的方法包括;选择，从，连接，在哪里和顺序。还有一些方法，比如在数据存储中修改记录的插入、更新和删除。通过链接这些方法和其他方法调用，您可以在不影响代码的可移植性的情况下创建几乎所有针对数据存储的查询。.

**从单个表中选择记录**

下面是一个使用该方法创建数据库查询的示例 JDatabaseQuery 类。使用select、from、where和order方法，我们可以创建灵活、易读和可移植的查询:

*// Get a db connection.*

$db = JFactory::getDbo();

 

*// Create a new query object.*

$query = $db->getQuery(**true**);

 

*// Select all records from the user profile table where key begins with "custom.".*

*// Order it by the ordering field.*

$query->select($db->quoteName(**array**('user_id', 'profile_key', 'profile_value', 'ordering')));

$query->from($db->quoteName('#__user_profiles'));

$query->where($db->quoteName('profile_key') . ' LIKE ' . $db->quote('custom.%'));

$query->order('ordering ASC');

 

*// Reset the query using our newly populated query object.*

$db->setQuery($query);

 

*// Load the results as a list of stdClass objects (see later for more options on retrieving data).*

$results = $db->loadObjectList();

(Here the quoteName() function adds appropriate quotes around the column names to avoid conflicts with any database reserved word, now or in the future.)

该查询还可以被链接，以进一步简化:

$query

  ->select($db->quoteName(**array**('user_id', 'profile_key', 'profile_value', 'ordering')))

  ->from($db->quoteName('#__user_profiles'))

  ->where($db->quoteName('profile_key') . ' LIKE ' . $db->quote('custom.%'))

  ->order('ordering ASC');

当查询变得更长和更复杂时，链接将变得有用。

分组也可以简单地实现。下面的查询将计算每个类别中的文章数量。

$query

  ->select(**array**('catid', 'COUNT(*)'))

  ->from($db->quoteName('#__content'))

  ->group($db->quoteName('catid'));

可以使用“setLimit”设置一个查询的限制。例如，在以下查询中，它将返回多达10条记录。

$query

  ->select($db->quoteName(**array**('user_id', 'profile_key', 'profile_value', 'ordering')))

  ->from($db->quoteName('#__user_profiles'))

  ->setLimit('10');

 

**从多个表中选择记录**

使用JDatabaseQuery的 [join](http://api.joomla.org/11.4/Joomla-Platform/Database/JDatabaseQuery.html#join) 方法，我们可以从多个相关的表中选择记录。泛型“join”方法接受两个参数;连接“类型”(内、外、左、右)和连接条件。在下面的示例中，您将注意到，如果我们编写一个原生SQL查询，我们可以使用我们通常使用的所有关键字，包括作为别名表的关键字，以及用于创建表之间关系的ON关键字。还要注意，表别名用于所有引用表列(即select、where、order)的方法。

*// Get a db connection.*

$db = JFactory::getDbo();

 

*// Create a new query object.*

$query = $db->getQuery(**true**);

 

*// Select all articles for users who have a username which starts with 'a'.*

*// Order it by the created date.*

*// Note by putting 'a' as a second parameter will generate `#__content` AS `a`*

$query

  ->select(**array**('a.*', 'b.username', 'b.name'))

  ->from($db->quoteName('#__content', 'a'))

  ->join('INNER', $db->quoteName('#__users', 'b') . ' ON ' . $db->quoteName('a.created_by') . ' = ' . $db->quoteName('b.id'))

  ->where($db->quoteName('b.username') . ' LIKE ' . $db->quote('a%'))

  ->order($db->quoteName('a.created') . ' DESC');

 

*// Reset the query using our newly populated query object.*

$db->setQuery($query);

 

*// Load the results as a list of stdClass objects (see later for more options on retrieving data).*

$results = $db->loadObjectList();

上面的连接方法使我们能够查询内容和用户表，检索文章的作者详细信息。还有一些方便的连接方法:

·     [innerJoin()](http://api.joomla.org/cms-3/classes/JDatabaseQuery.html#method_innerJoin)

·     [leftJoin()](http://api.joomla.org/cms-3/classes/JDatabaseQuery.html#method_leftJoin)

·     [rightJoin()](http://api.joomla.org/cms-3/classes/JDatabaseQuery.html#method_rightJoin)

·     [outerJoin()](http://api.joomla.org/cms-3/classes/JDatabaseQuery.html#method_outerJoin)

我们可以使用多个连接来查询超过两个表:

$query

  ->select(**array**('a.*', 'b.username', 'b.name', 'c.*', 'd.*'))

  ->from($db->quoteName('#__content', 'a'))

  ->join('INNER', $db->quoteName('#__users', 'b') . ' ON ' . $db->quoteName('a.created_by') . ' = ' . $db->quoteName('b.id'))

  ->join('LEFT', $db->quoteName('#__user_profiles', 'c') . ' ON ' . $db->quoteName('b.id') . ' = ' . $db->quoteName('c.user_id'))

  ->join('RIGHT', $db->quoteName('#__categories', 'd') . ' ON ' . $db->quoteName('a.catid') . ' = ' . $db->quoteName('d.id'))

  ->where($db->quoteName('b.username') . ' LIKE ' . $db->quote('a%'))

  ->order($db->quoteName('a.created') . ' DESC');

请注意，链接是如何使源代码在这些较长的查询中更具可读性的。

在某些情况下，您还需要在选择项时使用AS子句，以避免列名称冲突。在这种情况下，可以将多个select语句与使用第二个参数连接起来$db->quoteName.

$query

  ->select('a.*')

  ->select($db->quoteName('b.username', 'username'))

  ->select($db->quoteName('b.name', 'name'))

  ->from($db->quoteName('#__content', 'a'))

  ->join('INNER', $db->quoteName('#__users', 'b') . ' ON ' . $db->quoteName('a.created_by') . ' = ' . $db->quoteName('b.id'))

  ->where($db->quoteName('b.username') . ' LIKE ' . $db->quote('a%'))

  ->order($db->quoteName('a.created') . ' DESC');

第二个数组也可以作为select语句的第二个参数来填充as子句的值。记住，在第二个数组中包含null，以对应第一个数组中的列，您不想使用AS子句:

$query

  ->select(**array**('a.*'))

  ->select($db->quoteName(**array**('b.username', 'b.name'), **array**('username', 'name')))

  ->from($db->quoteName('#__content', 'a'))

  ->join('INNER', $db->quoteName('#__users', 'b') . ' ON ' . $db->quoteName('a.created_by') . ' = ' . $db->quoteName('b.id'))

  ->where($db->quoteName('b.username') . ' LIKE ' . $db->quote('a%'))

  ->order($db->quoteName('a.created') . ' DESC');

**Using OR in queries**

When using multiple WHERE clauses in your query, they will be treated as an AND.

So, for example, the query below will return results where the 'name' field AND the 'state' field match.

$query = $db

  ->getQuery(**true**)

  ->select('COUNT(*)')

  ->from($db->quoteName('#__my_table'))

  ->where($db->quoteName('name') . " = " . $db->quote($value)

  ->where($db->quoteName('state') . " = " . $db->quote($state));

To use a WHERE clause as an OR, the query can be written like this

$query = $db

  ->getQuery(**true**)

  ->select('COUNT(*)')

  ->from($db->quoteName('#__my_table'))

  ->where($db->quoteName('name') . " = " . $db->quote($name_one), 'OR')

  ->where($db->quoteName('name') . " = " . $db->quote($name_two));

it can also be written like this

$query = $db

  ->getQuery(**true**)

  ->select('COUNT(*)')

  ->from($db->quoteName('#__my_table'))

  ->where($db->quoteName('name') . " = " . $db->quote($name_one)

  ->orWhere($db->quoteName('name') . " = " . $db->quote($name_two));

**查询结果**

数据库类包含许多处理查询结果集的方法。

If there are no matches to the query, the result will be null.

**单值的结果**

**loadResult()**

使用 **loadResult()** 当您期望从数据库查询返回单个值时。

| **id** | **name**          | **email**                | **username** |
| ------ | ----------------- | ------------------------ | ------------ |
| 1      | John  Smith       | johnsmith@domain.example | johnsmith    |
| 2      | Magda  Hellman    | magda_h@domain.example   | magdah       |
| 3      | Yvonne  de Gaulle | ydg@domain.example       | ydegaulle    |

这通常是“count”查询的结果，以获得一些记录:

$db = JFactory::getDbo();

$query = $db

  ->getQuery(**true**)

  ->select('COUNT(*)')

  ->from($db->quoteName('#__my_table'))

  ->where($db->quoteName('name') . " = " . $db->quote($value));

 

*// Reset the query using our newly populated query object.*

$db->setQuery($query);

$count = $db->loadResult();

或者，您只需要从表的单行中查找单个字段(或者从第一行返回的单个字段)。

$db = JFactory::getDbo();

$query = $db

  ->getQuery(**true**)

  ->select('field_name')

  ->from($db->quoteName('#__my_table'))

  ->where($db->quoteName('some_name') . " = " . $db->quote($some_value));

 

$db->setQuery($query);

$result = $db->loadResult();

**单行结果**

每个结果函数都将从数据库返回单个记录，尽管可能有几条记录符合您设置的标准。

| **id** | **name**          | **email**                | **username** |
| ------ | ----------------- | ------------------------ | ------------ |
| 1      | John  Smith       | johnsmith@domain.example | johnsmith    |
| 2      | Magda  Hellman    | magda_h@domain.example   | magdah       |
| 3      | Yvonne  de Gaulle | ydg@domain.example       | ydegaulle    |

**loadRow()**

loadRow() 从表中的单个记录返回一个索引数组:

. . .

$db->setQuery($query);

$row = $db->loadRow();

print_r($row);

将:

Array ( [0] => 1, [1] => John Smith, [2] => johnsmith@domain.example, [3] => johnsmith ) 

您可以通过以下方式访问单个值:

$row['index'] // e.g. $row['2']

注释:

\1.   数组索引是从0开始的数值。

\2.   虽然您可以重复调用以获得更多的行，但返回多行的函数之一可能更有用。

**loadAssoc()**

loadAssoc() 从表中的单个记录返回一个相关联的数组:

. . .

$db->setQuery($query);

$row = $db->loadAssoc();

print_r($row);

将:

Array ( [id] => 1, [name] => John Smith, [email] => johnsmith@domain.example, [username] => johnsmith )

您可以通过以下方式访问单个值:

$row['name'] // e.g. $row['email']

注释:

\1.   虽然您可以重复调用以获得更多的行，但返回多行的函数之一可能更有用。

**loadObject()**

loadObject从表中的单个记录返回一个PHP对象:

. . .

$db->setQuery($query);

$result = $db->loadObject();

print_r($result);

将:

stdClass Object ( [id] => 1, [name] => John Smith, [email] => johnsmith@domain.example, [username] => johnsmith )

您可以通过以下方式访问单个值:

$result->index // e.g. $result->email

注释:

\1.   虽然您可以重复调用以获得更多的行，但返回多行的函数之一可能更有用。

**单行结果**

每个结果函数都将从数据库返回单个列。

| **id** | **name**          | **email**                | **username** |
| ------ | ----------------- | ------------------------ | ------------ |
| 1      | John  Smith       | johnsmith@domain.example | johnsmith    |
| 2      | Magda  Hellman    | magda_h@domain.example   | magdah       |
| 3      | Yvonne  de Gaulle | ydg@domain.example       | ydegaulle    |

**loadColumn()**

loadColumn() 从表中的单个列返回一个索引数组:

$query->select('name'));

   ->from . . .";

. . .

**$db->setQuery**(**$query**);

**$column**= **$db->loadColumn**();

print_r(**$column**);

将:

Array ( [0] => John Smith, [1] => Magda Hellman, [2] => Yvonne de Gaulle )

您可以通过以下方式访问单个值:

$column['index'] // e.g. $column['2']

注释:

\1.   数组索引是从0开始的数值。

\2.   loadColumn() 相当于 loadColumn(0).

**loadColumn($index)**

loadColumn($index) 从表中的单个列返回一个索引数组:

$query->select(**array**('name', 'email', 'username'));

   ->from . . .";

. . .

**$db->setQuery**(**$query**);

**$column**= **$db->loadColumn**(1);

print_r(**$column**);

将:

Array ( [0] => johnsmith@domain.example, [1] => magda_h@domain.example, [2] => ydg@domain.example )

您可以通过以下方式访问单个值:

$column['index'] // e.g. $column['2']

loadColumn($index) 允许您遍历结果中的一系列。

. . .

$db->setQuery($query);

**for** ( $i = 0; $i <= 2; $i++ ) {

 $column= $db->loadColumn($i);

 print_r($column);

}

将:

Array ( [0] => John Smith, [1] => Magda Hellman, [2] => Yvonne de Gaulle ),

Array ( [0] => johnsmith@domain.example, [1] => magda_h@domain.example, [2] => ydg@domain.example ),

Array ( [0] => johnsmith, [1] => magdah, [2] => ydegaulle )

注释：

\1.   数组索引是从0开始的数值。

**多行结果**

每个结果函数都将从数据库返回多个记录。

| **id** | **name**          | **email**                | **username** |
| ------ | ----------------- | ------------------------ | ------------ |
| 1      | John  Smith       | johnsmith@domain.example | johnsmith    |
| 2      | Magda  Hellman    | magda_h@domain.example   | magdah       |
| 3      | Yvonne  de Gaulle | ydg@domain.example       | ydegaulle    |

**loadRowList()**

loadRowList() 从查询返回的表记录返回索引数组的索引数组:

. . .

$db->setQuery($query);

$row = $db->loadRowList();

print_r($row);

将给予(为了清晰起见，添加换行符):

Array ( 

[0] => Array ( [0] => 1, [1] => John Smith, [2] => johnsmith@domain.example, [3] => johnsmith ), 

[1] => Array ( [0] => 2, [1] => Magda Hellman, [2] => magda_h@domain.example, [3] => magdah ), 

[2] => Array ( [0] => 3, [1] => Yvonne de Gaulle, [2] => ydg@domain.example, [3] => ydegaulle ) 

)

您可以通过以下方式访问单个行:

$row['index'] // e.g. $row['2']

你可以通过使用来访问单个值：

$row['index']['index'] // e.g. $row['2']['3']

注释：

\1.   数组索引是从0开始的数值。

**loadAssocList()**

loadAssocList() 从查询返回的表记录返回关联数组的索引数组:

. . .

$db->setQuery($query);

$row = $db->loadAssocList();

print_r($row);

将给予(为了清晰起见，添加换行符):

Array ( 

[0] => Array ( [id] => 1, [name] => John Smith, [email] => johnsmith@domain.example, [username] => johnsmith ), 

[1] => Array ( [id] => 2, [name] => Magda Hellman, [email] => magda_h@domain.example, [username] => magdah ), 

[2] => Array ( [id] => 3, [name] => Yvonne de Gaulle, [email] => ydg@domain.example, [username] => ydegaulle ) 

) 

您可以通过以下方式访问单个行:

$row['index'] // e.g. $row['2']

你可以通过使用来访问单个值：

$row['index']['column_name'] // e.g. $row['2']['email']

**loadAssocList($key)**

loadAssocList('key') 返回一个关联数组 - indexed on 'key' - 由查询返回的表记录中的关联数组:

. . .

$db->setQuery($query);

$row = $db->loadAssocList('username');

print_r($row);

将给予(为了清晰起见，添加换行符):

Array ( 

[johnsmith] => Array ( [id] => 1, [name] => John Smith, [email] => johnsmith@domain.example, [username] => johnsmith ), 

[magdah] => Array ( [id] => 2, [name] => Magda Hellman, [email] => magda_h@domain.example, [username] => magdah ), 

[ydegaulle] => Array ( [id] => 3, [name] => Yvonne de Gaulle, [email] => ydg@domain.example, [username] => ydegaulle ) 

)

您可以通过以下方式访问单个行:

$row['key_value'] // e.g. $row['johnsmith']

你可以通过使用来访问单个值：

$row['key_value']['column_name'] // e.g. $row['johnsmith']['email']

注意:键必须是来自表的有效列名;它不必是索引或主键。但是如果它没有唯一的值，您可能无法可靠地检索结果。

**loadAssocList($key, $column)**

loadAssocList('key', 'column') 返回由查询返回的列“列”的“键”上的值的关联数组:

. . .

$db->setQuery($query);

$row = $db->loadAssocList('id', 'username');

print_r($row);

将给予(为了清晰起见，添加换行符):

Array ( 

[1] => John Smith, 

[2] => Magda Hellman, 

[3] => Yvonne de Gaulle,

)

注意:键必须是来自表的有效列名;它不必是索引或主键。但是如果它没有唯一的值，您可能无法可靠地检索结果。

**loadObjectList()**

loadObjectList() 从查询返回的表记录返回一个已索引的PHP对象数组:

. . .

$db->setQuery($query);

$row = $db->loadObjectList();

print_r($row);

将给予(为了清晰起见，添加换行符):

Array ( 

[0] => stdClass Object ( [id] => 1, [name] => John Smith, 

  [email] => johnsmith@domain.example, [username] => johnsmith ), 

[1] => stdClass Object ( [id] => 2, [name] => Magda Hellman, 

  [email] => magda_h@domain.example, [username] => magdah ), 

[2] => stdClass Object ( [id] => 3, [name] => Yvonne de Gaulle, 

  [email] => ydg@domain.example, [username] => ydegaulle ) 

)

您可以通过以下方式访问单个行:

$row['index'] // e.g. $row['2']

你可以通过使用来访问单个值：

$row['index']->name // e.g. $row['2']->email

**loadObjectList($key)**

loadObjectList('key') 返回一个关联数组 - indexed on 'key' - 查询返回的表记录中的对象:

. . .

$db->setQuery($query);

$row = $db->loadObjectList('username');

print_r($row);

将给予(为了清晰起见，添加换行符):

Array ( 

[johnsmith] => stdClass Object ( [id] => 1, [name] => John Smith, 

  [email] => johnsmith@domain.example, [username] => johnsmith ), 

[magdah] => stdClass Object ( [id] => 2, [name] => Magda Hellman, 

  [email] => magda_h@domain.example, [username] => magdah ), 

[ydegaulle] => stdClass Object ( [id] => 3, [name] => Yvonne de Gaulle, 

  [email] => ydg@domain.example, [username] => ydegaulle ) 

)

您可以通过以下方式访问单个行:

$row['key_value'] // e.g. $row['johnsmith']

你可以通过使用来访问单个值：

$row['key_value']->column_name // e.g. $row['johnsmith']->email

注意:键必须是来自表的有效列名;它不必是索引或主键。但是如果它没有唯一的值，您可能无法可靠地检索结果。

**其他结果集方法**

**getNumRows()**

getNumRows() 将返回最后一个SELECT或SHOW查询所发现的结果行数，并等待读取。得到结果 getNumRows() 你必须运行 **after** 查询和 **before** 您已经检索到任何结果。检索受影响的行数 INSERT, UPDATE, REPLACE 或 DELETE 查询,使用getAffectedRows().

. . .

$db->setQuery($query);

$db->execute();

$num_rows = $db->getNumRows();

print_r($num_rows);

$result = $db->loadRowList();

将返回

3

注意: getNumRows() 只对选择或显示返回实际结果集的语句有效 getNumRows() after loadRowList() - 或者任何其他的检索方法——您将得到一个PHP警告:

Warning: mysql_num_rows(): 80 is not a valid MySQL result resource 

in libraries\joomla\database\database\mysql.php on line 344

**Sample Module Code**

Below is the code for a simple Joomla module which you can install and run to demonstrate use of the JDatabase functionality, and which you can adapt to experiment with some of the concepts described above. If you are unsure about development and installing a Joomla module then following the tutorial at [Creating a simple module ](https://docs.joomla.org/Special:MyLanguage/J3.x:Creating_a_simple_module/Introduction)will help.

**Important note: In any Joomla extensions which you develop that you should avoid accessing the core Joomla tables directly like this and should instead use the Joomla APIs if at all possible, because the database structures may change without warning.**

In a folder mod_db_select create the following 2 files:

mod_db_select.xml

<?xml version="1.0" encoding="utf-8"?>

**<extension** type="module" version="3.1" client="site" method="upgrade"**>**

  **<name>**Database select query demo**</name>**

  **<version>**1.0.1**</version>**

  **<description>**Code demonstrating use of Joomla Database class to perform SQL SELECT queries**</description>**

  **<files>**

​    **<filename** module="mod_db_select"**>**mod_db_select.php**</filename>**

  **</files>**

**</extension>**

mod_db_select.php

<?php

defined('_JEXEC') **or** **die**('Restricted Access');

 

**use** Joomla\CMS\Factory;

 

$db = Factory::getDbo();

 

$me = Factory::getUser();

 

$query = $db->getQuery(**true**);

 

$query->select($db->quoteName(**array**('name', 'email')))

​     ->from($db->quoteName('#__users'))

​     ->where($db->quoteName('id') . ' != ' . $db->quote($me->id))

​     ->order($db->quoteName('name') . ' ASC');

 

$db->setQuery($query);

 

**echo** $db->replacePrefix((string) $query);

 

$results = $db->loadAssocList();

 

**foreach** ($results **as** $row) {

​     **echo** "<p>" . $row['name'] . ", " . $row['email'] . "<br></p>";

}

The code above selects and outputs the username and email of the records in the Joomla users table, apart from those of the currently logged-on user. The method Factory::getUser() returns the user object of the currently logged-on user, or if not logged on, then a blank user object, whose id field is set to zero.

The $db->replacePrefix((string) $query) expression returns the actual SQL statement, and outputting this can be useful in debugging.

Zip up the mod_db_select directory to create mod_db_select.zip.

Within your Joomla administrator go to Install Extensions and via the Upload Package File tab select this zip file to install this sample log module.

Make this module visible by editing it (click on it within the Modules page) then:

\1.   making its status Published

\2.   selecting a position on the page for it to be shown

\3.   on the menu assignment tab specify the pages it should appear on

When you visit a site web page then you should see the module in your selected position, and it should output the SQL SELECT statement and the sequence of name, email values from the Joomla users table.

**另请参阅**

·     [使用JDatabase插入、更新和删除数据。](https://docs.joomla.org/Special:MyLanguage/Inserting,_Updating_and_Removing_data_using_JDatabase)

·     [在数据库查询中使用union方法](https://docs.joomla.org/Special:MyLanguage/Using_the_union_methods_in_database_queries) (Joomla! 3.3+)

·     [Joomla CMS 3.8 API](https://api.joomla.org/cms-3/classes/JDatabaseQuery.html)

 