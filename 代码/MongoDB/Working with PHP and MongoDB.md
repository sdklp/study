**Working with PHP and MongoDB**

MongoDB is an open-source document database that provides high performance, high availability and automatic scaling. MongoDB is also a cross-platform document-oriented database, classified as a NoSQL database. MongoDB tries to avoid the traditional table-based relational database structure in favor of JSON-like documents with dynamic schemas (MongoDB calls the format BSON), making the integration of data in certain types of applications easier and faster.

**Install PHP MongoDB driver on Windows the Wamp way**

To access MongoDB from PHP you need to correctly install the following:

   •  The MongoDB server

   •  The MongoDB PHP driver

a.) [**Download**](https://www.mongodb.org/) the 32bit or 64 bit version to match your OS and the WampServer (32/64) version you are running. Then, to install it all you needl to do is to extract the files from the zip and place the bin folder somewhere on your system. As you can see in the figure below, I put it in C:wampbinmongomongo2.6.10bin:

![img](file:///C:/Users/klp/AppData/Local/Temp/msohtmlclip1/01/clip_image001.png)

Create a data folder on c:datadb, if you want a specific data folder path, you need to set the dbpath yourself.

Next, to test the MongoDB server, we need to open two command prompts (both run as administrator): one for the MondoDB server (mongod command) and one for the client (mongo command):

![img](file:///C:/Users/klp/AppData/Local/Temp/msohtmlclip1/01/clip_image003.jpg)

Notice that the server runs on the 27017 port and it is opened for connections.

The client connects to the server and opens the MongoDB shell. The help command lists all the mongoDB available commands:

![img](file:///C:/Users/klp/AppData/Local/Temp/msohtmlclip1/01/clip_image004.png)

![img](file:///C:/Users/klp/AppData/Local/Temp/msohtmlclip1/01/clip_image005.jpg)

To see the MongoDB databases, enter the show dbs command, to use one of it, enter the use comedy command and to see all the collection from the comedy database use the show collections command, as you can see from the above figure.

b.) To install the MongoDB PHP driver follow the next few steps:

Download the driver from [**http://pecl.php.net/package/mongo**](https://pecl.php.net/package/mongo). Select the version you want and click the DLL word from the ‘Downloads’ column.

![img](file:///C:/Users/klp/AppData/Local/Temp/msohtmlclip1/01/clip_image007.jpg)

So, for the 2.6.10 Mongo DB server version, I choose the 1.6.10 MongoDB PHP driver. I have installed the PHP 5.5, and 64 bit version for my OS, so I will download the pointed version.

![img](file:///C:/Users/klp/AppData/Local/Temp/msohtmlclip1/01/clip_image008.png)

Extract the zip file, and copy php_mongo.dll to your PHP folder C:wamp\bin\php\php5.5.12\ext. Edit the php.ini file to add the php_mongo.dll new extension (extension=php_mongo.dll). To use Mongo with your web server (Apache) use the wampmanager menus to edit php.ini and add this extension also in there:

![img](file:///C:/Users/klp/AppData/Local/Temp/msohtmlclip1/01/clip_image009.jpg)

Restart all the services from Wamp and go on Localhost page, then you should now see a section entitled ‘mongo’ this means that the PHP MONGO extension is active.

![img](file:///C:/Users/klp/AppData/Local/Temp/msohtmlclip1/01/clip_image011.jpg)

Using phpinfo() you will find the mongo specifications:

![img](file:///C:/Users/klp/AppData/Local/Temp/msohtmlclip1/01/clip_image013.jpg)

**Accessing MongoDB from PHP**

**1.) Connecting to a MongoDB database from PHP scripts:**

For connecting to a MongoDB database from PHP you can use the instances of the MongoClient() class. The first example in this article connects to MongoDB, selects the comedy database and lists all the documents from the cartoons collection:

```
<?php
// connect to MongoDB
$m = new MongoClient();
// select a database
$db = $m->comedy;
// select a collection 
$collection = $db->cartoons;
//returns a cursor for the search results
$cursor = $collection->find();
// iterate through the results
foreach ($cursor as $document) {
	     echo '"_id": '.$document["_id"]."<br />";
         echo '"title": '.$document["title"]."<br />";
         echo '"author": '.$document["author"]."<br />";
} 
?> 
```

![img](file:///C:/Users/klp/AppData/Local/Temp/msohtmlclip1/01/clip_image015.png)

The outputs of listing the documents placed into cartoons collection, using PHP code and the mongo Command Prompt.

**2.) Insert a document into a collection:**

To insert a new document into a collection you can use MongoCollection::insert( array|object $document [, array $options = array()] ) method. The document parameter can be an array or object and the options parameter is an array of options for the insert operation.

The listing below inserts a new document into the cartoons collection and then shows the results:

```
<?php
// connect to MongoDB
$m = new MongoClient();
// select a database
$db = $m->comedy;
// select a collection 
$collection = $db->cartoons;
$document = array( 
      "title" => "Hibernate and MongoDB", 
      "author" => "Anghel Leonard"
   );
//insert a new document
  $collection->insert($document); 
//returns a cursor for the search results
$cursor = $collection->find();
// iterate through the results
foreach ($cursor as $document) {
    echo '"_id": '.$document["_id"]."<br />";
         echo '"title": '.$document["title"]."<br />";
         echo '"author": '.$document["author"]."<br />";
         echo '*********************************';
} 
?>
```

![img](file:///C:/Users/klp/AppData/Local/Temp/msohtmlclip1/01/clip_image017.jpg)

**3.) Removing and updating a document from a collection:**

For removing a record from a collection use the remove() methods which has as arguments: criteria – query the criteria for the documents to delete and options – an array of options for the remove operation (as an example of available options is “justOne” – Specify TRUE to limit deletion to just one document. If FALSE or omitted, all documents matching the criteria will be deleted). To update records based on a given criteria, use the update ( array $criteria , array $new_object [, array$options = array() ] ) method, where the criteria parameter represents the query criteria for the documents to update and new_object parameter represents the object used to update the matched documents. The below example use both methods described above:

```
<?php
// connect to MongoDB
$m = new MongoClient();
// select a database
$db = $m->comedy;
// select a collection 
$collection = $db->cartoons;
//remove a document 
$collection->remove(array("title"=>"Calvin and Hobbes"));
//update a document
$collection->update(array("title"=>"Hibernate and MongoDB"), array('$set'=>array("title"=>"Mastering JavaServer Faces 2.2"))); 
//returns a cursor for the search results
$cursor = $collection->find();
// iterate through the results
foreach ($cursor as $document) {
    echo '"_id": '.$document["_id"]."<br />";
         echo '"title": '.$document["title"]."<br />";
         echo '"author": '.$document["author"]."<br />";
         echo '***************************************';
} 
?> 
```

The output is:

![img](file:///C:/Users/klp/AppData/Local/Temp/msohtmlclip1/01/clip_image019.png)

**4.) Creating a new database**

Using the PHP MongoDB driver we can also create new database. So, to do that we need to use the public MongoDB::__construct ( MongoClient $conn , string $name ) method. This method is not meant to be called directly, the preferred way to create an instance of MongoDB is through MongoClient::__get() or MongoClient::selectDB(). The new database created, that you can see in the below example, is named Books and it will contain a collection, named also Books containing two fields: title and author and six documents inserted also in the below listing. In the next section of this article we will see how to add a new collection to the Books database.

```
<?php
// connect to MongoDB
$connection = new MongoClient();
$db = $connection->Books;
//the new Database should now exist, with no user Collections created yet
$list = $db->listCollections();
foreach ($list as $collection) {
    echo "$collection </br>";       
}
//select the Books collection
$books = $db->Books;
//insert new documents into the Books collection
$books->insert(array("title" => "Mastering JavaServer Faces 2.2", "author" => "Anghel Leonard"));
$books->insert(array("title" => "JSF 2.0 Cookbook", "author" => "Anghel Leonard"));
$books->insert(array("title" => "Rapid PrimeFaces", "author" => "Anghel Leonard"));
$books->insert(array("title" => "JBoss Tools 3 Developers Guide", "author" => "Anghel Leonard"));
$books->insert(array("title" => "Pro Hibernate and MongoDB", "author" => "Anghel Leonard"));
$books->insert(array("title" => "Pro Java 7 NIO.2", "author" => "Anghel Leonard"));
//list the documents from the Books collection
$cursor = $collection->find();
// iterate through the results
foreach ($cursor as $document) {
    echo '"_id": '.$document["_id"]."<br />";
    echo '"title": '.$document["title"]."<br />";
    echo '"author": '.$document["author"]."<br />";
    echo '***************************************'."<br />";
} 
?> 
```

The output of the above example is:

![img](file:///C:/Users/klp/AppData/Local/Temp/msohtmlclip1/01/clip_image021.png)

**5.) Creating a new Collection**

As I said above, in this section we will create a new collection to the Books database and it will be called “publisher”. To do that, we need the public MongoCollection MongoDB::createCollection ( string $name [, array $options ] ) method, which takes as arguments: the name of the collection and the option array containing options for the collections. Some of the most important options are: capped – If the collection should be a fixed size, size – If the collection is fixed size, max – If the collection is fixed size, the maximum number of elements to store in the collection, which you will see also in the next example:

```
<?php
$connection = new MongoClient();
$db = $connection->Books;
$publisher = $db->createCollection(
    "publisher",
    array(
        'capped' => true,
        'size' => 10*1024,
        'max' => 10
    )
);
$books = $db->Books;
$list = $db->listCollections();
foreach ($list as $collection) {
    echo "$collection </br>";     
}
?> 
```

The output is:

![img](file:///C:/Users/klp/AppData/Local/Temp/msohtmlclip1/01/clip_image022.gif)

**6.) Sorting field into a collection**

If we want to sort a field, ascending or descending, we should use the public MongoCursor MongoCursor::sort ( array $fields ) – sorts the results by given fields. Each element in the array has as key the field name, and as value either 1 for ascending sort, or -1 for descending sort. In the next example, we will sort ascending the title field, and the result will be listed below:

```
<?php
// connect to MongoDB
$m = new MongoClient();
// select a database
$db = $m->Books;
// select a collection 
$collection = $db->Books; 
//returns a cursor for the search results
$cursor = $collection->find();
//sorting the title fields ascending
$cursor->sort(array('title' => 1));
// iterate through the results
foreach ($cursor as $document) {
    echo '"_id": '.$document["_id"]."<br />";
    echo '"title": '.$document["title"]."<br />";
    echo '"author": '.$document["author"]."<br />";
    echo '***************************************'."<br />";
} 
?> 
```

The output is:

![img](file:///C:/Users/klp/AppData/Local/Temp/msohtmlclip1/01/clip_image023.png)

**Conclusion**

This article has shown you how to install the MongoDB server and the PHP MongoDB driver for them to work well together. You also saw how to use MongoDB CRUD operation from PHP, using the implicit MongoDB database and a newly created database.

 