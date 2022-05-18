# 冒泡算法应用(坐标Y值降序X值升序)

今天有个客户需求是有一坐标数组，希望按Y值降序X值升序排列，我临时写了个算法。
先写个坐标类：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

class XYZ
{
  public XYZ() { }
  public XYZ(double x, double y)
  {
    _X = x;
    _Y = y;
  }
  double _X, _Y;
  public double X
  {
    set { _X = value; }
    get { return _X; }
  }
  public double Y
  {
    set { _Y = value; }
    get { return _Y; }
  }
}

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

冒泡排序：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

XYZ xyz0 = new XYZ(4, 5);
XYZ xyz1 = new XYZ(3, 2);
XYZ xyz2 = new XYZ(2, 1);
XYZ xyz3 = new XYZ(2, 2);
//
XYZ[] arrXYZ = new XYZ[] { xyz0, xyz1, xyz2, xyz3 };
XYZ xyzTmp = new XYZ();
for (int i = 0; i < arrXYZ.Length; i++)
{
  for (int j = 0; j < arrXYZ.Length - 1 - i; j++)
  {
    if (arrXYZ[j].Y < arrXYZ[j + 1].Y)//按Y值降序排列
    {
      xyzTmp = arrXYZ[j];
      arrXYZ[j] = arrXYZ[j + 1];
      arrXYZ[j + 1] = xyzTmp;
    }
  }
}
//在以Y值分组
List<double> listDb = new List<double>();
for (int i = 0; i < arrXYZ.Length; i++)
{
  if (!listDb.Contains(arrXYZ[i].Y))
    listDb.Add(arrXYZ[i].Y);
}
//再以X值升序排列
for (int k = 0; k < listDb.Count; k++)
{
  for (int i = 0; i < arrXYZ.Length; i++)
  {
    for (int j = 0; j < arrXYZ.Length - 1 - i; j++)
    {
      if (arrXYZ[j].Y != listDb[k] || arrXYZ[j + 1].Y != listDb[k]) continue;
      //这里要找出Y值相同的。
      if (arrXYZ[j].X > arrXYZ[j + 1].X)
      {
        xyzTmp = arrXYZ[j];
        arrXYZ[j] = arrXYZ[j + 1];
        arrXYZ[j + 1] = xyzTmp;
      }
    }
  }
}

for (int i = 0; i < arrXYZ.Length; i++)
{
  MessageBox.Show(arrXYZ[i].X + "," + arrXYZ[i].Y);
}

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

用Linq简练多了：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

private List<XYZ> sortYandX(List<XYZ> listXy)
{
  var query = from element in listXy
        orderby element.Y descending, element.X ascending
        select element;
  return new List<XYZ>(query);
}
List<XYZ> listXYZ = new List<XYZ>();
listXYZ.Add(xyz0);
listXYZ.Add(xyz1);
listXYZ.Add(xyz2);
listXYZ.Add(xyz3);
foreach (XYZ xyz in sortYandX(listXYZ))
{
  MessageBox.Show(xyz.X + "," + xyz.Y);
}

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

临时时想了下，对算法之类很陌生，敬请高手指教。