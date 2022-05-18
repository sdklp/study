C# OpenFileDialog control allows us to browse and select files on a computer in an application. A typical Open File Dialog looks like Figure 1 where you can see Windows Explorer like features to navigate through folders and select a file.



*![OpenFileDialog In C#](https://csharpcorner-mindcrackerinc.netdna-ssl.com/UploadFile/mahesh/openfiledialog-in-C-Sharp/Images/OpenFileDlgImg1.jpg)*

**Figure 1** 

## Creating a OpenFileDialog

We can create an OpenFileDialog control using a Forms designer at design-time or using the OpenFileDialog class in code at run-time (also known as dynamically). Unlike other Windows Forms controls, an OpenFileDialog does not have and not need visual properties like others. The only purpose of OpenFileDialog to display available colors, create custom colors and select a color from these colors. Once a color is selected, we need that color in our code so we can apply it on other controls.

Again, you can create an OpenFileDialog at design-time but it is easier to create an OpenFileDialog at run-time.

**Design-time**

To create an OpenFileDialog control at design-time, you simply drag and drop an OpenFileDialog control from Toolbox to a Form in Visual Studio. After you drag and drop an OpenFileDialog on a Form, the OpenFileDialog looks like Figure 2.





*![OpenFileDialog In C#](https://csharpcorner-mindcrackerinc.netdna-ssl.com/UploadFile/mahesh/openfiledialog-in-C-Sharp/Images/OpenFileDlgImg2.jpg)*

**Figure 2** 

Adding an OpenFileDialog to a Form adds following two lines of code.

1. **private** System.Windows.Forms.OpenFileDialog openFileDialog1; 
2. **this**.openFileDialog1 = **new** System.Windows.Forms.OpenFileDialog(); 

**Run-time**

Creating a OpenFileDialog control at run-time is merely a work of creating an instance of OpenFileDialog class, set its properties and add OpenFileDialog class to the Form controls.

First step to create a dynamic OpenFileDialog is to create an instance of OpenFileDialog class. The following code snippet creates an OpenFileDialog control object.

1. OpenFileDialog openFileDialog1 = **new** OpenFileDialog(); 

ShowDialog method displays the OpenFileDialog.

1. openFileDialog1.ShowDialog(); 

Once the ShowDialog method is called, you can browse and select a file.



## Setting OpenFileDialog Properties

After you place an OpenFileDialog control on a Form, the next step is to set properties.

The easiest way to set properties is from the Properties Window. You can open Properties window by pressing F4 or right click on a control and select Properties menu item. The Properties window looks like Figure 3.





*![OpenFileDialog In C#](https://csharpcorner-mindcrackerinc.netdna-ssl.com/UploadFile/mahesh/openfiledialog-in-C-Sharp/Images/OpenFileDlgImg3.jpg)*

**Figure 3** 

**Initial and Restore Directories**

InitialDirectory property represents the directory to be displayed when the open file dialog appears first time.

1. openFileDialog1.InitialDirectory = @"C:\"; 

If RestoreDirectory property set to true that means the open file dialogg box restores the current directory before closing.

1. openFileDialog1.RestoreDirectory = **true**; 

**Title**

Title property is used to set or get the title of the open file dialog.

1. openFileDialog1.Title = "Browse Text Files"; 

**Default Extension**

DefaultExtn property represents the default file name extension.



1. openFileDialog1.DefaultExt = "txt"; 

**Filter and Filter Index**

Filter property represents the filter on an open file dialog that is used to filter the type of files to be loaded during the browse option in an open file dialog. For example, if you need users to restrict to image files only, we can set Filter property to load image files only.

1. openFileDialog1.Filter = "txt files (*.txt)|*.txt|All files (*.*)|*.*"; 

FilterIndex property represents the index of the filter currently selected in the file dialog box.

1. openFileDialog1.FilterIndex = 2; 

**Check File Exists and Check Path Exists**

CheckFileExists property indicates whether the dialog box displays a warning if the user specifies a file name that does not exist. CheckPathExists property indicates whether the dialog box displays a warning if the user specifies a path that does not exist.

1. openFileDialog1.CheckFileExists = **true**; 
2. openFileDialog1.CheckPathExists = **true**; 

**File Name and File Names**

FileName property represents the file name selected in the open file dialog.

1. textBox1.Text = openFileDialog1.FileName; 

If MultiSelect property is set to true that means the open file dialog box allows multiple file selection. The FileNames property represents all the files selected in the selection.

1. **this**.openFileDialog1.Multiselect = **true**; 
2. **foreach** (String file **in** openFileDialog1.FileNames)  
3. {  
4.  MessageBox.Show(file); 
5. } 

**Read Only Checked and Show Read Only Files**

ReadOnlyChecked property represents whether the read-only checkbox is selected and ShowReadOnly property represents whether the read-only checkbox is available or not.

1. openFileDialog1.ReadOnlyChecked = **true**; 
2. openFileDialog1.ShowReadOnly = **true**; 

## Implementing OpenFileDialog in a C# and WinForms Applications

Now let's create a WinForms application that will use an OpenFileDialog that has two Button controls, a TextBox, and a container control. The Form looks like Figure 4.





*![OpenFileDialog In C#](https://csharpcorner-mindcrackerinc.netdna-ssl.com/UploadFile/mahesh/openfiledialog-in-C-Sharp/Images/OpenFileDlgImg4.jpg)*

**Figure 4** 

The Browse button click event handler will show an open file dialog and users will be able to select text files. The open file dialog looks like Figure 5.





![OpenFileDialog In C#](https://csharpcorner-mindcrackerinc.netdna-ssl.com/UploadFile/mahesh/openfiledialog-in-C-Sharp/Images/OpenFileDlgImg5.jpg)

Figure 5 

The following code snippet is the code for Browse button click event handler. Once a text file is selected, the name of the text file is displayed in the TextBox.

1. **private** **void** BrowseButton_Click(**object** sender, EventArgs e) 
2. { 
3.   OpenFileDialog openFileDialog1 = **new** OpenFileDialog 
4.   { 
5. ​    InitialDirectory = @"D:\", 
6. ​    Title = "Browse Text Files", 
7.  
8. ​    CheckFileExists = **true**, 
9. ​    CheckPathExists = **true**, 
10.  
11. ​    DefaultExt = "txt", 
12. ​    Filter = "txt files (*.txt)|*.txt", 
13. ​    FilterIndex = 2, 
14. ​    RestoreDirectory = **true**, 
15.  
16. ​    ReadOnlyChecked = **true**, 
17. ​    ShowReadOnly = **true** 
18.   }; 
19.  
20.   **if** (openFileDialog1.ShowDialog() == DialogResult.OK) 
21.   { 
22. ​    textBox1.Text = openFileDialog1.FileName; 
23.   } 
24. } 

The code for Browse Multiple Files button click event handler looks like following.

1. **private** **void** BrowseMultipleButton_Click(**object** sender, EventArgs e) 
2. { 
3.   **this**.openFileDialog1.Filter = 
4. "Images (*.BMP;*.JPG;*.GIF,*.PNG,*.TIFF)|*.BMP;*.JPG;*.GIF;*.PNG;*.TIFF|" + 
5. "All files (*.*)|*.*"; 
6.  
7.   **this**.openFileDialog1.Multiselect = **true**; 
8.   **this**.openFileDialog1.Title = "Select Photos"; 
9.  
10.   DialogResult dr = **this**.openFileDialog1.ShowDialog(); 
11.   **if** (dr == System.Windows.Forms.DialogResult.OK) 
12.   { 
13. ​    **foreach** (String file **in** openFileDialog1.FileNames) 
14. ​    { 
15. ​      **try** 
16. ​      { 
17. ​        PictureBox imageControl = **new** PictureBox(); 
18. ​        imageControl.Height = 400; 
19. ​        imageControl.Width = 400; 
20.  
21. ​        Image.GetThumbnailImageAbort myCallback = 
22. ​            **new** Image.GetThumbnailImageAbort(ThumbnailCallback); 
23. ​        Bitmap myBitmap = **new** Bitmap(file); 
24. ​        Image myThumbnail = myBitmap.GetThumbnailImage(300, 300, 
25. ​          myCallback, IntPtr.Zero); 
26. ​        imageControl.Image = myThumbnail; 
27.  
28. ​        PhotoGallary.Controls.Add(imageControl); 
29. ​      } 
30. ​      **catch** (Exception ex) 
31. ​      { 
32. ​        MessageBox.Show("Error: " + ex.Message); 
33. ​      } 
34. ​    } 
35.   } 
36. } 
37. **public** **bool** ThumbnailCallback() 
38. { 
39.   **return** **false**; 
40. } 

The output looks like Figure 6.

 

![OpenFileDialog control in C#](https://csharpcorner-mindcrackerinc.netdna-ssl.com/UploadFile/mahesh/openfiledialog-in-C-Sharp/Images/FileopendialogOutput.jpg)

 

## Summary

An OpenFileDialog control allows users to launch Windows Open File Dialog and let them select files. In this article, we discussed how to use a Windows Open File Dialog and set its properties in a Windows Forms application. 