字符串分解Split函数的用法  

描述

返回一个下标从零开始的一维数组，它包含指定数目的子字符串。

语法
Split(expression[, delimiter[, limit[, compare]]])
中文描述
Split(字符串或字符串变量[, 分隔符[, 返回个数[, 比较方式]]])

Split函数语法有如下命名参数：

部分 描述
expression 必需的。包含子字符串和分隔符的字符串表达式 。如果expression是一个长度为零的字符串("")，Split则返回一个空数组，即没有元素和数据的数组。
delimiter 可选的。用于标识子字符串边界的字符串字符。如果忽略，则使用空格字符(" ")作为分隔符。如果delimiter是一个长度为零的字符串，则返回的数组仅包含一个元素，即完整的 expression字符串。
limit 可选的。要返回的子字符串数，–1表示返回所有的子字符串。
compare 可选的。数字值，表示判别子字符串时使用的比较方式。关于其值，请参阅“设置值”部分。
设置值
compare参数的设置值如下：
常数 值 描述
vbUseCompareOption –1 用Option Compare语句中的设置值执行比较。
vbBinaryCompare 0 执行二进制比较。
vbTextCompare 1 执行文字比较。
vbDatabaseCompare 2 仅用于Microsoft Access。基于您的数据库的信息执行比较。

示例：
Dim a() As String
Dim s As String
Dim i As Integer
s="123456/234567/678/900/000"
a = Split(s, "/", -1)
for i=LBound(a) to UBound(a)
 msgbox(a(i))
next i

其中使用了 LBound（数组最小下标）函数和UBound（数组最大下标） 函数
LBound 函数
   

返回一个 Long 型数据，其值为指定数组维可用的最小下标。

语法

LBound(arrayname[, dimension])

LBound 函数的语法包含下面部分：

部分 描述
arrayname 必需的。数组变量的名称，遵循标准的变量命名约定。
dimension 可选的；Variant (Long)。指定返回哪一维的下界。1 表示第一维，2 表示第二维，如此类推。如果省略 dimension，就认为是 1。

 

说明

LBound 函数与 UBound 函数一起使用，用来确定一个数组的大小。UBound 用来确定数组某一维的上界。

对具有下述维数的数组而言，LBound 的返回值见下表：

Dim A(1 To 100, 0 To 3, -3 To 4)

语句 返回值
LBound(A, 1) 1
LBound(A, 2) 0
LBound(A, 3) -3

 

所有维的缺省下界都是 0 或 1，这取决于 Option Base 语句的设置。使用 Array 函数创建的数组的下界为 0；它不受 Option Base 的影响。

对于那些在 Dim 中用 To 子句来设定维数的数组而言，Private、Public、ReDim 或 Static 语句可以用任何整数作为下界。

UBound 函数
   

返回一个 Long 型数据，其值为指定的数组维可用的最大下标。

语法

UBound(arrayname[, dimension])

UBound 函数的语法包含下面部分：

部分 描述
arrayname 必需的。数组变量的名称，遵循标准变量命名约定。
dimension 可选的；Variant (Long)。指定返回哪一维的上界。1 表示第一维，2 表示第二维，如此等等。如果省略 dimension，就认为是 1。

 

说明

UBound 函数与 LBound 函数一起使用，用来确定一个数组的大小。LBound 用来确定数组某一维的上界。

对具有下述维数的数组而言，UBound 的返回值见下表：

Dim A(1 To 100, 0 To 3, -3 To 4)

语句 返回值
UBound(A, 1) 100
UBound(A, 2) 3
UBound(A, 3) 4