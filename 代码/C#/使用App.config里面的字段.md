![get connection string from app.config](https://i.stack.imgur.com/B47QC.png)

```
//App.config
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <configSections>
    </configSections>
    <connectionStrings>
        <add name="connStr" connectionString="Provider=Microsoft.ACE.OLEDB.12.0;Data Source=|DataDirectory|\db.accdb"
            providerName="System.Data.OleDb" />
    </connectionStrings>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
</configuration>
```

使用：

```
using System.Configuration;

 string connectionStr=ConfigurationManager.ConnectionStrings["connStr"].ConnectionString;
```

