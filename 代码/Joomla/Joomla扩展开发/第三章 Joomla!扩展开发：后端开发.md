# 第三章 Joomla!扩展开发：后端开发

创建和管理评论是我们组件的最大任务。我们会加入表单和数据库函数，然后就可以添加评论。

创建数据表

在建立界面来输入评论前，我们需要创建评论的数据表：

CREATE TABLE 'jos_reviews'

(

'id' int(11) NOT NULL auto_increment,

'name' varchar(255) NOT NULL,

'address' varchar(255) NOT NULL,

'reservations' varchar(31) NOT NULL,

'quicktake' text NOT NULL,

'review' text NOT NULL,

'notes' text NOT NULL,

'smoking' tinyint(1) unsigned NOT NULL default '0',

'credit_cards' varchar(255) NOT NULL,

'cuisine' varchar(31) NOT NULL,

'avg_dinner_price' tinyint(3) unsigned NOT NULL default '0',

'review_date' datetime NOT NULL,

'published' tinyint(1) unsigned NOT NULL default '0',

PRIMARY KEY ('id')

);


创建数据表类

我们能够编写独立的函数来处理评论数据的添加、更新和删除。我想这些功能你都不想重复地编写。幸运地是，Joomla!团队已经为你做了。JTable 这个类提供了处理单个数据表的记录的创建、读取、更新和删除操作。要利用JTable，我们需要写一个指定到jos_reviews表的扩展。在administrator/components/com_reviews 文件夹下，创建一个tables文件夹，然后在里面创建 review.php 文件并输入一下代码：

<?php

defined('_JEXEC') or die('Restricted access');

class TableReview extends JTable

{

var $id = null;

var $name = null;

var $address = null;

var $reservations = null;

var $quicktake = null;

var $review = null;

var $notes = null;

var $smoking = null;

var $credit_cards = null;

var $cuisine = null;

var $avg_dinner_price = null;

var $review_date = null;

var $published = null;

function __construct(&$db)

{

  parent::__construct( '#__reviews', 'id', $db );

}

}

?>

我们继承了JTable类，并加入数据表的所有字段作为类的成员变量，成员变量都初始化为null。然后覆盖类的构造函数 __construct() ，__construct() 会带有一个数据库对象为参数，并调用父类的构造函数，以数据表名（以#__为前缀）、主键和数据库对象为参数值。

说明：

为什么要使用 #__ 为数据表的前缀？

在Joomla!编写查询和定义JTable 扩展时，使用 #__ 代替 jos_。Joomla! 执行查询时会自动将 #__ 替换为 管理员选择的数据库前缀。这样的好处是可以在同一个数据库中运行多套Joomla!。你随便修改数据库的前缀也不用修改代码。

 

​    TableReview 类继承了 bing()、store()、load() 和 delete() 等函数，这四个函数可以让你不用写一行的SQL 就可以管理数据库的记录。

 

创建评论表单

​    创建好了数据表，我们需要有一个友好的界面来增加评论。第一步，然我们创建一个表单来输入数据，我们想从逻辑中分离HTML，配置表单的必要代码会写在 admin.reviews.php中，admin.reviews.html.php中则包含实际的HTML代码。打开admin.reivews.php，用以下的代码替换原来的内容：

<?php

defined( '_JEXEC' ) or die( 'Restricted access' );

require_once( JApplicationHelper::getPath( 'admin_html' ) );

JTable::addIncludePath(JPATH_COMPONENT.DS.'tables');

switch($task)

{

case 'add':

editReview( $option );

  break;

}

function editReview( $option )

{

$row =& JTable::getInstance('Review', 'Table');

$lists = array();

$reservations = array(

  '0' => array('value' => 'None Taken',

​      'text' => 'None Taken'),

  '1' => array('value' => 'Accepted',

​      'text' => 'Accepted'),

  '2' => array('value' => 'Suggested',

​      'text' => 'Suggested'),

  '3' => array('value' => 'Required',

​      'text' => 'Required'),

);

$lists['reservations'] = JHTML::_('select.genericList',

$reservations, 'reservations', 'class="inputbox" '. '', 'value',

​                    'text', $row->reservations );

$lists['smoking'] = JHTML::_('select.booleanlist', 'smoking',

​               'class="inputbox"', $row->smoking);

$lists['published'] = JHTML::_('select.booleanlist', 'published',

​              'class="inputbox"', $row->published);

HTML_reviews::editReview($row, $lists, $option);

}

?>

​    我们使用require_once( JApplicationHelper::getPath( 'admin_html' ) ) 来包含admin.reviews.html.php文件。getPath() 函数带一个字符串参数并返回与组件文件一致的绝对路径。尽管我们没有指定组件名，但是它会自动包含适当的文件，即使是改变了组件名也一样。使用require_once() 确保文件只被包含一次。

​    addIncludePath() 成员函数会包含我们的数据表类，addIncludePath() 会自动包含所有我们定义在tables目录下的数据表类，太强大了，是吧？文件名和路径的构建都是跨平台兼容的。Joomla!设置JPATH_COMPONENT 为后端代码的绝对路径。DS常量是指定的操作系统的目录分隔符。switch() 语句检查 $task 变量，然后基于它的值来运行适当的函数。最后，editReview() 函数准备了一些HMTL元素然后传给显示函数 HTML_reviews::editReview()。

​    现在我们创建 admin.reviews.html.php文件并加入以下代码：

<?phpdefined( '_JEXEC' ) or die( 'Restricted access' );class HTML_reviews{function editReview( $row, $lists, $option ){    $editor =& JFactory::getEditor();    JHTML::_('behavior.calendar');    ?>    <form action="index.php" method="post"                  name="adminForm" id="adminForm">      <fieldset class="adminform">        <legend>Details</legend>        <table class="admintable">        <tr>          <td width="100" align="right" class="key">            Name:          </td>          <td>            <input class="text_area" type="text" name="name"                id="name" size="50" maxlength="250"                value="<?php echo $row->name;?>" />          </td>        </tr>        <tr>          <td width="100" align="right" class="key">            Address:          </td>          <td>            <input class="text_area" type="text" name="address"                id="address" size="50" maxlength="250"                value="<?php echo $row->address;?>" />          </td>        </tr>        <tr>          <td width="100" align="right" class="key">            Reservations:          </td>          <td>            <?php            echo $lists['reservations'];Chapter 3            ?>          </td>        </tr>        <tr>          <td width="100" align="right" class="key">            Quicktake:          </td>          <td>            <?php            echo $editor->display( 'quicktake', $row->quicktake ,                                       '100%', '150', '40', '5' ) ;            ?>          </td>        </tr>        <tr>          <td width="100" align="right" class="key">            Review:          </td>          <td>            <?php            echo $editor->display( 'review', $row->review ,                                      '100%', '250', '40', '10' ) ;            ?>          </td>        </tr>         <tr>          <td width="100" align="right" class="key">            Notes:          </td>          <td>            <textarea class="text_area" cols="20" rows="4"                name="notes" id="notes" style="width:500px"><?php echo                $row->notes; ?></textarea>          </td>        </tr>        <tr>          <td width="100" align="right" class="key">            Smoking:          </td>          <td>            <?php            echo $lists['smoking'];            ?>Back-End Development          </td>        </tr>        <tr>          <td width="100" align="right" class="key">            Credit Cards:          </td>          <td>            <input class="text_area" type="text" name="credit_cards"                id="credit_cards" size="50" maxlength="250"                value="<?php echo $row->credit_cards;?>" />          </td>        </tr>        <tr>          <td width="100" align="right" class="key">            Cuisine:          </td>          <td>            <input class="text_area" type="text" name="cuisine"                id="cuisine" size="31" maxlength="31"                value="<?php echo $row->cuisine;?>" />          </td>        </tr>        <tr>          <td width="100" align="right" class="key">            Average Dinner Price:          </td>          <td>            $<input class="text_area" type="text"                  name="avg_dinner_price"                  id="avg_dinner_price" size="5" maxlength="3"                  value="<?php echo $row->avg_dinner_price;?>" />          </td>        </tr>        <tr>          <td width="100" align="right" class="key">            Review Date:          </td>          <td>            <input class="inputbox" type="text" name="review_date"                id="review_date" size="25" maxlength="19"                value="<?php echo $row->review_date; ?>" />Chapter 3            <input type="reset" class="button" value="..."                onclick="return showCalendar('review_date',                'y-mm-dd');" />          </td>        </tr>        <tr>          <td width="100" align="right" class="key">            Published:          </td>          <td>            <?php            echo $lists['published'];            ?>          </td>        </tr>        </table>      </fieldset>      <input type="hidden" name="id"         value="<?php echo $row->id; ?>" />      <input type="hidden" name="option"         value="<?php echo $option;?>" />      <input type="hidden" name="task"         value="" />    </form>    <?php}}?>在浏览器地址栏输入http://localhost/joomla/administrator/index.

php?option=com_reviews&task=add，然后你会看到：

**暂时不提供图片显示，请参考《Joomla! extension development》**

**
**

我们的 editReview() 函数根据传过来的数据表行对象，结合了HTML来输出内容。所以editReview() 函数总是用来显示外观，输出内容之前函数包含了一组帮助函数来增强UI元素。


说明：

JHTML::_() 做了什么？

Joomla!提供了很多自动生成HTML元素的函数，如下拉列表、复选框等。为了提供执行的效率，这些函数只有在需要的时候才会读到内存里。这个工作有 _() 函数来完成。


首先，JFactory::getEditor() 函数返回 HTML 编辑器，JHTML::_(‘behavior.calendar’) 函数会在header 中加入JavaScript 和 CSS ，这是用在评论日期字段中弹出日历的代码：

class HTML_reviews

{

function editReview( $row, $lists, $option )

{

  $editor =& JFactory::getEditor();

  JHTML::_('behavior.calendar');


编辑器对象的成员函数display() 返回选择的富文本编辑器的HTML ，如果富文本编辑器不存在就返回<textarea> 元素。

​      <td>

​       <?php

​      echo $editor->display( 'quicktake', $row->quicktake ,

​        '100%', '150', '40', '5' ) ;

​      ?>

​     </td>

display() 函数带有以下的参数：表单变量名、值、宽、高、列数和行数。当没有使用HTML编辑器，最后两个参数是 <textarea> 的大小。


处理数据

管理员填完表单并且当即保存按钮后，我们需要保存信息到数据库里。开始，在admin.reviews.php中创建 saveReview() 函数：

function saveReview( $option )

{

global $mainframe;

$row =& JTable::getInstance('review', 'Table');

if (!$row->bind(JRequest::get('post')))

{

echo "<script> alert('".$row->getError()."');

window.history.go(-1); </script>\n";

exit();

}

$row->quicktake = JRequest::getVar( 'quicktake', '', 'post',

'string', JREQUEST_ALLOWRAW );

$row->review = JRequest::getVar( 'review', '', 'post',

'string', JREQUEST_ALLOWRAW );

if(!$row->review_date)

$row->review_date = date( 'Y-m-d H:i:s' );

if (!$row->store())

{

echo "<script> alert('".$row->getError()."');

window.history.go(-1); </script>\n";

exit();

}

$mainframe->redirect('index.php?option=' .

$option, 'Review Saved');

}


  首先，将全局变量 $mainframe 传进来， $mainframe 对象提供很多成员函数来控制 session 变量和 headers。然后将 TableReview 类的一个实例赋值给 $row ，类的名字由第一个参数和第二个参数组合而成，第二个参数是第一个参数的前缀。第二步，使用bind() 成员函数来加载表单中所有变量到 $row 中。

bind() 函数传一个关联数组参数并且要数组的所有元素都要和对象的成员变量完全匹配。为了减少SQL注入的风险，我们使用 Jrequest::get() 来清除 $_POST 的值，这个过程会过滤掉所有能够控制SQL的字符。

​    如果 bind() 失败了会弹出一个JavaScript的警告对话框并返回到前一个页面。绑定后就可以直接操作 $row 的成员变量。既然 quicktake 和 review 字段都接受HTML内容，那么它们需要对 bind() 函数进行清除 HTML 的特殊处理。要做这样处理，可以使用 Jrequest 的成员函数 getVar() 并传递表单的变量名、默认值、请求的数组、期望的格式和各自JREQUEST_ALLOWRAW标识。以防评论没有选择日期，我们赋了当前日期给评论日期。

最后，调用 store() 函数，把所有的成员变量都转化成 UPDATE 和 INSERT 语句（由id的值决定是UPDATE还是INSERT）。因为是第一次创建记录，id没有值，所以会构建INSERT 查询语句。如果有SQL错误就返回上一页，通常这一类的SQL错误都是由于 $row 额外的成员变量而没有在数据表类中引起的。那么如果发现有SQL错误，第一时间就是要检查确保你的成员变量的拼写要与数据表的列一致。否则，如果SQL执行成功，将使用 $mainframe 的redirect() 函数返回组件的页面。

此时，admin.review.php 中的 switch() 语句只是执行添加任务。既然我们已经有了表单和函数，那么添加一个分支来保存我们的数据。添加以下粗体的代码：

switch($task)

{

case 'add':

editReview( $option );

  break;

**case 'save':**

**saveReview( $option );**

  **break;**

}

保存文件后访问这个地址：http://localhost/joomla/administrator/index.

php?option=com_reviews&task=add

你填好表单后点击保存，你能看到类似以下的页面：

**暂时不提供图片显示，请参考《Joomla! extension development》**

说明：

为什么我们不能点击“新建”按钮？

工具栏的按钮需要有名字为 adminForm 的表单才能有效，既然现在没有表单，那么点击任何的按钮都产生JavaScript错误的。当你加上 adminForm 表单后，按钮马上就生效了。

如果一切正常，那么你可以在 phpMyAdmin 中找到类似以下的数据：

**暂时不提供图片显示，请参考《Joomla! extension development》**


创建列表

既然我们的管理员不会有访问phpMyAdmin 的权限，我们需要创建显示评论的列表。开始我们在admin.reviews.php中添加以下函数：

function showReviews( $option )

{

$db =& JFactory::getDBO();

$query = "SELECT * FROM #__reviews";

$db->setQuery( $query );

$rows = $db->loadObjectList();

if ($db->getErrorNum()) {

   echo $db->stderr();

   return false;

}

HTML_reviews::showReviews( $option, $rows );

}

这个函数加载了将被显示的数据，我们得到了一个当前数据库连接的引用，然后调用它的成员函数 setQuery() ，setQuery() 函数带一个 SQL 语句的字符串为参数，但只做存储之后使用而不是立即执行。当调用 loadObjectList() 函数，之前设置的SQL 语句就会执行并返回记录到一个数组中。如果运行过程出现错误，那么将显示错误和停止组件运行。

如果一切正常，那么把记录结果的数组传给 admin.reviews.htlm.php 中的成员函数 showReviews()，如下：

function showReviews( $option, &$rows )

{

?>

<form action="index.php" method="post" name="adminForm">

<table class="adminlist">

  <thead>

   <tr>

​    <th width="20">

​     <input type="checkbox" name="toggle"

​        value="" onclick="checkAll(<?php echo

​        count( $rows ); ?>);" />

​    </th>

​    <th class="title">Name</th>

​    <th width="15%">Address</th>

​    <th width="10%">Reservations</th>

​    <th width="10%">Cuisine</th>

​    <th width="10%">Credit Cards</th>

​    <th width="5%" nowrap="nowrap">Published</th>

   </tr>

  </thead>

  <?php

  $k = 0;

  for ($i=0, $n=count( $rows ); $i < $n; $i++)

  {

   $row = &$rows[$i];

   $checked = JHTML::_('grid.id', $i, $row->id );

   $published = JHTML::_('grid.published', $row, $i );

   ?>

   <tr class="<?php echo "row$k"; ?>">

​    <td>

​     <?php echo $checked; ?>

​    </td>

​    <td>

​     <?php echo $row->name; ?>

​    </td>

​    <td>

​     <?php echo $row->address; ?>

​    </td>

​    <td>

​     <?php echo $row->reservations; ?>

​    </td>

​    <td>

​     <?php echo $row->cuisine; ?>

​    </td>

​    <td>

​     <?php echo $row->credit_cards; ?>

​    </td>

​    <td align="center">

​     <?php echo $published;?>

​    </td>

   </tr>

   <?php

   $k = 1 - $k;

  }

  ?>

</table>

<input type="hidden" name="option"

​          value="<?php echo $option;?>" />

<input type="hidden" name="task" value="" />

<input type="hidden" name="boxchecked" value="0" />

</form>

<?php

}





这个函数定义了一个名为 adminForm（作为JavaScript应用） 并指向 index.php 的表单，接着显示一个带有 adminlist 类的表格，第一行为表格的头部，第一列是一个复选框 “check all”，它会自动地选择页面上的所有记录。

接着使用传进来的记录数组来循环显示每一行的数组。要注意的是变量 $k，它在每次循环中会在 0 和 1 之中更换值，它的作用好是用来更换每个 <tr> 的类，从而控制了每行显示的背景色。

大部分的成员变量会直接输出，但是有两个比较特殊，JHTML::(‘grid.id‘) 函数将返回一个能被后端 JavaScript 识别的复选框，JHTML::_('grid.published') 函数返回一个基于 成员变量 published 的值的图片，当 published 的值是 1 时，将返回打勾的图片，否则返回打“X”的图片。

在表格下面，有四个隐藏的变量，第一个处理 option 的值，以便路由到正确的组件，第二个是 task，它是在提交表单之前以便让工具栏中的 JavaScript 能给它赋值。第三个是 boxchecked，当有任意一行的复选框被选择，boxchecked 被置为 1，当所有行的复选框被清除，boxchecked 被置为 0，它是用来辅助 JavaScript 来处理列表。

当完成了 HTML 代码的输出，最后一步就是更新文件admin.reviews.php中的 switch() 语句，加入下面的高亮代码：

switch($task)

{

case 'add':

editReview( $option );

  break;

case 'save':

  saveReview( $option );

  break;

**default:**

  **showReviews( $option );**

  **break;**

}

在浏览器中输入URL http://localhost/joomla/administrator/index.

php?option=com_reviews，一个相似的页面如下：

**暂时不提供图片显示，请参考《Joomla! extension development》**

 

编辑记录

我们将扩展原有的代码来编辑记录，而不是写一个新的功能。在文件 admin.reviews.php 中的 editReview() 函数中用以下的高亮代码来代替：$row=&JTable:getInstance(‘Review’, ‘Table’)：

function editReview( $option )

{

**$row =& JTable::getInstance('review', 'Table');**

**$cid = JRequest::getVar( 'cid', array(0), '', 'array' );**

**$id = $cid[0];**

**$row->load($id);**

 

当执行 editReview () 函数时，我们取得 TableReview 对象来处理数据，然后会从表单中取得记录ID的数组变量 cid，既然在同一个时间只编辑一条记录，那我们只选择第一个数组元素来加载相应的记录。更新文件 admin.reviews.php 中的 switch() 语句如下：

**case 'edit':**

case 'add':

editReview( $option );

  break;

你应该要提供能够让用户通过点击来编辑各自的记录的链接。在文件 admin.reviews.html.php 的HTML_reviews::showReviews() 函数下加入一下高亮的代码：

**jimport('joomla.filter.output');**

$k = 0;

for ($i=0, $n=count( $rows ); $i < $n; $i++)

{

$row = &$rows[$i];

$checked = JHTML::_('grid.id', $i, $row->id );

$published = JHTML::_('grid.published', $row, $i );

**$link = JFilterOutput::ampReplace( 'index.php?option=' .**

​         **$option . '&task=edit&cid[]='. $row->id );**

?>

<tr class="<?php echo "row$k"; ?>">

  <td>

   <?php echo $checked; ?>

  </td>

  **<td>**

      <a href="<?php echo $link; ?>">

   **<?php echo $row->name; ?></a>**

  **</td>**

  <td>

   <?php echo $row->address; ?>

  </td> 

  <td>

   <?php echo $row->reservations; ?>

  </td>

  <td>

   <?php echo $row->cuisine; ?>

  </td> 

  <td>

   <?php echo $row->credit_cards; ?>

  </td>

  <td align="center">

   <?php echo $published;?>

  </td>

为了兼容 XHTML，我们需要确保符号 & 使用 &amp; 来代替，我们使用 ampReplace() 来处理，它是 JFilterOutput 类的成员函数，JFilterOutput 通过调用 jimport(‘joomla.filter.output’) 来加载。Joomla! 提供了许多不同的库，例如 XML处理和RSS输出等。我们使用 jimport() 函数来按需要加载代码，而不是每次加载Joomla! 是都加载所用的库。你需要更新工具栏的代码，首先，去到文件 toolbar.reviews.php 中的 switch() 语句：

case 'edit':

case 'add':

  TOOLBAR_reviews::_NEW();

  break;

既然已经加入“编辑”函数，我们可以在工具栏加入“编辑”按钮，他可以根据每一行记录选择的复选框的来编辑内容，而不单只是点击链接。打开文件 toolbar.reviews.html.php ，添加以下的高亮代码：

TOOLBAR_reviews::_DEFAULT():

JToolBarHelper::unpublishList();

**JToolBarHelper::editList();**

JToolBarHelper::deleteList();


保存所有的文件，然后刷新页面 http://localhost/joomla/administrator/index.php?option=com_reviews，每一行的记录的name 栏都会带有链接，点击链接你会看到如下的页面：

**暂时不提供图片显示，请参考《Joomla! extension development》**

你可能已经注意到了在编辑页面的工具栏上有个“应用”按钮，它允许人们保存内容的同时，页面依然保留在编辑的状态，为了是应用按钮生效，需要在文件 admin.reviews.php 中做两个改变，在 switch() 语句中加入一下的高亮代码：

**case 'apply':**

case 'save':

saveReview( $option, $task );

  break;

在 saveReview() 函数中加入 $task 参数：

function saveReview( $option, **$task** )

将 saveReview() 函数的最后一行更改为如下：

current $task:

switch ($task)

{

  case 'apply':

   $msg = 'Changes to Review saved';

   $link = 'index.php?option=' . $option .

​     '&task=edit&cid[]='. $row->id;

   break;

  case 'save':

  default:

   $msg = 'Review Saved';

   $link = 'index.php?option=' . $option;

   break;

}

$mainframe->redirect($link, $msg);


删除记录

增加删除的功能是相当的简单，在文件 admin.reviews.php 的 switch() 语句中加入以下的 case 语句：

case 'remove':

removeReviews( $option );

  break;

当然也要增加 removeReviews() 函数：

function removeReviews( $option )

{

global $mainframe;

$cid = JRequest::getVar( 'cid', array(), '', 'array' );

$db =& JFactory::getDBO();

if(count($cid))

{

  $cids = implode( ',', $cid );

  $query = "DELETE FROM #__reviews WHERE id IN ( $cids )";

  $db->setQuery( $query );

  if (!$db->query())

  {

   echo "<script> alert('".$db->getErrorMsg()."');

   window.history.go(-1); </script>\n";

  }

}

$mainframe->redirect( 'index.php?option=' . $option );

}

我们从表单中再一次取得 cid 变量，然后检查数组中是否有 id 元素。如果有 id 元素，那么用逗号将数组中的元素连成字符串，然后用这个字符串来建立 SQL 语句，在执行过程中，除非发生错误，否则重定向到列表页面。