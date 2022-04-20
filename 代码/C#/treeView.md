# [TreeView](https://www.cnblogs.com/zhdonghu/archive/2009/07/21/1528110.html)

TreeView概述
TreeView控件用于以节点形式显示文本或数据。
TreeView控件由System.Windows.Forms.TreeView类来定义;
而其中的节点(即Node)，由System.Windows.Forms.TreeNode类来定义的。


所以当在程序中创建一个TreeView对象，其实只是创建了一个可以放置节点的"容器"。而在这个容器中加入一个节点，其实就是加入了从TreeNode类中创建的一个节点对象；同样删除一个节点，也就是删除一个TreeNode节点对象。
 
TreeView主要属性：
Name：获取或设置控件的名称。
Nodes：获取分配给树视图控件的树节点集合。
Parent：获取或设置控件的父容器
Index：选定项的索引值
Tag：获取或设置包含有关控件的数据的对象
TopNode：获取树视图控件中第一个完全可见的树节点。
Text：指定节点显示的文本
SelectedNode：当前被选择的节点
ExpandDepth：指定默认情况下节点是否打开
Selected：标明当前节点是否被选择的节点
ShowCheckBox： 标明当前节点是否显示复选框
CheckedNodes：声明被选择的单个或者多个节点
ShowCheckBoxes：声明是否显示复选框
ShowExpandCollapse：声明是否显示/折叠状态
ShowLines： 声明节点间是否以线连接
Checked：标明节点上的复选框的选择状态
HotTracking:指示当鼠标指针移过树节点标签时，树节点标签是否具有超链接的外观。


TreeView主要方法：
1定位根节点：treeView1.SelectedNode=treeView1.Nodes[0]
2 展开所有节点：treeView.SelectedNode.ExpandAll();
3 燕尾服开选定节点的下一级节点：treeView1.SelectedNode.Expand()
4 折叠所有节点：treeView.SelectedNode.Collapse();
5 添加节点：tvw.Nodes.Add(new TreeNode("根节点"));
6 删除当前选中节点：tvw.Nodes[0].Nodes[tvw.SelectedNode.Index].Remove();
7 删除指定索引位置上的节点：tvw.Nodes[0].Nodes.RemoveAt(0);

TreeView主要事件:
AfterCheck:选定树节点旁边的复选框后触发
AfterCollapse:折叠树节点后触发
AfterExpand:展开树节点后触发
AfterSelect:选定树节点后触发
 
BeforeCheck: 在将要更改选定树节点旁边的复选框时触发
BeforeCollapse: 在将要折叠树节点时触发
BeforeExpand: 在将要展开树节点时触发
BeforeSelect: 在将要更改选定树节点时触发


 
 
例题:实现如下所示的功能


using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Text;
using System.Windows.Forms;
using System.IO;

namespace treeview
...{
  public partial class Form1 : Form
  ...{
    public Form1()
    ...{
      InitializeComponent();
      FilltvwDirectory();
      LoadImageToComBox();
    }

​    /**//// <summary>
​    /// 向树tvw添加盘符
​    /// </summary>
​    private void FilltvwDirectory()
​    ...{
​      //获取当前设备盘符，并添加到数组drivers[i]里面
​      string[] drives = Environment.GetLogicalDrives();
​      for (int i = 0; i < drives.Length; i++)
​      ...{
​        TreeNode cRoot = new TreeNode(drives[i]);
​        //TreeNode cRoot = new TreeNode();
​        //cRoot.Text = drives[i];

​        tvwDirectory.Nodes.Add(cRoot);

​        //添加该盘符下的文件夹
​        this.AddDirectories(cRoot);
​      }
​    }

​    /**//// <summary>
​    /// 添加该盘符下的文件夹
​    /// </summary>
​    /// <param name="node"></param>
​    private void AddDirectories(TreeNode node)
​    ...{
​      try
​      ...{
​        //获取当前节点路径
​        DirectoryInfo dir = new DirectoryInfo(GetPathFromNode(node));

​        //获取当前节点所在路径下的子文件夹
​        DirectoryInfo[] e = dir.GetDirectories();

​        for (int i = 0; i < e.Length; i++)
​        ...{
​          string name = e[i].Name;

​          //判断是否是上级目录
​          if (!name.Equals(".") && !name.Equals(".."))
​          ...{
​            node.Nodes.Add(new TreeNode(name));
​          }
​        }
​      }
​      catch (Exception e)
​      ...{
​        // MessageBox.Show(e.Message);
​      }
​    }

​    /**//// <summary>
​    /// 递归调用产生节点对应文件夹的路径
​    /// </summary>
​    /// <param name="node"></param>
​    /// <returns></returns>
​    private string GetPathFromNode(TreeNode node)
​    ...{
​      if (node.Parent == null)
​      ...{
​        return node.Text;
​      }

​      //递归调用产生节点对应文件夹的路径
​      //Combine(string path1,string path2)合并两个路径字符中
​      //如Path.Combine("C:windows","Kugoo");则生成C:\windows\Kugkoo
​      return Path.Combine(GetPathFromNode(node.Parent), node.Text);
​    }

​    /**//// <summary>
​    /// ComBox的内容
​    /// </summary>
​    private void LoadImageToComBox()
​    ...{
​      cbxImageList.Items.AddRange(new object[] ...{ "系统图像", "图像A", "图像B", "图像C", "图像D" });
​    }

​    /**//// <summary>
​    /// 在将要展开树节点时触发的事件
​    /// </summary>
​    /// <param name="sender"></param>
​    /// <param name="e"></param>
​    private void tvwDirectory_BeforeExpand(object sender, TreeViewCancelEventArgs e)
​    ...{
​      TreeNode nodeExpanding = (TreeNode)e.Node;
​      AddSubDirectories(nodeExpanding);
​    }

​    /**//// <summary>
​    /// 调用AddDirectores()将该节点的子节点添加到树视图中该节点下
​    /// </summary>
​    /// <param name="node"></param>
​    private void AddSubDirectories(TreeNode node)
​    ...{
​      for (int i = 0; i < node.Nodes.Count; i++)
​      ...{
​        AddDirectories(node.Nodes[i]);
​      }
​    }

​    /**//// <summary>
​    /// 是否排序
​    /// </summary>
​    /// <param name="sender"></param>
​    /// <param name="e"></param>
​    private void chkOrder_Click(object sender, EventArgs e)
​    ...{
​     
​      if (chkOrder.Checked == true)
​      ...{
​        this.tvwDirectory.Sort();
​      }
​      else
​      ...{
​        this.tvwDirectory.Refresh();
​      }
​    }

​    /**//// <summary>
​    /// 是否显示复选框
​    /// </summary>
​    /// <param name="sender"></param>
​    /// <param name="e"></param>
​    private void chkCheckBox_Click(object sender, EventArgs e)
​    ...{
​      if (chkCheckBox.Checked == true)
​      ...{
​        tvwDirectory.CheckBoxes = true;
​      }
​      else
​      ...{
​        tvwDirectory.CheckBoxes = false;
​      }
​    }

​    /**//// <summary>
​    /// 是否显示根级线
​    /// </summary>
​    /// <param name="sender"></param>
​    /// <param name="e"></param>
​    private void chkRootLine_Click(object sender, EventArgs e)
​    ...{
​      if (chkRootLine.Checked == true)
​      ...{
​        tvwDirectory.ShowRootLines = true;
​      }
​      else
​      ...{
​        tvwDirectory.ShowRootLines = false;
​      }
​    }

​    /**//// <summary>
​    /// 是否显示同级线
​    /// </summary>
​    /// <param name="sender"></param>
​    /// <param name="e"></param>
​    private void chkLine_Click(object sender, EventArgs e)
​    ...{
​      if (chkLine.Checked == true)
​      ...{
​        tvwDirectory.ShowLines = true;
​      }
​      else
​      ...{
​        tvwDirectory.ShowLines = false;
​      }
​    }

​    /**//// <summary>
​    /// 是否显示跟踪线
​    /// 指示当鼠标指针移过树节点标签时，树节点标签是否具有超链接的外观。
​    /// </summary>
​    /// <param name="sender"></param>
​    /// <param name="e"></param>
​    private void chkTrack_Click(object sender, EventArgs e)
​    ...{
​      if (chkTrack.Checked == true)
​      ...{
​        tvwDirectory.HotTracking = true;
​      }
​      else
​      ...{
​        tvwDirectory.HotTracking = false;
​      }
​    }

​    /**//// <summary>
​    /// 是否显示+ -号
​    /// </summary>
​    /// <param name="sender"></param>
​    /// <param name="e"></param>
​    private void chkPlus_Click(object sender, EventArgs e)
​    ...{
​      if (chkPlus.Checked == true)
​      ...{
​        tvwDirectory.ShowPlusMinus = true;
​      }
​      else
​      ...{
​        tvwDirectory.ShowPlusMinus = false;
​      }
​    }

​    private void chkHideState_Click(object sender, EventArgs e)
​    ...{
​      if (chkHideState.Checked == true)
​      ...{
​        tvwDirectory.HideSelection = true;
​      }
​      else
​      ...{
​        tvwDirectory.HideSelection = false;
​      }
​    }

​    /**//// <summary>
​    /// mouse单击节点时发生
​    /// </summary>
​    /// <param name="sender"></param>
​    /// <param name="e"></param>
​    private void tvwDirectory_NodeMouseClick(object sender, TreeNodeMouseClickEventArgs e)
​    ...{
​      txtIndex.Text = tvwDirectory.SelectedNode.Index.ToString();
​      txtText.Text = tvwDirectory.SelectedNode.Text.ToString();
​    }

​    private void indentUpDown_ValueChanged(object sender, EventArgs e)
​    ...{
​      //设置indentUpDown中微调范围
​      indentUpDown.Maximum = 250;
​      indentUpDown.Minimum = 150;

​      tvwDirectory.Width = int.Parse( indentUpDown.Value.ToString());
​    }

​    /**//// <summary>
​    /// 为树设置图标
​    /// </summary>
​    /// <param name="sender"></param>
​    /// <param name="e"></param>
​    private void cbxImageList_SelectedIndexChanged(object sender, EventArgs e)
​    ...{
​      this.tvwDirectory.ImageList = this.tvwimgList;

​      if (cbxImageList.SelectedIndex == 1)
​      ...{
​        this.tvwDirectory.SelectedImageIndex = 0;
​        this.tvwDirectory.ImageIndex = 0;
​      } else if(cbxImageList.SelectedIndex==2)
​      ...{
​        this.tvwDirectory.SelectedImageIndex = 1;
​        this.tvwDirectory.ImageIndex = 1;
​      } else if (cbxImageList.SelectedIndex == 3)
​      ...{
​        this.tvwDirectory.SelectedImageIndex = 2;
​        this.tvwDirectory.ImageIndex = 2;
​      }
​      else if (cbxImageList.SelectedIndex == 4)
​      ...{
​        this.tvwDirectory.SelectedImageIndex = 3;
​        this.tvwDirectory.ImageIndex = 3;
​      }
​      else
​      ...{
​        this.tvwDirectory.SelectedImageIndex = 5;
​        this.tvwDirectory.ImageIndex = 5;
​      }
​    }
  }
}