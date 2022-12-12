# MongoDB Atlas - Pymongo

## Introduction

In the previous lesson, we introduced the popular NoSQL database __MongoDB__. We executed CRUD operations on a sample dataset using __MongoDB Atlas__, a cloud-based service offered by MongoDB. 

In this lesson, we will expand on your knowledge of MongoDB, cloud computing, and CRUD operations to manipulate data from a MongoDB Atlas database in a Jupyter notebook using the popular Python library, __Pymongo__. This is very similar to the way that sqlite3 allowed us to connect to and work with SQLite databases in previous lessons. 

As a reminder __CRUD__ operations include:

- **_C_**reating a MongoDB database
- **_R_**ead data from a MongoDB database
- **_U_**pdate data from a MongoDB database
- **_D_**elete data from a MongoDB database

## Objectives
You will be able to:
* Connect to a MongoDB Atlas database in a Jupyter notebook using the Python library __Pymongo__
* Perform essential __CRUD operations__ on a MongoDB atlas database with Pymongo
* Construct complex queries to select and filter results with Pymongo

## Connect to MongoDB Atlas in Python
In this section we will import Pymongo to connect to our MongoDB Atlas database in the cloud and interact with it on our local machine in a Jupyter notebook. In the previous we laid the foundation to do this by creating a MongoDB Atlas account and creating a cluster on a shared server. Then, we granted access permissions and downloaded our TLS certificate so that we can access the cluster from our local machine.

We will connect to MongoDB Atlas using the following steps:
* import Pymongo
* retrieve our connection string so we can construct a URI 
* locate the file path for our TLS certificate
* pass the URI and our TLS certificate's path to MongoClient to create a client that is connected to our MongoDB Atlas server

Once we connect, we can begin performing CRUD operations with Pymongo in this Jupyter notebook. To begin using Pymongo, execute the cell below to import the library.


```python
import pymongo
```

### Retrieve the Connection String
1. Navigate to the __Database Deployment__ page on the __MongoDB Atlas UI__ and select __Connect__.
2. Then, select **Connect Your Application** from the menu.

<table><tr><td>
<img src="https://curriculum-content.s3.amazonaws.com/data-science/images/images/mongodb/connect-2.png" alt="A screen shot of the Welcome to Atlas screen on the MongoDB website. The screen contains a form with three questions: What is your goal today? What are you building? What is your preferred language." height=350/>
</td></tr></table>

3. This will open a new screen that displays two questions. Answer them as follows:
    1. __Select your driver and version__
        * Driver: Python
        * Version: 3.6 and above
    2. __Add your connection string into your application code__
        * Select __X.509__
        * Make sure that the checkbox __Include full driver code example__ is __not__ checked.
        * Copy and paste the value generated in the code box. It should look _similar_ to the example provided below (your code snippet will be unique to your database).
        * Then, set the variable `uri` in the code cell below with that value (don't forget quotes!).


```python
# EXAMPLE
uri = "mongodb+srv://cluster0.bdezx80.mongodb.net/?authSource=%24external&authMechanism=MONGODB-X509&retryWrites=true&w=majority"

# Enter your URI snippet below
# uri = ""
```

### Import Pymongo and Connect to a MongoDB Atlas
1. Next, copy and paste the file path for your TLS certificate below. **Hint:** It should have a __.cer__ file extension.


```python
# example
tls_cert = '/Users/christineegan/Documents/bugs/ssh/dsc-mongodb/X509-cert-6927762566522206864.cer'

# tls_cert = None
```

2. Then, run the cell below to create your MongoClient.


```python
myclient = pymongo.MongoClient(uri,
                     tls=True,
                     tlsCertificateKeyFile=tls_cert)
```

Great! Now we are ready to begin performing CRUD operations. Let's start by creating a brand new database.

## Perform CRUD Operations with Pymongo

### Define a New Database in MongoDB Atlas with Pymongo 
1. First, let's take a look at the databases in our server by retrieving a list of names.
2. Run the code cell below, then check to make sure it matches the output in the cell below it.


```python
print(myclient.list_database_names())
```

    ['lab_db', 'sample_airbnb', 'sample_analytics', 'sample_contacts', 'sample_geospatial', 'sample_guides', 'sample_mflix', 'sample_restaurants', 'sample_supplies', 'sample_training', 'sample_weatherdata', 'admin', 'local']



```python
# Output:
# ['sample_airbnb', 'sample_analytics', 'sample_geospatial', 'sample_guides', 'sample_mflix', 'sample_restaurants', 'sample_supplies', 'sample_training', 'sample_weatherdata', 'admin', 'local']
```

3. To add a new database to the server, execute the command in the cell below.


```python
mydb = myclient['sample_contacts']
```

4. Great! Now, let's check the databases again to see if 'example_database' appears. Run the cell below.


```python
print(myclient.list_database_names())
```

    ['lab_db', 'sample_airbnb', 'sample_analytics', 'sample_contacts', 'sample_geospatial', 'sample_guides', 'sample_mflix', 'sample_restaurants', 'sample_supplies', 'sample_training', 'sample_weatherdata', 'admin', 'local']


5. You will notice that the new database does not appear in the list of database names. This is because mongoDB doesn't actually create the new database until we have stored some data in it. 
    * The practice of not doing something until absolutely necessary because another operation needs it is a programming concept called __lazy execution__. 
    * Lazy execution can be an attractive feature when trying to conserve compute resources.
    
    
6. Just as a SQL database has tables, a mongo database has __collections__ of __documents__. 
    * We can get a collection or create a new one by passing its name to the database object we created, just like when we passed the database name to the client object. 

In the cell below, we create a sample collection.


```python
mycollection = mydb['customer_collection']
```

### Create a New Database MongoDB Atlas with Pymongo

We have _defined_ a database that contains a collection, but we still haven't added any data to it yet. Since Pymongo uses lazy execution we know that this means that those data structures have not yet been created. 

To create them, let's add some data to our database and see what we can do with it.


##### Insert a Document with `.insert_one()`
To insert a document (in SQL, we would call this a record) into a collection, we make use of the collection's `__.insert_one() method__`, and pass in the information we want saved as a Python __dictionary__.


```python
example_customer_data = {'name': 'John Doe', 'address': '123 elm street', 'age': 28}

results = mycollection.insert_one(example_customer_data)
results
```




    <pymongo.results.InsertOneResult at 0x7f97a891cd90>



##### Retrieve .inserted_id

The value that was just output by `.insert_one()` is known as a results object. This object contains the unique `_id` of the object we just inserted inside its `.inserted_id` attribute.

To retrieve the `.inserted_id attribute`, you can access it with the following syntax.


```python
results.inserted_id
```




    ObjectId('63975f6b4c7a4b738f1ee223')



##### Insert Many Documents with `.insert_many()`
We can pass a list of Python dictionaries containing customer records to `__.insert_many()__` to insert multiple records into a Mongo database at once.


```python
customer_2 = {'name': 'Jane Doe', 'address': '234 elm street', 'age': 7}
customer_3 = {'name': 'Santa Claus', 'address': 'The North Pole', 'age': 547}
customer_4 = {'name': 'John Doe jr.', 'address': '', 'age': 0.5}

list_of_customers = [customer_2, customer_3, customer_4]

results_2 = mycollection.insert_many(list_of_customers)
```


```python
results_2.inserted_ids
```




    [ObjectId('63975f6b4c7a4b738f1ee224'),
     ObjectId('63975f6b4c7a4b738f1ee225'),
     ObjectId('63975f6b4c7a4b738f1ee226')]



### Construct Queries with PyMongo
This is the most important thing we can know how to do in MongoDB -- actually get the data we need! We do this by reading chunks of the data from the database using queries. 

##### Select and Filter Documents with `.find()` 
Remember the syntax we used to search `sample_airbnb.listingsAndReviews` for rentals that could accomodate at least 4 people and had a room type of "Entire house/apartment"? 

We can use this syntax to construct queries in our Jupyter notebook and pass them to Pymongo's`.find()` method to search `sample_airbnb.listingsAndReviews` in our Jupyter notebook instead of in the MongoDB Atlas UI.

Let's use the same query we used in the MongoDB Atlas UI and practice applying it with `.find()` in Pymongo.

First, run the code cells below to try for yourself. Then we will discuss how you can construct your own queries like the one below to interact with data in MongoDB with PyMongo.


```python
# these are the values from our query in MongoDB Atlas GUI
vals = {'accommodates': {'$gt': 3}, 'room_type': 'Entire home/apt'}
```


```python
# now, lets connect to `sample_airbnb` and verify the total number of documents
airbnb_db = myclient['sample_airbnb']
lr_collection = airbnb_db['listingsAndReviews']
doc_count = lr_collection.count_documents({})
print(f"Total Documents: {doc_count}")

# finally, test our query by passing those values through the find method.
query_x = lr_collection.find(vals)
total = 0
for x in query_x:
    total +=1
    
print(f"Total Results: {total}")
```

    Total Documents: 5555
    Total Results: 2174


#### Selecting Data with .find()
1. The quickest and easiest way to get data from a collection is to use the collection object's `.find()` method! This is how we will __construct queries__ with Pymongo, similar to how we used the __Filter box__ in the previous lesson to construct queries.


```python
# these are the values from our query in MongoDB Atlas GUI
vals = {'accommodates': {'$gt': 3}, 'room_type': 'Entire home/apt'}
```


```python
# now, lets connect to `sample_airbnb` and verify the total number of documents
airbnb_db = myclient['sample_airbnb']
lr_collection = airbnb_db['listingsAndReviews']
doc_count = lr_collection.count_documents({})
print(f"Total Documents: {doc_count}")

# finally, test our query by passing those values through the find method.
query_x = lr_collection.find(vals)
total = 0
for x in query_x:
    total +=1
    
print(f"Total Results: {total}")
```

    Total Documents: 5555
    Total Results: 2174


3. Now, lets apply that idea to read data from the database and collection that we created. The following code cells contains an example of the many ways we can use `.find()` to construct some essential queries.

##### Select All Documents from a Collection


```python
query_1 = mycollection.find({})
for x in query_1:
    print(x)
```

    {'_id': ObjectId('63975f6b4c7a4b738f1ee223'), 'name': 'John Doe', 'address': '123 elm street', 'age': 28}
    {'_id': ObjectId('63975f6b4c7a4b738f1ee224'), 'name': 'Jane Doe', 'address': '234 elm street', 'age': 7}
    {'_id': ObjectId('63975f6b4c7a4b738f1ee225'), 'name': 'Santa Claus', 'address': 'The North Pole', 'age': 547}
    {'_id': ObjectId('63975f6b4c7a4b738f1ee226'), 'name': 'John Doe jr.', 'address': '', 'age': 0.5}


In the cell above, we grabbed every field from every item in the entire collection. There are times where this is probably too much data for it to be useful for us. 

So what if we want to get all the names and addresses for each customer, but not the age? There are two ways we can do this. The first is by passing in a dictionary specifying the fields we want, like so:

##### Select Specific Fields from All Documents in a Collection


```python
query_2 = mycollection.find({}, {'_id': 1, 'name': 1, 'address': 1})
for item in query_2:
    print(item)
```

    {'_id': ObjectId('63975f6b4c7a4b738f1ee223'), 'name': 'John Doe', 'address': '123 elm street'}
    {'_id': ObjectId('63975f6b4c7a4b738f1ee224'), 'name': 'Jane Doe', 'address': '234 elm street'}
    {'_id': ObjectId('63975f6b4c7a4b738f1ee225'), 'name': 'Santa Claus', 'address': 'The North Pole'}
    {'_id': ObjectId('63975f6b4c7a4b738f1ee226'), 'name': 'John Doe jr.', 'address': ''}


In this method, we created a dictionary with the key of every item we want, and a `1` as the value to make clear that we want that field returned. 

##### Select All Fields EXCEPT Specific Fields from All Documents
Conversely, if we'd rather specify the key-value pairs we _don't_ want returned, we can do that too. All we have to do is create a dictionary containing the keys we don't want returned, and set the value for each to `0`, like so:


```python
query_3 = mycollection.find({}, {'age': 0})
for item in query_3:
    print(item)
```

    {'_id': ObjectId('63975f6b4c7a4b738f1ee223'), 'name': 'John Doe', 'address': '123 elm street'}
    {'_id': ObjectId('63975f6b4c7a4b738f1ee224'), 'name': 'Jane Doe', 'address': '234 elm street'}
    {'_id': ObjectId('63975f6b4c7a4b738f1ee225'), 'name': 'Santa Claus', 'address': 'The North Pole'}
    {'_id': ObjectId('63975f6b4c7a4b738f1ee226'), 'name': 'John Doe jr.', 'address': ''}


Note that we can't use both methods at the same time. We have to either specify what we do want, and make sure that every value is a 1, or specify what we don't want, and make sure every corresponding value is a 0. If we pass in a dictionary containing both, we'll get an error in return. 

The one exception to this is the `'_id'` key -- we can set that to 0 or 1 to say if we do or don't want it included. This single value does not need to be the same as all the others in the dictionary. 


#### Filtering Query Results with `.find()`
Obviously, we'll rarely want to get all the records at once. There will be many more times where we'll need to get a single record, or to filter records according to their values. Below, we demonstrate some common modifiers that you can use to filter by value.

##### Filter by Key/Value
For instance, if we know the value for a given key, we can pass that key-value pair (or pairs) into `.find()` as a dictionary, and the results will contain the entire document. 


```python
query_4 = mycollection.find({'name': 'Santa Claus'})
for item in query_4:
    print(item)
```

    {'_id': ObjectId('63975f6b4c7a4b738f1ee225'), 'name': 'Santa Claus', 'address': 'The North Pole', 'age': 547}


##### Filter by Value with Modifiers

We can also filter queries by using **_Modifiers_**. For instance, let's say we wanted to get record for every person in our collection older than 20. We can signify this with the 'greater than' modifier, `"$gt"` and pass in the corresponding value. 


```python
query_5 = mycollection.find({"age": {"$gt": 20}})
for item in query_5:
    print(item)
```

    {'_id': ObjectId('63975f6b4c7a4b738f1ee223'), 'name': 'John Doe', 'address': '123 elm street', 'age': 28}
    {'_id': ObjectId('63975f6b4c7a4b738f1ee225'), 'name': 'Santa Claus', 'address': 'The North Pole', 'age': 547}


We can even pass in **_Regular Expressions_** to filter text data and pattern match! You don't know regular expressions yet, but you will learn them in a few sections. When you do, we encourage you to try using them within a mongodb query!

## Updating Documents

Updating a document in Mongo is just as simple as creating or reading data -- there's a method for that! Updating a record works a bit like filtering a query and getting a record with a specific value, although we also pass in an additional dictionary as the second parameter. This second parameter will contain the modifier `'$set'` as the key, and a dictionary containing the key-value pair we want to update. See the example in the cell below: 


```python
record_to_update = {'name' : 'John Doe'}
update_1 = {'$set': {'age': 29}}
update_2 = {'$set': {'birthday': '02/20/1986'}}

mycollection.update_one(record_to_update, update_1)
mycollection.update_one(record_to_update, update_2)
query_6 = mycollection.find({'name': 'John Doe'})
for item in query_6:
    print(item)
```

    {'_id': ObjectId('63975f6b4c7a4b738f1ee223'), 'name': 'John Doe', 'address': '123 elm street', 'age': 29, 'birthday': '02/20/1986'}


In the cell above, the first update we make updates the value for a key that already exists in the document. The second update adds an entirely new key-value pair to the document. As we can see, the syntax for both is the same (just like when we work with Python dictionaries), and is very easy and intuitive to use. 

### Deleting Records

We can delete records by using the collection object's `.delete_*()` methods. Just like for inserting records, you'll find that we can use `delete_one()` for a single deletion, or `delete_many()` for multiple deletions.

Let's try deleting the record for `'John Doe'`:


```python
deletion_1 = mycollection.delete_one({'name': 'John Doe'})
print(deletion_1.deleted_count)
```

    1


Note that we can also use modifiers here, too! For instance, in the cell below, we'll delete all records for customers younger than 10.


```python
deletion_2 = mycollection.delete_many({'age': {'$lt': 10}})
print(deletion_2.deleted_count)
```

    2



```python
query_6 = mycollection.find({})
for item in query_6:
    print(item)
```

    {'_id': ObjectId('63975f6b4c7a4b738f1ee225'), 'name': 'Santa Claus', 'address': 'The North Pole', 'age': 547}



```python
mycollection.delete_many({})
```




    <pymongo.results.DeleteResult at 0x7f9778139fa0>



**_Note:_** Be _very careful_ when using `delete_many()` -- passing in an empty dictionary will delete the entire collection -- it is the mongoDB equivalent of  `DROP TABLE`, and can really ruin your day if you aren't careful!

## Summary

In this lesson, we learned about how to get mongoDB up and running on our machine, how to connect to it, and how to do some basic CRUD tasks with Python!
