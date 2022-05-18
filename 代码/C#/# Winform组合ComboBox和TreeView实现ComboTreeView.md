# Winform组合ComboBox和TreeView实现ComboTreeView



最近做Winform项目需要用到类似ComboBox的TreeView控件。

虽然各种第三方控件很多，但是存在各种版本不兼容问题。所以自己写了个简单的ComboTreeView控件。

下图是实现效果：

![img](https://img2018.cnblogs.com/blog/649291/201909/649291-20190926101139986-488729408.png)

目前实现的比较简单，能满足我项目中的需求。

此处是项目中的代码简化后的版本，供大家参考。

```
  1 using System;
  2 using System.Collections.Generic;
  3 using System.Windows.Forms;
  4 
  5 namespace CustomControl.Tree
  6 {
  7     public abstract class ComboTreeView<T> : ComboBox where T : class
  8     {
  9         protected const int WM_LBUTTONDOWN = 0x0201, WM_LBUTTONDBLCLK = 0x0203;
 10 
 11         protected TreeView treeView;
 12         protected ToolStripControlHost treeViewHost;
 13         protected ToolStripDropDown dropDown;
 14         protected bool dropDownOpen = false;
 15         protected TreeNode selectedNode;
 16         protected T toBeSelected;
 17 
 18         public ComboTreeView(TreeView internalTreeView)
 19         {
 20             if (null == internalTreeView)
 21             {
 22                 throw new ArgumentNullException("internalTreeView");
 23             }
 24             this.InitializeControls(internalTreeView);
 25         }
 26 
 27         public event TreeNodeChangedEventHandler TreeNodeChanged;
 28 
 29         protected virtual void InitializeControls(TreeView internalTreeView)
 30         {
 31             this.treeView = internalTreeView;
 32             this.treeView.BorderStyle = BorderStyle.FixedSingle;
 33             this.treeView.Margin = new Padding(0);
 34             this.treeView.Padding = new Padding(0);
 35             this.treeView.AfterExpand += new TreeViewEventHandler(this.WhenAfterExpand);
 36 
 37             this.treeViewHost = new ToolStripControlHost(this.treeView);
 38             this.treeViewHost.Margin = new Padding(0);
 39             this.treeViewHost.Padding = new Padding(0);
 40             this.treeViewHost.AutoSize = false;
 41 
 42             this.dropDown = new ToolStripDropDown();
 43             this.dropDown.Margin = new Padding(0);
 44             this.dropDown.Padding = new Padding(0);
 45             this.dropDown.AutoSize = false;
 46             this.dropDown.DropShadowEnabled = true;
 47             this.dropDown.Items.Add(this.treeViewHost);
 48             this.dropDown.Closed += new ToolStripDropDownClosedEventHandler(this.OnDropDownClosed);
 49 
 50             this.DropDownWidth = this.Width;
 51             base.DropDownStyle = ComboBoxStyle.DropDownList;
 52             base.SizeChanged += new EventHandler(this.WhenComboboxSizeChanged);
 53         }
 54 
 55         public new ComboBoxStyle DropDownStyle
 56         {
 57             get { return base.DropDownStyle; }
 58             set { base.DropDownStyle = ComboBoxStyle.DropDownList; }
 59         }
 60 
 61         public virtual TreeNode SelectedNode
 62         {
 63             get { return this.selectedNode; }
 64             private set { this.treeView.SelectedNode = value; }
 65         }
 66 
 67         public virtual T SelectedNodeData
 68         {
 69             get { return (T)base.SelectedItem; }
 70             set
 71             {
 72                 this.toBeSelected = value;
 73                 this.UpdateComboBox(value);
 74             }
 75         }
 76 
 77         protected new int SelectedIndex
 78         {
 79             get { return base.SelectedIndex; }
 80             set { base.SelectedIndex = value; }
 81         }
 82 
 83         protected new object SelectedItem
 84         {
 85             get { return base.SelectedItem; }
 86             set { base.SelectedItem = value; }
 87         }
 88 
 89         public virtual string DisplayMember { get; set; } = "Name";
 90 
 91         /// <summary>Gets the internal TreeView control.</summary>
 92         public virtual TreeView TreeView => this.treeView;
 93 
 94         /// <summary>Gets the collection of tree nodes that are assigned to the tree view control.</summary>
 95         /// <returns>A <see cref="T:System.Windows.Forms.TreeNodeCollection" /> that represents the tree nodes assigned to the tree view control.</returns>
 96         public virtual TreeNodeCollection Nodes => this.treeView.Nodes;
 97 
 98         public new int DropDownHeight { get; set; } = 100;
 99 
100         public new int DropDownWidth { get; set; } = 100;
101 
102         protected virtual void ShowDropDown()
103         {
104             this.dropDown.Width = this.Width;
105             this.dropDown.Height = this.DropDownHeight;
106             this.treeViewHost.Width = this.Width;
107             this.treeViewHost.Height = this.DropDownHeight;
108             this.treeView.Font = this.Font;
109             this.dropDown.Focus();
110             this.dropDownOpen = true;
111             this.dropDown.Show(this, 0, base.Height);
112         }
113 
114         protected virtual void HideDropDown()
115         {
116             this.dropDown.Hide();
117             this.dropDownOpen = false;
118         }
119 
120         protected virtual void ToggleDropDown()
121         {
122             if (!this.dropDownOpen)
123             {
124                 this.ShowDropDown();
125             }
126             else
127             {
128                 this.HideDropDown();
129             }
130         }
131 
132         protected override void WndProc(ref Message m)
133         {
134             if ((WM_LBUTTONDOWN == m.Msg) || (WM_LBUTTONDBLCLK == m.Msg))
135             {
136                 if (!this.Focused)
137                 {
138                     this.Focus();
139                 }
140                 this.ToggleDropDown();
141             }
142             else
143             {
144                 base.WndProc(ref m);
145             }
146         }
147 
148         protected override void Dispose(bool disposing)
149         {
150             if (disposing)
151             {
152                 if (this.dropDown != null)
153                 {
154                     this.dropDown.Dispose();
155                     this.dropDown = null;
156                 }
157             }
158             base.Dispose(disposing);
159         }
160 
161         protected virtual void WhenTreeNodeChanged(TreeNode newValue)
162         {
163             if ((null != this.selectedNode) || (null != newValue))
164             {
165                 bool changed;
166                 if ((null != this.selectedNode) && (null != newValue))
167                 {
168                     changed = (this.selectedNode.GetHashCode() != newValue.GetHashCode());
169                 }
170                 else
171                 {
172                     changed = true;
173                 }
174 
175                 if (changed)
176                 {
177                     if (null != this.TreeNodeChanged)
178                     {
179                         try
180                         {
181                             this.TreeNodeChanged.Invoke(this, new TreeNodeChangedEventArgs(this.selectedNode, newValue));
182                         }
183                         catch (Exception)
184                         {
185                             // do nothing
186                         }
187                     }
188 
189                     this.selectedNode = newValue;
190                     this.UpdateComboBox(this.GetTreeNodeData(newValue));
191                 }
192             }
193         }
194 
195         protected virtual void OnDropDownClosed(object sender, ToolStripDropDownClosedEventArgs e)
196         {
197             if (null == this.toBeSelected)
198             {
199                 var selectedNode = this.treeView.SelectedNode;
200                 this.WhenTreeNodeChanged(selectedNode);
201             }
202         }
203 
204         protected virtual void UpdateComboBox(T data)
205         {
206             base.DisplayMember = this.DisplayMember; // update DisplayMember
207             if (null != data)
208             {
209                 this.DataSource = new List<T>() { data };
210                 this.SelectedIndex = 0;
211             }
212             else
213             {
214                 this.DataSource = null;
215             }
216         }
217 
218         protected virtual void WhenAfterExpand(object sender, TreeViewEventArgs e)
219         {
220             if (null != this.toBeSelected)
221             {
222                 if (this.SelectChildNode(e.Node.Nodes, this.toBeSelected))
223                 {
224                     this.toBeSelected = null;
225                 }
226             }
227         }
228 
229         protected virtual void WhenComboboxSizeChanged(object sender, EventArgs e)
230         {
231             this.DropDownWidth = base.Width;
232         }
233 
234         public virtual bool SelectChildNode(TreeNodeCollection nodes, T data)
235         {
236             var node = this.FindChildNode(nodes, data);
237             if (null != node)
238             {
239                 this.DoSelectTreeNode(node);
240                 return true;
241             }
242             else
243             {
244                 return false;
245             }
246         }
247 
248         protected abstract bool Identical(T x, T y);
249 
250         protected virtual void DoSelectTreeNode(TreeNode node)
251         {
252             this.SelectedNode = node;
253             this.ExpandTreeNode(node.Parent);
254         }
255 
256         public virtual TreeNode FindChildNode(TreeNodeCollection nodes, T data)
257         {
258             foreach (TreeNode node in nodes)
259             {
260                 var nodeData = this.GetTreeNodeData(node);
261                 if (this.Identical(nodeData, data))
262                 {
263                     return node;
264                 }
265             }
266 
267             return null;
268         }
269 
270         public virtual void ExpandTreeNode(TreeNode node)
271         {
272             if (null != node)
273             {
274                 node.Expand();
275                 this.ExpandTreeNode(node.Parent);
276             }
277         }
278 
279         public abstract T GetTreeNodeData(TreeNode node);
280     }
281 }
```

 