## 动态向ACCESS数据库中创建表及添加记录

### ACCESS字段数据类型：

Boolean、Integer、Long、Currency、Single、Double、Date、String 和 Variant（默认）

### 代码

### 添加数据库 tbl_Name

`

```vbscript
Public Sub AddTable()`
	`Set dbs = CurrentDb`
	`Set tbl = dbs.CreateTableDef("tbl_Name")`
	`Set fld = tbl.CreateField("Field1", dbText, 255)`
	`tbl.Fields.Append fld`
	`Set fld = tbl.CreateField("Field2", dbText, 255)`
	`tbl.Fields.Append fld`
	`Set fld = tbl.CreateField("Field3", dbInteger)`
	`tbl.Fields.Append fld`
	`Set fld = tbl.CreateField("Field4", dbCurrency)`
	`tbl.Fields.Append fld`
	`dbs.TableDefs.Append tbl`
	`dbs.TableDefs.Refresh`

`End Sub
```

`



### 向数据库  tbl_Name  中添加记录


```vbscript
Public Sub AddRecords()
Dim dbs As DAO.Database
Dim rs As DAO.Recordset

Set dbs = CurrentDb
Set rs = dbs.OpenRecordset("tbl_name")

rs.AddNew
rs("field1").Value = "TEST"
rs("field2").Value = "TEXT"
rs("field3").Value = 1991
rs("field4").Value = 19.99
rs.Update

End Sub`
```

`

### 查询记录
`

```vbscript
Public Sub searchRecord()
	Dim Rs As DAO.Recordset
	Set Rs = CurrentDb.OpenRecordset("SELECT * FROM tbl_Name")`

	If Not Rs.EOF And Not Rs.BOF Then`
  		MsgBox Rs(0)`
	Else
  	  MsgBox "没有记录"
	End If

End Sub
```

`

### 修改数据记录

```vbscript
`Public Sub editRecord()`
`	'Set Rs = CurrentDb.OpenRecordset("SELECT * FROM [tbl_Name] 	WHERE Field1 =" & LngUserID)`
    `Set Rs = CurrentDb.OpenRecordset("SELECT * FROM [tbl_Name] WHERE Field1 ='text'")`
`'Flag user as being logged out`
    `If Not Rs.EOF And Not Rs.BOF Then`
        `Rs.Edit`
        `Rs.Fields("Field1").Value = "editField1"`
        `Rs.Fields("Field2").Value = "editField2"`
        `Rs.Update`
        `Rs.Close`
    `End If`
    `Set Rs = Nothing`
`End Sub`
```



### 程序窗口

![image-20210910141853128](C:\Users\konglingping\AppData\Roaming\Typora\typora-user-images\image-20210910141853128.png)

### 创建表的运行结果

![image-20210910144010004](C:\Users\konglingping\AppData\Roaming\Typora\typora-user-images\image-20210910144010004.png)

### 添加记录后的运行结果

![image-20210910144110757](C:\Users\konglingping\AppData\Roaming\Typora\typora-user-images\image-20210910144110757.png)

