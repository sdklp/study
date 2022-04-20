# SQL Server存储过程带参数和通配符

使用带有参数的简单过程

　　Create PROCEDURE au_info
      @lastname varchar(40),
      @firstname varchar(20)
　　AS
　　Select au_lname, au_fname, title, pub_name
      FROM authors a INNER JOIN titleauthor ta
      ON a.au_id = ta.au_id INNER JOIN titles t
      ON t.title_id = ta.title_id INNER JOIN publishers p
      ON t.pub_id = p.pub_id
      Where  au_fname = @firstname
      AND au_lname = @lastname
　　GO

　　au_info 存储过程可以通过以下方法执行：

　　EXECUTE au_info 'Dull', 'Ann'
　　-- or
　　EXECUTE au_info @lastname = 'Dull', @firstname = 'Ann'
　　-- or
　　EXECUTE au_info @firstname = 'Ann', @lastname = 'Dull'
　　-- or
　　EXEC au_info 'Dull', 'Ann'
　　-- or
　　EXEC au_info @lastname = 'Dull', @firstname = 'Ann'
　　-- or
　　EXEC au_info @firstname = 'Ann', @lastname = 'Dull'

　　如果该过程是批处理中的第一条语句，则可使用：

　　au_info 'Dull', 'Ann'
　　-- or
　　au_info @lastname = 'Dull', @firstname = 'Ann'
　　-- or
　　au_info @firstname = 'Ann', @lastname = 'Dull'

 

使用带有通配符参数的简单过程

　　Create PROCEDURE au_info2
　　@lastname varchar(30) = 'D%',
　　@firstname varchar(18) = '%'
　　AS
　　Select au_lname, au_fname, title, pub_name
　　FROM authors a INNER JOIN titleauthor ta
　　   ON a.au_id = ta.au_id INNER JOIN titles t
　　   ON t.title_id = ta.title_id INNER JOIN publishers p
　　   ON t.pub_id = p.pub_id
　　Where au_fname LIKE @firstname
　　   AND au_lname LIKE @lastname
　　GO

　　au_info2 存储过程可以用多种组合执行。下面只列出了部分组合：

　　EXECUTE au_info2
　　-- or
　　EXECUTE au_info2 'Wh%'
　　-- or
　　EXECUTE au_info2 @firstname = 'A%'
　　-- or
　　EXECUTE au_info2 '[CK]ars[OE]n'
　　-- or
　　EXECUTE au_info2 'Hunter', 'Sheryl'
　　-- or
　　EXECUTE au_info2 'H%', 'S%'