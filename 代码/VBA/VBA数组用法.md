# VBA数组用法

**前言**

VBA数组在Excel开发应用中，作用还是很明显的，用好数组可以提高工作效率，下面就开始揭开VBA数组的神秘面纱。

**具体操作**

1、VBA数组的定义方法

下面是几种数组常用的定义方法，一维数组的定义、二维数组的定义

直接赋值定义、调用Array函数定义、调用Excel工作表内存数组

```vbscript
''''''''''''直接定义给数组赋值
'一维常量数组的定义
Sub arrDemo1()
Dim arr(2) As Variant   '数组
arr(0) = "vba"
arr(1) = 100
arr(2) = 3.14
MsgBox arr(0)
End Sub

'二维常量数组的定义
Sub arrDemo2()
Dim arr(1, 1) As Variant  'Dim arr(0 To 1, 0 To 1) As Variant
arr(0, 0) = "apple"
arr(0, 1) = "banana"
arr(1, 0) = "pear"
arr(1, 1) = "grape"
For i = 0 To 1
    For j = 0 To 1
        MsgBox arr(i, j)
    Next
Next
End Sub

''''''''''''用array函数创建常量数组
'一维数组
Sub arrayDemo3()
Dim arr As Variant   '数组
arr = Array("vba", 100, 3.14)
MsgBox arr(0)
End Sub

'二维数组
Sub arrayDemo4()
Dim arr As Variant   '数组
arr = Array(Array("张三", 100), Array("李四", 76), Array("王五", 80))
MsgBox arr(1)(1)
End Sub

'调用Excel工作表内存数组
' 一维数组[{"A",1,"C"}]
'二维数组[{"a",10;"b",20;"c",30}]
Sub mylook()
Dim arr
arr = [{"a",10;"b",20;"c",30}]
Range("a1:b3") = arr
MsgBox Application.WorksheetFunction.VLookup("b", arr, 2, 0)  '调用vlookup时可以作为第二个参数
End Sub

'动态数组的定义方法
Sub arrDemo5()
Dim arr1() '声明一个动态数组（动态指不固定大小）
Dim arr2  '声明一个Variant类型的变量

arr1 = Range("a1:b2")   '把单元格区域A1：B2的值装入数组arr1
arr2 = Range("a1:b2")   '把单元格区域A1：B2的值装入数组arr2

MsgBox arr1(1, 1)  '读取arr数组中第1行第1列的数值
MsgBox arr2(2, 2) '读取arr1数组的第2行第2列的数值
End Sub
```

2、数组的赋值和计算

```vb.net
'读取单元格数据到数组，进行计算，再赋值给单元格
Sub arr_calculate()
Dim arr     '声明一个变量用来盛放单元格数据
Dim i%
arr = Range("a2:d5")     '把单元格数据搬入到arr里，它有4列4行
For i = 1 To 4     '通过循环在arr数组中循环
    arr(i, 4) = arr(i, 3) * arr(i, 2)      '数组的第4列(金额)=第3列*第2例
Next i
Range("a2:d5") = arr     '把数组放回到单元格中
End Sub
```

3、数组的合并（join）与拆分（split）

```vbscript
'数组合并(join)与拆分(Split)
Sub join_demo()
Dim a As Variant
Dim b As Variant
  ' Join using spaces
a = Array("Red", "Blue", "Yellow")
b = Join(a, "")
MsgBox ("The value of b is :" & b) 'Red Bule Yellow

' Join using $
b = Join(a, "$")                   'Red$Bule$Yellow
MsgBox ("The Join result after using delimiter is : " & b)
End Sub

Sub split_demo()
Dim a As Variant
Dim b As Variant
 a = Split("Red$Blue$Yellow", "$")     'a = Array("red"，"blue"，"yellow")
 b = UBound(a)
 For i = 0 To b
    MsgBox a(i)
 Next
End Sub
```

4、数组的筛选（Filter）

```vbscript
'vba数组的筛选
Sub arr_filter()
arr = Array("ABC", "F", "D", "CA", "ER")
arr1 = VBA.Filter(arr, "A", True) '筛选所有含A的数值组成一个新数组
arr2 = VBA.Filter(arr, "A", False) '筛选所有不含A的数值组成一个新数组
MsgBox Join(arr1, ",") '查看筛选的结果
End Sub
```

5、数组维度的转换（Transpose）

```vbscript
'数组维数的转换

'一维转二维
Sub arr_tranpose1()
arr = Array(10, "vba", 2, "b", 3)
arr1 = Application.Transpose(arr)
MsgBox arr1(2, 1) '转换后的数组是1列多行的二维数组
End Sub

'二维数组转一维 '注意:在转置时只有1列N行的数组才能直接转置成一维数组
Sub arr_tranpose2()
arr2 = Range("A1:B5")
arr3 = Application.Transpose(Application.Index(arr2, , 2)) '取得arr2第2列数据并转置成1维数组
MsgBox arr3(4)
End Sub

'把单元格中的内容用“-”连接起来
Sub join_transpose_demo()
arr = Range("A1:C1")
arr1 = Range("A1:A5")
MsgBox Join(Application.Transpose(Application.Transpose(arr)), "-")
MsgBox Join(Application.Transpose(arr1), "-")
End Sub
```

6、利用数组获取所有工作表名称的自定义函数

```vbscript
'利用数组获取所有工作表名称的自定义函数
Function getSheetsname(id)
Dim i%, arr()
k = Sheets.Count
ReDim arr(1 To k)
For i = 1 To k
    arr(i) = Sheets(i).Name
Next
getSheetsname = Application.Index(arr, id)
End Function
```

7、数组赋值，提高计算效率

```vbscript
'数组赋值，提高计算效率
'2.03秒
Sub dataInput()
Dim start As Double
start = Timer
Dim i&
For i = 1 To 30000
    Cells(i, 1) = i
Next
MsgBox "程序运行时间为" & Format(Timer - start, "0.00") & "秒"
End Sub

'0.12秒
Sub dataInputArr()
Dim start As Double
start = Timer
Dim i&, arr(1 To 30000) As String
For i = 1 To 30000
    arr(i) = i
Next
Range("a1:a30000").Value = Application.Transpose(arr)
MsgBox "程序运行时间为" & Format(Timer - start, "0.00") & "秒"
End Sub

'0.09秒
Sub dataInputArr2()
Dim start As Double
start = Timer
Dim i&, arr(1 To 30000, 1 To 1) As String
For i = 1 To 30000
    arr(i, 1) = i
Next
Range("a1:a30000").Value = arr
MsgBox "程序运行时间为" & Format(Timer - start, "0.00") & "秒"
End Sub
```

**总结**

VBA数组还是很强大的，通过对单元格区域数据的读取，赋值给数组，再利用数组函数或者调用Excel内置函数进行相关处理。另外，数组在赋值计算效率上面也是非常高的，大家可以自行尝试下。