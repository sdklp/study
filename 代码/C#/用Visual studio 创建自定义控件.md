# 用Visual studio 创建自定义控件

想了解一下自定义控件的知识，不过，在自定义控件在工具条上显示，花了些时间。



最后是出来的，但这里不想仔细找原因了，大至总结几个可能的点：



\1. 新建windows forms control library工程

 

![img](https://img-blog.csdn.net/20140421093704453)




\2. vs设置如下：



![img](https://img-blog.csdn.net/20140421093318046?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGFveXVqaWU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



  工具-》选项-》Windows窗体设计器-》常规-》工具箱-》AutoToolBoxPopulate 改为TRUE

 

![img](https://img-blog.csdn.net/20140421093520015)

vs 2010所在位置，这个图有错误，是toolbax下面那个。

 

 

 

 

**2. 检查项目的.NET Framework项目的版本是否和开发控件的.NET版本一致**

寻找方法，喜爱敏捷，实践方法，不断总结，坚持到底，终将成功。

\3. 基类最好用完整空间名称

public partial class UserControl1 : System.Windows.Forms.Button

.designer.cs 最好删除

\4. 添加这两段：

 

- Add the following code to the Declarations section:

  ``

  ```
  using System.ComponentModel.Design;
  ```

- Apply the

   

  System.ComponentModel.DesignerAttribute

   

  attribute to the control as follows:

  ``

  ```
  [Designer("System.Windows.Forms.Design.ParentControlDesigner, System.Design", typeof(IDesigner))] 
  public class UserControl1 : System.Windows.Forms.UserControl
  {
  
        ...
  
  }
  ```

  \5. 要重新编译。

  

  6.最后自动就会出来。

-  

- ![img](https://img-blog.csdn.net/20140421093709703)

  ===================

  

  ```
  [
      Category("Gradient"),
      Description("First Gradient Color")
  ]
  ```

  ========================

  

  一些链接

  https://workspaces.codeproject.com/haoyujie/writing-your-custom-control-step-by-step

  http://support.microsoft.com/default.aspx?scid=kb;en-us;813450

  http://www.codeproject.com/Articles/837/Your-first-C-control

  http://www.cnblogs.com/jerryjaord/archive/2011/05/09/2040805.html

  =====================

  

  ### Overview

  By default, a

   

  UserControl

   

  object can act as a control container only when you create the control. To make a

  UserControl

   

  host a constituent control after you put the

   

  UserControl

   

  on a Windows Form, you must change the default designer of the

  UserControl

  . To implement design-time services for a component, use the

  DesignerAttribute

   

  class of the

   

  System.ComponentModel

   

  namespace. The

  DesignerAttribute

   

  comes before the class declaration. Initialize the

  DesignerAttribute

   

  by passing the

   

  designerTypeName

   

  and the

  designerBaseType

   

  parameters.

  designerTypeName

   

  is the fully qualified name of the designer type that provides design-time services. Pass the combination of the

  System.Windows.Forms.Design.ParentControlDesigner

   

  and the

   

  System.Design

   

  for the

   

  designerTypeName

   

  parameter. The

   

  ParentControlDesigner

   

  class extends design-time behavior for a

   

  UserControl

  .

  designerBaseType

   

  is the name of the base class for the designer. The class that is used for the design-time services must implement the IDesigner interface.

  

  ### Create the UserControl as a Design-Time Control Container

  1. Create a new Visual C# Windows Control Libraryproject. To do this, follow these steps:

     1. Start Visual Studio.

     2. On the **File** menu, point to**New**, and then click **Project**.

     3. Under **Project Types**, click**Visual C#** , and then click **Windows Forms Control Library** under **Templates**.

        **Note** In Visual Studio 2003, click **Visual C# Projects** under**Project Types** and then click **Windows Control Library** under **Templates**.

  2. Name the projectContainerUserControl. By default,**UserControl1.cs** is created.

  3. In Solution Explorer, right-click**UserControl1.cs**, and then click**ViewCode**.

  4. Add the following code to the Declarations section:

     ``

     ```
     using System.ComponentModel.Design;
     ```

  5. Apply the

      

     System.ComponentModel.DesignerAttribute

      

     attribute to the control as follows:

     ``

     ```
     [Designer("System.Windows.Forms.Design.ParentControlDesigner, System.Design", typeof(IDesigner))] 
     public class UserControl1 : System.Windows.Forms.UserControl
     {
     
           ...
     
     }
     ```

  6. On the **Build** menu, click **BuildSolution**.

  

  ### Test the UserControl

  1. Create a new Visual C# project. To do this, follow thesesteps:

     1. Start Visual Studio.

     2. On the **File** menu, point to**New**, and then click **Project**.

     3. Under **Project Types**, click**Visual C#**, and then click **Windows Forms Application** under **Templates**. By default,**Form1.cs** is created.

        **Note** In Visual Studio 2003, click **Visual C# Projects** under**Project Types**, and then click **Windows Control Library** under **Templates**.

  2. Add the

      

     UserControl1

      

     control to the toolbox.

     1. On the **Tools** menu, click**Choose Toolbox Items**.
     2. On the **.NET Framework Components** tab, click**Browse**.
     3. In the **Open File** box, locate the DLL that was built when you created the**UserControl** control.

  3. Drag **UserControl1** from the toolbox (underWindows Forms) to**Form1.cs**.

  4. Drag a **Button** control from the toolbox to**UserControl1**.

  5. Notice that the **UserControl1** behaves ascontrol container for the**Button** control.

  

  [![img](http://support.microsoft.com/library/images/support/en-us/uparrow.gif)Back to the top](http://support.microsoft.com/default.aspx?scid=kb;en-us;813450#top) | [Give Feedback](http://support.microsoft.com/default.aspx?scid=kb;en-us;813450#survey)

  ## ![Collapse image](http://support.microsoft.com/library/images/support/en-us/20x20_grey_minus.png)REFERENCES

  For more information, see the following Microsoft Web sites:

  ParentControlDesigner Class
  http://msdn.microsoft.com/en-us/library/system.windows.forms.design.parentcontroldesigner(VS.71).aspx

  DesignerAttribute Class
  [http://msdn.microsoft.com/en-us/library/system.componentmodel.designerattribute(vs.71).aspx](http://msdn.microsoft.com/en-us/library/system.windows.forms.design.parentcontroldesigner(VS.71).aspx)