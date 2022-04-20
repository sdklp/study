**Inserting, Updating and Removing data using JDatabase**

**Version Note**

Note many examples online use $db->query() instead of $db->execute(). This was the old method in Joomla 1.5 and 2.5 and will throw a deprecated notice in Joomla 3.0+.

This tutorial is split into two independent parts:

·     Inserting, updating and removing data from the database.

·     Selecting data from one or more tables and retrieving it in a variety of different forms.

This section of the documentation looks at inserting, updating and removing data from a database table. To see the other part [click here](https://docs.joomla.org/Special:MyLanguage/Selecting_data_using_JDatabase)

**目录**

 [[隐藏](https://docs.joomla.org/Inserting,_Updating_and_Removing_data_using_JDatabase)] 

·     [1 Introduction](https://docs.joomla.org/Inserting,_Updating_and_Removing_data_using_JDatabase#Introduction)

·     [2 The Query](https://docs.joomla.org/Inserting,_Updating_and_Removing_data_using_JDatabase#The_Query)

·     [3 Inserting a Record](https://docs.joomla.org/Inserting,_Updating_and_Removing_data_using_JDatabase#Inserting_a_Record)

o  [3.1 Using SQL](https://docs.joomla.org/Inserting,_Updating_and_Removing_data_using_JDatabase#Using_SQL)

§ [3.1.1 How to store empty value as NULL](https://docs.joomla.org/Inserting,_Updating_and_Removing_data_using_JDatabase#How_to_store_empty_value_as_NULL)

o  [3.2 Using an Object](https://docs.joomla.org/Inserting,_Updating_and_Removing_data_using_JDatabase#Using_an_Object)

§ [3.2.1 How to store empty value as NULL when insert an object](https://docs.joomla.org/Inserting,_Updating_and_Removing_data_using_JDatabase#How_to_store_empty_value_as_NULL_when_insert_an_object)

·     [4 Updating a Record](https://docs.joomla.org/Inserting,_Updating_and_Removing_data_using_JDatabase#Updating_a_Record)

o  [4.1 Using SQL](https://docs.joomla.org/Inserting,_Updating_and_Removing_data_using_JDatabase#Using_SQL_2)

o  [4.2 Using an Object](https://docs.joomla.org/Inserting,_Updating_and_Removing_data_using_JDatabase#Using_an_Object_2)

·     [5 Deleting a Record](https://docs.joomla.org/Inserting,_Updating_and_Removing_data_using_JDatabase#Deleting_a_Record)

·     [6 Sample Module Code](https://docs.joomla.org/Inserting,_Updating_and_Removing_data_using_JDatabase#Sample_Module_Code)

**Introduction**

Joomla provides a sophisticated database abstraction layer to simplify the usage for third party developers. New versions of the Joomla Platform API provide additional functionality which extends the database layer further, and includes features such as connectors to a greater variety of database servers and the query chaining to improve readability of connection code and simplify SQL coding.

Joomla can use different kinds of SQL database systems and run in a variety of environments with different table-prefixes. In addition to these functions, the class automatically creates the database connection. Besides instantiating the object you need just two lines of code to get a result from the database in a variety of formats. Using the Joomla database layer ensures a maximum of compatibility and flexibility for your extension.

**The Query**

Joomla's database querying has changed since the new Joomla Framework was introduced "query chaining" is now the recommended method for building database queries (although string queries are still supported).

Query chaining refers to a method of connecting a number of methods, one after the other, with each method returning an object that can support the next method, improving readability and simplifying code.

To obtain a new instance of the JDatabaseQuery class we use the JDatabaseDriver getQuery method:

$db = JFactory::getDbo();

 

$query = $db->getQuery(**true**);

The JDatabaseDriver::getQuery takes an optional argument, $new, which can be true or false (the default being false).

To query our data source we can call a number of JDatabaseQuery methods; these methods encapsulate the data source's query language (in most cases SQL), hiding query-specific syntax from the developer and increasing the portability of the developer's source code.

Some of the more frequently used methods include; select, from, join, where and order. There are also methods such as insert, update and delete for modifying records in the data store. By chaining these and other method calls, you can create almost any query against your data store without compromising portability of your code.

**Inserting a Record**

**Using SQL**

The JDatabaseQuery class provides a number of methods for building insert queries, the most common being insert, columns, values.

*// Get a db connection.*

$db = JFactory::getDbo();

 

*// Create a new query object.*

$query = $db->getQuery(**true**);

 

*// Insert columns.*

$columns = **array**('user_id', 'profile_key', 'profile_value', 'ordering');

 

*// Insert values.*

$values = **array**(1001, $db->quote('custom.message'), $db->quote('Inserting a record using insert()'), 1);

 

*// Prepare the insert query.*

$query

  ->insert($db->quoteName('#__user_profiles'))

  ->columns($db->quoteName($columns))

  ->values(implode(',', $values));

 

*// Set the query using our newly populated query object and execute it.*

$db->setQuery($query);

$db->execute();

(Here the quoteName() function adds appropriate quotes around the table and column names to avoid conflicts with any database reserved word, now or in the future.) To get the ID of the row that you just inserted, you can use the 'insertid' method, for example.

*// Get the row that was just inserted*

$new_row_id = $db->insertid();

**How to store empty value as NULL**

If your default value of a column is NULL, you should not add that column name in the array. Let the database system store NULL as the default value. If the default value of a column is not NULL and it is allowed to store NULL values, you should specify that in the code. See how to do it in the following example.

*// Get a db connection.*

$db = JFactory::getDbo();

 

*// Create a new query object.*

$query = $db->getQuery(**true**);

 

*/** First Case [NULL as default value] **/*

*// The column 'profile_value' has NULL as default value. So, we will not add it to the array. The database engine will store NULL as value for column 'profile_value'.*

*// $columns = array('user_id', 'profile_key', 'profile_value', 'ordering');*

$columns = **array**('user_id', 'profile_key', 'ordering');

 

*// Insert values.*

$values = **array**(1001, $db->quote('custom.message'), 1);

 

*/** Second Case [string as default value and you can also store NULL value] **/*

*// The column 'profile_value' has empty string '' as default value but we can also store NULL value. So, we have to add the column name and NULL value to $columns and $values.*

$columns = **array**('user_id', 'profile_key', 'profile_value', 'ordering');

 

*// Insert values.*

$values = **array**(1001, $db->quote('custom.message'), $db->quote('NULL'), 1);

**Using an Object**

The JDatabaseDriver class also provides a convenient method for saving an object directly to the database allowing us to add a record to a table without writing a single line of SQL.

*// Create and populate an object.*

$profile = **new** **stdClass**();

$profile->user_id = 1001;

$profile->profile_key='custom.message';

$profile->profile_value='Inserting a record using insertObject()';

$profile->ordering=1;

 

*// Insert the object into the user profile table.*

$result = JFactory::getDbo()->insertObject('#__user_profiles', $profile);

Notice here that we do not need to escape the table name; the insertObject method does this for us.

The insertObject method will throw a error if there is a problem inserting the record into the database table.

If you are providing a unique primary key value (as in the example above), it is highly recommended that you select from the table by that column value before attempting an insert.

If you are simply inserting the next row in your table (i.e. the database generates a primary key value), you can specify the primary key column-name as the third parameter of the insertObject() method and the method will update the object with the newly generated primary key value.

For example, given the following statement:

$result = $dbconnect->insertObject('#__my_table', $object, 'primary_key');

after execution, $object->primary_key will be updated with the newly inserted row's primary key value.

**HINT:** Set $object->primary_key to null or 0 (zero) before inserting.

**How to store empty value as NULL when insert an object**

If your default value of a column is NULL, you should not add that column name to the object. Let the database system store NULL as the default value. If the default value of a column is not NULL and it is allowed to store NULL values, you should specify that in the code. See how to do it in the following example.

*// Create and populate an object.*

$profile = **new** **stdClass**();

$profile->user_id = 1001;

$profile->profile_key='custom.message';

$profile->ordering=1;

 

*/** First Case [NULL as default value] **/*

*// The column 'profile_value' has NULL as default value. So, we will not add it to the object. The database engine will store NULL as value for column 'profile_value'.*

*// $profile->profile_value='Inserting a record using insertObject()';*

 

*/** Second Case [string as default value and you can also store NULL value] **/*

*// The column 'profile_value' has empty string '' as default value but we can also store NULL value. So, we have to add the column name and NULL value as its value.*

$profile->profile_value = $db->quote('NULL');

 

*// Insert the object into the user profile table.*

$result = JFactory::getDbo()->insertObject('#__user_profiles', $profile);

**Updating a Record**

**Using SQL**

The JDatabaseQuery class also provides methods for building update queries, in particular [update](http://api.joomla.org/11.4/Joomla-Platform/Database/JDatabaseQuery.html#update) and [set](http://api.joomla.org/11.4/Joomla-Platform/Database/JDatabaseQuery.html#set). We also reuse another method which we used when creating select statements, the where method.

$db = JFactory::getDbo();

 

$query = $db->getQuery(**true**);

 

*// Fields to update.*

$fields = **array**(

  $db->quoteName('profile_value') . ' = ' . $db->quote('Updating custom message for user 1001.'),

  $db->quoteName('ordering') . ' = 2',

 

  *// If you would like to store NULL value, you should specify that.*

  $db->quoteName('avatar') . ' = NULL',

);

 

*// Conditions for which records should be updated.*

$conditions = **array**(

  $db->quoteName('user_id') . ' = 42', 

  $db->quoteName('profile_key') . ' = ' . $db->quote('custom.message')

);

 

$query->update($db->quoteName('#__user_profiles'))->set($fields)->where($conditions);

 

$db->setQuery($query);

 

$result = $db->execute();

 

**Using an Object**

Like insertObject, the JDatabaseDriver class provides a convenience method for updating an object.

Below we will update our custom table with new values using an existing id primary key:

$updateNulls = **true**;

 

*// Create an object for the record we are going to update.*

$object = **new** **stdClass**();

 

*// Must be a valid primary key value.*

$object->id = 1;

$object->title = 'My Custom Record';

$object->description = 'A custom record being updated in the database.';

 

*// If you would like to store NULL value, you should specify that.*

$object->short_description = **null**;

 

*// Update their details in the users table using id as the primary key.*

*// You should provide forth parameter with value TRUE, if you would like to store the NULL values.*

$result = JFactory::getDbo()->updateObject('#__custom_table', $object, 'id', $updateNulls);

Just like insertObject, updateObject takes care of escaping table names for us.

The updateObject method will throw a error if there is a problem updating the record into the database table.

We need to ensure that the record already exists before attempting to update it, so we would probably add some kind of record check before executing the updateObject method.

**Deleting a Record**

Finally, there is also a delete method to remove records from the database.

$db = JFactory::getDbo();

 

$query = $db->getQuery(**true**);

 

*// delete all custom keys for user 1001.*

$conditions = **array**(

  $db->quoteName('user_id') . ' = 1001', 

  $db->quoteName('profile_key') . ' = ' . $db->quote('custom.%')

);

 

$query->delete($db->quoteName('#__user_profiles'));

$query->where($conditions);

 

$db->setQuery($query);

 

$result = $db->execute();

**Sample Module Code**

Below is the code for a simple Joomla module which you can install and run to demonstrate use of the JDatabase functionality for updating records in the database, and which you can adapt to experiment with some of the concepts described above. If you are unsure about development and installing a Joomla module then following the tutorial at [Creating a simple module ](https://docs.joomla.org/Special:MyLanguage/J3.x:Creating_a_simple_module/Introduction)will help. **Important note: In any Joomla extensions which you develop that you should avoid accessing the core Joomla tables directly like this and should instead use the Joomla APIs if at all possible, because the database structures may change without warning.** In a folder mod_db_update create the following 2 files:

mod_db_update.xml

<?xml version="1.0" encoding="utf-8"?>

**<extension** type="module" version="3.1" client="site" method="upgrade"**>**

  **<name>**Database update demo**</name>**

  **<version>**1.0.1**</version>**

  **<description>**Code demonstrating use of Joomla Database class to perform SQL UPDATE statements**</description>**

  **<files>**

​    **<filename** module="mod_db_update"**>**mod_db_update.php**</filename>**

  **</files>**

**</extension>**

mod_db_update.php

<?php

defined('_JEXEC') **or** **die**('Restricted Access');

 

**use** Joomla\CMS\Factory;

 

$db = Factory::getDbo();

 

$me = Factory::getUser();

 

**if** ($me->id == 0)

{

​     **echo** "Not logged on!";

}

**else**

{

​     $email = $me->email; 

​     *// change the case of the email address*

​     $email_uppercase = strtoupper($email);

​     **if** ($email == $email_uppercase)

​     {

​         $new_email = strtolower($email);

​     }

​     **else**

​     {

​         $new_email = $email_uppercase;

​     }

​     

​     $query = $db->getQuery(**true**);

 

​     $fields = **array**($db->quoteName('email') . " = '**{**$new_email**}**'");

 

​     $conditions = **array**($db->quoteName('id') . ' = ' . $me->id); 

 

​     $query->update($db->quoteName('#__users'))->set($fields)->where($conditions);

 

​     **echo** $db->replacePrefix((string) $query);

​     

​     $db->setQuery($query);

 

​     **if** ($result = $db->execute())

​     {

​         **echo** "Email case successfully changed!";

​     }

}

The code above updates the email address field in the users record of the currently logged-on user, toggling between upper case and lower case in successive reloads of the web page. The method Factory::getUser() returns the user object of the currently logged-on user, or if not logged on, then a blank user object, whose id field is set to zero. The $db->replacePrefix((string) $query) expression returns the actual SQL statement, and outputting this can be useful in debugging.

Zip up the mod_db_update directory to create mod_db_update.zip.

Within your Joomla administrator go to Install Extensions and via the Upload Package File tab upload this zip file to install this sample log module.

Make this module visible by editing it (click on it within the Modules page) then:

\1.   making its status Published

\2.   selecting a position on the page for it to be shown

\3.   on the menu assignment tab specify the pages it should appear on

When you visit a site web page then you should see the module in your selected position, and it should output the SQL UPDATE statement and affirm that it has updated the record successfully. To confirm that it has updated the record correctly go into phpmyadmin and view the users table within the Joomla database. The updates aren't visible via the admin Users functionality on the back end, as Joomla displays in lower case all the email addresses in the records.

 