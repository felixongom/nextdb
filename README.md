## Nextdb.

Nextdb is a nextjs library for persisting data just like a database.
It store it's data locally on the server just like sqlite3 would do, but instead it store its data in a json file.
Nextdb is similar to mongodb and can perform both read and write operation.
It can help when building a small application, but for larger project, i would advise going for sql databases or mongodb.

***This is not a state management library***

##### Installation.
Install with npm i nextdb
```js
conts {Nextdb} = require('nextdb)
//OR
import {Nextdb} from "nextdb";
```
Open the api folder and create Db/db.json,
In the db.json add an empty json braces to start with, {} to make it a valid json file,
**Make sure all you json files is in a folder named Db**.

![img](./img//Screenshot%20(17).png)

Instantiate the Nextdb class and start querying the db.
![img](./img//Screenshot%20(20).png)
The above code creates the post collection in db.json.
If you want to create collection or any other collection, just do this;
```js
  //read the file
  let db  = require("./Db/db.json")

  //create the collection
  let post = new Nextdb(db, 'user') 
```
-The first parameter is the json data you want to keep witing.
-Second parameter is the name of the collection you want to create in the json data
-The third and aptional paramerter is boolean (true or false), it defaults to false if not pass.

  False - Means your are dealing with only one json file as database.

  True - Means your are dealing with many json file, each file is representing one collection

```js
  //read the file
  let db  = require("./Db/db.json")

  //create the collection
  let post = new Nextdb(db, 'user') 
  //same as
  let post = new Nextdb(db, 'user', false) 
```

***Passing third paramter as "true"***
```js
  //read the file
  let userCollecion  = require("./Db/user.json")
  let post = new Nextdb(db, 'user', true) 
```
This will remaind nextdb to create a separate file for each collection, this is a little smater since nextdb read and write files which may take longer than normal as the json file grows if we were using only one file;

![img](./img//Screenshot%20(19).png)

#### NB:
Make sure the name of the json file you are requiring is similar to the collection name(second parameter to the constractor) if you are using many files (true);
Eg:
1. user.json
```js
  let userCollection  = require("./Db/user.json")
  let user = new Nextdb(db, 'user', true) 
```
2. post.json
```js
  let postCollection = require("./Db/post.json")
  let post = new Nextdb(db, 'post', true) 
```

#### Nextdb methods.
Methods are similar to mongodb methods

```js
let db = require("./Db/db.json")

//creating Db/db.json
let post = new Nextdb(db, 'post') 
```
##### **.create()**
```js
//writing data to post colletion in the database
let result = post.create({title:'post title 1',body:'i am post 1'})

```

.create() returns an object which will look like this;
```js
{
  id: 1,
  title:'post title1',
  body:'i am post 1',
  views:2,
  createdAt: '2024-01-08T09:39:09.976Z',
  updatedAt: '2024-01-08T09:39:09.976Z'
}

```
**Its that simple**

It auto asign createdAt, updatedAt and id to it as it saves to database.
The id  is an autoincreamenting id.

***As i told you earlier that the methods are similar to mongodb methods***, We also have methods like;

##### **.findById(id)**
```js
post.findById(1)
```
It returns the document with id 1 from posts collection.

##### **.findOne()**
```js
post.findOne({name:{$has:'jo'}})
```
It returns the first document with that matches the query


##### **.find()**
.find() method ends with .get() .eg
```js
post.find().get()
```
It returns an array of all the documents from posts collection.
```js
[
    {
        id: 1,
        title:'post title1',
        body:'i am post 1',
        views:2,
        createdAt: '2024-01-08T09:39:09.976Z',
        updatedAt: '2024-01-08T09:39:09.976Z'
    },
    {
        id: 2,
        title:'post title2',
        body:'i am post 2'
        views:8,
        createdAt: '2024-01-08T09:39:09.976Z',
        updatedAt: '2024-01-08T09:39:09.976Z'
    },
]
```
Also you can request for  particular kind of data like
```js
post.find({views:2}).get()
post.find({views:2, title:{$has:'post'}}).get()
```
We also have the **sort()** and **limit()**
```js
post.find().sort({id:1}).get() 
//1 for ascending  and -1 for for descending

post.find().limit(5).get() 
//it retuns 5 documents

post.find({views:{$gt:20}}).sort(id:-1).limit(5).get() 
/*it retuns 5 documents mating the criteria 
sorted in descending order basing on id 
*/
```
**The sort() and limit() have ***no orders*** in which they are called** like in mongodb.

```js
post.find().sort(id:-1).limit(5).get() 
post.find().limit(5).sort(id:-1).get() 
//the results for the above are the same
```

##### **.update()**

```js
post.update(2, {title:'updated title'})
/*update the title of document with id 2 
to 'updated title' and returns the updated document
*/
```

##### **.findAndUpdate()**

```js
post.findOneAndUpdate({views:10}, {title:'updated title'})
/*update the title of the first match
 to 'updated title'.
*/
```

##### **.findOneAndUpdate()**

```js
post.findAndUpdate({views:10}, {title:'updated title'})
/*update the title of all the matches to 'updated title'
*/
```

##### **.delete()**

```js
post.delete(2)
//deletes document with id 2

```

##### **.findOneAndDelete()**

```js
post.findOneAndDelete({title:{$has:'updated'}})
//deletes first match of the query
```

##### **.findAndDelete()**

```js
post.findAndDelete({title:{$has:'updated'}})
//deletes all the matches of the query

```

##### **.last()**

```js
post.last() 
post.last({title:{$search:'updated'}})
//both returns the last match of the query
```

##### **.countDocuments()**

```js
post.countDocuments() //1000
post.countDocuments({title:{$search:'updated'}}) //25
/*
counts the number of documents that matches the query
*/
```

##### **.paginate()**
This method has .get() at the end. This returns the data together with some metedata, call the get at the end
```js
post.paginate().get()
//defaults to page 1 and count of 12

post.paginate().page(2).get() 
//returns page 2 and count of 12

post.paginate().page(2).count(5).get()
//returns page 2 and count of 5 

post.paginate({age:10},{tile:false}).page(2).count(5).get()
//returns page 2 and count of 5 
```
The result look like this.
```js
{
  page: 1,
  par_page: 12,
  has_next: false,
  has_prev: false,
  next_page: 2,
  prev_page: null,
  num_pages: 1,
  result: [
    {
      post: "post1",
      id: 1,
      createdAt: "2024-01-08T10:26:27.158Z",
      updatedAt: "2024-01-08T10:26:27.158Z"
    },
    {
      post: "post2",
      id: 2,
      createdAt: "2024-01-08T10:26:28.629Z",
      updatedAt: "2024-01-08T10:26:28.629Z"
    }
  ] 
}
```

**Some of the different ways of filter data are;**

***$lt***
```js
people.find({age:{$lt:18}}).get()
//returns people whose age is less than 18
```

***$lte***
```js
people.find({age:{$lte:18}}).get()
//returns people whose age is less than or equal to 18
```

***$gt***
```js
people.find({age:{$gt:18}}).get()
//returns people whose age is greater than to 18
```
***$gte***
```js
people.find({age:{$gte:18}}).get()
//returns people whose age is greater than or equal to 18
```

***$eq***
```js
people.find({age:{$eq:18}}).get()
//returns people whose age is equal to 18
```

***$neq***
```js
people.find({age:{$neq:18}}).get()
//returns people whose age is not equal to 18
```

***$has***
```js 
people.find({country:{$has:'ug'}}).get()
//people whose country is has ug in their name
```

***$hasNo***
```js
people.find({country:{$hasNo:'ug'}}).get()
//people whose country has no ug in their name
```

***$sw***
```js
food.find({name:{$sw:'ap'}}).get()
//foods whose name starts with ap 
```

***$ew***
```js
food.find({name:{$ew:'ilk'}}).get()
//foods whose name end with ilk
```

***$nsw***
```js
food.find({name:{$nsw:'ap'}}).get()
//foods whose name is not starting with ap 
```

***$new***
```js
food.find({name:{$ew:'ilk'}}).get()
//foods whose name is not ending with ilk
```


***$in***
```js
food.find({prices:{$in:5}}).get()
//for array fields
//foods whose prices list has 5
```

***$nin***
```js
food.find({prices:{$nin:5}}).get()
//for array fields
//foods whose prices list has no 5
```

***$or***
```js
food.find({name:{$or:['pizza', 'buger']}}).get()
//foods whose name is pizza or burger
```

***$or***
```js
food.find({name:{$nor:['pizza', 'buger']}}).get()
//foods whose name is not pizza or burger
```

You can have complex queries like
```js
user.find({
    country:'canada',
    first_name:{$has:'ni'},
    last_name:{$has:'jo'},
    age:{$gte:20},
}).get()
//returns array

user
.find({id:{$gt:5}})
.find(first_name:{$sw:'nick'})
.get()
 //returns a number

user
.find({id:{$gt:5}})
.countDocuments()
 //returns a number
```
##### NB:
-Both .paginate() and .find() methods require you to call a get method at the end to return the data.

-In both .paginate() and .find() , you can call any method of you choice if .get() has not yet been called.

Both .paginate() and .find() takes in second optional parameter which spacifies which attribute you want back or not, all attributes will be return by default
```js
user.find({},{password:true})
user.find({country:'USA'},{password:false})
//returns results without password attribute
```
