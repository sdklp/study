# Control.DataBindings Property

- Reference

Is this page helpful?

## Definition

- Namespace:

  [System.Windows.Forms](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.forms?view=windowsdesktop-6.0)

- Assembly:

  System.Windows.Forms.dll

Gets the data bindings for the control.

C#

```csharp
public System.Windows.Forms.ControlBindingsCollection DataBindings { get; }
```

#### Property Value

- [ControlBindingsCollection](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.forms.controlbindingscollection?view=windowsdesktop-6.0)

A [ControlBindingsCollection](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.forms.controlbindingscollection?view=windowsdesktop-6.0) that contains the [Binding](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.forms.binding?view=windowsdesktop-6.0) objects for the control.

#### Implements

[DataBindings](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.forms.ibindablecomponent.databindings?view=windowsdesktop-6.0#system-windows-forms-ibindablecomponent-databindings)

## Examples

The following code example adds [Binding](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.forms.binding?view=windowsdesktop-6.0) objects to the [ControlBindingsCollection](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.forms.controlbindingscollection?view=windowsdesktop-6.0) of five controls: four [TextBox](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.forms.textbox?view=windowsdesktop-6.0) controls and a [DateTimePicker](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.forms.datetimepicker?view=windowsdesktop-6.0) control. The [ControlBindingsCollection](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.forms.controlbindingscollection?view=windowsdesktop-6.0) is accessed through the [DataBindings](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.forms.control.databindings?view=windowsdesktop-6.0) property of the [Control](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.forms.control?view=windowsdesktop-6.0) class.

C#

```csharp
protected void BindControls()
{
   /* Create two Binding objects for the first two TextBox 
   controls. The data-bound property for both controls 
   is the Text property. The data source is a DataSet 
   (ds). The data member is specified by a navigation 
   path in the form : TableName.ColumnName. */
   textBox1.DataBindings.Add(new Binding
   ("Text", ds, "customers.custName"));
   textBox2.DataBindings.Add(new Binding
   ("Text", ds, "customers.custID"));
      
   /* Bind the DateTimePicker control by adding a new Binding. 
   The data member of the DateTimePicker is specified by a 
   navigation path in the form: TableName.RelationName.ColumnName. */
   DateTimePicker1.DataBindings.Add(new 
   Binding("Value", ds, "customers.CustToOrders.OrderDate"));

   /* Create a new Binding using the DataSet and a 
   navigation path(TableName.RelationName.ColumnName).
   Add event delegates for the Parse and Format events to 
   the Binding object, and add the object to the third 
   TextBox control's BindingsCollection. The delegates 
   must be added before adding the Binding to the 
   collection; otherwise, no formatting occurs until 
   the Current object of the BindingManagerBase for 
   the data source changes. */
   Binding b = new Binding
   ("Text", ds, "customers.custToOrders.OrderAmount");
   b.Parse += new ConvertEventHandler(CurrencyStringToDecimal);
   b.Format += new ConvertEventHandler(DecimalToCurrencyString);
   textBox3.DataBindings.Add(b);

   /*Bind the fourth TextBox to the Value of the 
   DateTimePicker control. This demonstrates how one control
   can be bound to another.*/
   textBox4.DataBindings.Add("Text", DateTimePicker1,"Value");
   BindingManagerBase bmText = this.BindingContext[
   DateTimePicker1];

   /* Print the Type of the BindingManagerBase, which is 
   a PropertyManager because the data source
   returns only a single property value. */
   Console.WriteLine(bmText.GetType().ToString());
   // Print the count of managed objects, which is 1.
   Console.WriteLine(bmText.Count);

   // Get the BindingManagerBase for the Customers table. 
   bmCustomers = this.BindingContext [ds, "Customers"];
   /* Print the Type and count of the BindingManagerBase.
   Because the data source inherits from IBindingList,
   it is a RelatedCurrencyManager (derived from CurrencyManager). */
   Console.WriteLine(bmCustomers.GetType().ToString());
   Console.WriteLine(bmCustomers.Count);
   
   /* Get the BindingManagerBase for the Orders of the current
   customer using a navigation path: TableName.RelationName. */ 
   bmOrders = this.BindingContext[ds, "customers.CustToOrders"];
}
```

## Remarks

Use the [DataBindings](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.forms.control.databindings?view=windowsdesktop-6.0) property to access the [ControlBindingsCollection](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.forms.controlbindingscollection?view=windowsdesktop-6.0). By adding [Binding](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.forms.binding?view=windowsdesktop-6.0) objects to the collection, you can bind any property of a control to the property of an object.