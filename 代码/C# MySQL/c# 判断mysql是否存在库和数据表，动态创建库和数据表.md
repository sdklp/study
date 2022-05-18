# c# 判断mysql是否存在库和数据表，动态创建库和数据表

```cpp
MySqlConnection mConnection = new MySqlConnection("server=localhost;Persist Security Info=yes;User Id=root;password=;Charset=utf8;");
```

1.判断mysql里是否有某个数据库



```csharp
mConnection.Open();

MySqlCommand mySqlCommand = new MySqlCommand($@"SELECT * FROM information_schema.SCHEMATA where SCHEMA_NAME='{dataBaseName}'", mConnection);

MySqlDataReader mySqlDataReader = mySqlCommand.ExecuteReader();

while (mySqlDataReader.Read())

{

object name = mySqlDataReader.GetString(1);

 if (name.ToString() == "{dataBaseName}")

{

mConnection.Close();

return true;

}

 }

 mConnection.Close();
```

2.判断库里是否有某表



```csharp
mConnection.Open();

MySqlCommand mySqlCommand = new MySqlCommand($@"SELECT table_name FROM information_schema.TABLES WHERE table_name ='{tableName}';", mConnection);

if (mySqlCommand.ExecuteScalar()!=null)

{

mConnection.Close();

return true;

 }

mConnection.Close();

return false;
```

3.创建库



```csharp
MySqlCommand mySqlCommand = new MySqlCommand($"CREATE DATABASE {dataBaseName};", mConnection);

mConnection.Open();

mySqlCommand.ExecuteNonQuery();

mConnection.Close();
```

4.创建表



```csharp
mConnection = new MySqlConnection($"server=localhost;User Id=root;password=;Database={dataBaseName};Charset=utf8;");

string createStatement = $"CREATE TABLE {tableName} (id Integer PRIMARY KEY AUTO_INCREMENT,account VarChar(50) CHARACTER SET utf8,password VarChar(50) CHARACTER SET utf8)";

 using (mConnection)

 {

mConnection.Open();

using (MySqlCommand cmd = new MySqlCommand(createStatement, mConnection))

 {

cmd.ExecuteNonQuery();

}

 }

 mConnection.Close();
```