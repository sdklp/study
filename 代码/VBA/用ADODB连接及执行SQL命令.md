### 用ADODB操作数据库，首先要添加ADBDB的引用

### 添加ADODB引用

在VBA界面中点击工具------>引用----->选择Microsoft ActiveX Data Objects------->点击确认，即完成ADODB的引用

![image-20210910152232151](C:\Users\konglingping\AppData\Roaming\Typora\typora-user-images\image-20210910152232151.png)

![image-20210910152204357](C:\Users\konglingping\AppData\Roaming\Typora\typora-user-images\image-20210910152204357.png)



```vbscript
`Public Sub adodbTest()`
    `Dim cnAdo As New ADODB.Connection '声明一个新的ADO连接`
    `Dim strPath As String`
    `Dim NameStr As String`
    `Dim TypeStr As String`
    `NameStr = Combo19.Value`
    `TypeStr = Combo21.Value`
`'    MsgBox NameStr`
`'    MsgBox TypeStr`

‘此处要连接的数据库为D:\HVAC\新工作\设备台帐\MaintenanceRecord.accdb

​    `strPath = "D:\HVAC\新工作\设备台帐\MaintenanceRecord.accdb"`
    cnAdo.Open "Provider=Microsoft.ACE.OLEDB.12.0;Data Source=" & strPath
If cnAdo.State = adStateOpen Then
    DoCmd.SetWarnings False
    '要操作的数据表为PartsList,里面有两个字段PartsName和PartsType
    DoCmd.RunSQL "insert into PartsList (PartsName,PartsType) values ('" & NameStr & "','" & TypeStr & "')"
    cnAdo.Close
    Set cnAdo = Nothing
Else
    MsgBox "数据库连接失败！"
End If
`End Sub`
```
