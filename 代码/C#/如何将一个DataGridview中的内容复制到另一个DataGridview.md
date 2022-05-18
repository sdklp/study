```cs
//References to source and target grid.

DataGridView sourceGrid = this.dataGridView1;
DataGridView targetGrid = this.dataGridView2;

//Copy all rows and cells.

var targetRows = new List<DataGridViewRow>();

foreach (DataGridViewRow sourceRow in sourceGrid.Rows)
{

    if (!sourceRow.IsNewRow)
    {

        var targetRow = (DataGridViewRow)sourceRow.Clone();

        //The Clone method do not copy the cell values so we must do this manually.
        //See: https://msdn.microsoft.com/en-us/library/system.windows.forms.datagridviewrow.clone(v=vs.110).aspx

        foreach (DataGridViewCell cell in sourceRow.Cells)
        {
            targetRow.Cells[cell.ColumnIndex].Value = cell.Value;
        }

        targetRows.Add(targetRow);

    }

}

//Clear target columns and then clone all source columns.

targetGrid.Columns.Clear();

foreach (DataGridViewColumn column in sourceGrid.Columns)
{
    targetGrid.Columns.Add((DataGridViewColumn)column.Clone());
}

//It's recommended to use the AddRange method (if available)
//when adding multiple items to a collection.

targetGrid.Rows.AddRange(targetRows.ToArray());
```





其它

```c#
DataTable dt = (DataTable)dataGridView1.DataSource;

dataGridView2.DataSource=dt;
```

