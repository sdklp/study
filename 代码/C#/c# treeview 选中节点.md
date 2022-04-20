## C# treeview默认选中一个节点



有不少朋友都在找关于C#怎么在窗体加载的时候TreeView控件自定义选中任意一个节点.而且需要选中节点的上级节点是展开的.在这里公布源代码.并附上效果图.当窗体一加载的时候就默认选中了销售部(一)的节点 .并且节点0默认展开

```c#
for (int i = 0; i < tv.Nodes.Count; i++)
     {
       for (int j = 0; j < tv.Nodes[i].Nodes.Count; j++)
       {
         if (tv.Nodes[i].Nodes[j].Text.ToUpper().IndexOf("销售部(一)") > -1)
         {
           tv.Nodes[i].Expand();
           tv.SelectedNode = tv.Nodes[i].Nodes[j];
           tv.Focus();
           return;
         }
      }
    }
```



tree控件名称:  tree1;

选择顶层的第一个项:

```c#
tree1.SelectedNode=tree1.nodes(0)
```

选择顶层的第一个项下的第三个项:

```c#
tree1.SelectedNode=tree1.nodes(0).nodes(2)
```

