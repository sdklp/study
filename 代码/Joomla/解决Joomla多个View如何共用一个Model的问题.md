# 解决Joomla多个View如何共用一个Model的问题

在Joomla开发中，一般是一个View会对应一个Model，在view.html.php用$this->getModel();默认情况下是调用跟view相对应的model文件。如果需要多个View共用同一个Model，可以有以下两个方法实现。


一，直接调用model所在的文件，并实例化该类，如：

require_once (JPATH_COMPONENT . DS . 'models' . DS . 'example.php' );
$model = new ExampleModelExample();

二，在controller里应用，如：
在controller.php的display()的函数中加入以下代码：
function display($cache=false){
$view =& JRequest::getVar('view', 'example');
$model = $this->getModel('example1');//在controller中是可以读取该组件下的任意model；
$view = $this->getView($view,'html');
$view -> setModel($model,true);
parent::display($cache);

}