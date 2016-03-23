#ORMs
##Mapping Ruby Classes to Database Tab

###MAPPING A CLASS TO A TABLE

When building an ORM to connect our Ruby program to a database, we equate a class with a database table and the instances that the class produces to rows in that table.

Why map classes to tables? Our end goal is to persist information regarding songs to a database. In order to persist that data efficiently and in an organized manner, we need to first map or equate our Ruby class to a database table.

Let's say we are building a music player app that allows users to store their music and browse their songs by song.

This program will have a `Song` class. Each song instance will have a name and an album attribute.

```
class Song
 
  attr_accessor :name, :album
 
  def initialize(name, album)
    @name = name
    @album = album
  end
 
end
```

Here we have an `attr_accessor` for `name` and `album`. In order to "map" this `Song` class to a songs database table, we need to create our database, then we need to create our songs table. In building an ORM, it is conventional to pluralize the name of the class to create the name of the table. Therefore, the `Song` class equals the "songs" table.

###CREATING THE DATABASE

Before we can create a songs table we need to create our music database. Whose responsibility is it to create the database? It is not the responsibility of our `Song` class. Remember, classes are mapped to **tables inside a database**, not to the database as a whole. We may want to build other classes that we equate with other database tables later on.

It is the responsibility of our program as a whole to create and establish the database. Accordingly, you'll see our Ruby programs set up such that they have a `config` directory that contains an `environment.rb` file. This file will look something like this:

```
require 'sqlite3'
require_relative '../lib/song.rb'
 
DB = {:conn => SQLite3::Database.new("db/music.db")}
```

Here we set up a constant, DB, that is equal to a hash that contains our connection to the database. In our lib/song.rb file, we can therefore access the DB constant and the database connection it holds like this:

```
DB[:conn]
```

So, as we move through this reading, let's assume that our hypothetical program has a `config/environment.rb` file and that the `DB[:conn]` constant refers to our connection to the database.

Now that our hypothetical database is set up in our hypothetical program, let's move on to our `Song` class and its equivalent database table.

###CREATING THE TABLE

According to the ORM convention in which a class is mapped to or equated with a database table, we need to create a songs table. We will accomplish this by writing a class method in our `Song` class that creates this table.

**To "map" our class to a database table, we will create a table with the same name as our class and give that table column names that match the attr_accessors of our class.**

Here's an example of a `Song` class that maps instance attributes to table columns:

```
class Song
 
  attr_accessor :name, :album, :id
 
  def initialize(name, album, id=nil)
    @id = id
    @name = name
    @album = album
  end
 
  def self.create_table
    sql =  <<-SQL 
      CREATE TABLE IF NOT EXISTS songs (
        id INTEGER PRIMARY KEY, 
        name TEXT, 
        album TEXT
        )
        SQL
    DB[:conn].execute(sql) 
  end
 
end
```

Let's break down this code.

**THE `ID` ATTRIBUTE**

Notice that we are initializing an individual `Song` instance with an `id` attribute that has a default value of `nil`. Why are we doing this? First of all, songs need an `id` attribute only because they will be saved into the database and we know that each table row needs an `id` value which is the primary key.

When we create a new song with the `Song.new` method, *we do not set that song's id*. A song gets an `id` only when it gets saved into the database (more on inserting songs into the database later). We therefore set the default value of the `id` argument that the `#initialize` method takes equal to `nil`, so that we can create new song instances that *do not have an `id` value*. We'll leave that up to the database to handle later on. Why leave it up to the database? Remember that in the world of relational database, the `id` of a given record must be unique. If we could replicate a record's `id`, we would have a very disorganized database. Only the database itself, through the magic of SQL, can ensure that the `id` of each record is unique.

**THE `.CREATE_TABLE` METHOD**

Above, we created a class method, `.create_table`, that crafts a SQL statement to create a songs table and give that table column names that match the attributes of an individual instance of `Song`. Why is the `.create_table` method a class method? Well, it is *not* the responsibility of an individual song to create the table it will eventually be saved into. It is the job of the class as a whole to create the table that it is mapped to.

**Top-Tip**: For strings that will take up multiple lines in your text editor, use a heredoc to create a string that runs on to multiple lines. To create a heredoc, we use:

`<<-` + `name of language contained in our multiline statement` + `the string, on multiple lines` + `name of language`.

You don't have to use a heredoc, it's just a helpful tool for crafting long strings in Ruby. 

Now that our songs table exists, we can learn how to save data regarding individual songs into that table.

###MAPPING CLASS INSTANCES TO TABLE ROWS

When we say that we are saving data to our database, what data are we referring to? If individual instances of a class are "mapped" to rows in a table, does that mean that the instances themselves, these individual Ruby objects, are saved into the database?

Actually, **we are not saving Ruby objects in our database**. We are going to take the individual attributes of a given instance, in this case a song's name and album, and save *those attributes that describe an individual song* to the database, as one, single row.

For example, let's say we have a song:

```
gold_digger = Song.new("Gold Digger", "Late Registration")
 
gold_digger.name
# => "Gold Digger"
 
gold_digger.album
# => "Late Registration" 
```

This song has it's two attributes, `name` and `album`, set equal to the above values. In order to save the song `gold_digger` into the songs table, we will use the name and album of the song to create a new row in that table. The SQL statement we want to execute would look something like this:

```
INSERT INTO songs (name, album) VALUES ("Gold Digger", "Late Registration")
```

What if we had another song that we wanted to save?

```
hello = Song.new("Hello", "25")
 
hello.name 
# => "Hello"
 
hello.album
# => "25"
```

In order to save `hello` into our database, we do not insert the Ruby object stored in the `hello` variable. Instead, we use `hello`'s name and album values to create a new row in the songs table:

```
INSERT INTO songs (name, album) VALUES ("Hello", "25")
```

We can see that the operation of saving the attributes of a particular song into a database table is common enough. Every time we want to save a record, though, we are repeating the same exact steps and using the same code. The only thing that is different are the values that we are inserting into our songs table. Let's abstract this functionality into an instance method, `#save`.

**INSERTING DATA INTO A TABLE WITH THE `#SAVE` METHOD**

Let's build an instance method, `#save`, that saves a given instance of our `Song` class into the songs table of our database.

```
class Song
 
  def save
    sql = <<- SQL
      INSERT INTO songs (name, album) 
      VALUES (?, ?)
    SQL
 
    DB[:conn].execute(sql, self.name, self.album)
 
  end
end
```

Let's break down the code in this method.

**THE `#SAVE` METHOD**

In order to `INSERT` data into our songs table, we need to craft a SQL `INSERT` statement. Ideally, it would look something like this:

```
INSERT INTO songs (name, album)
VALUES <the given song's name>, <the given song's album>
```

Above, we used the heredoc to craft our multi-line SQL statement. How are we going to pass in, or interpolate, the name and album of a given song into our heredoc?
We use something called **bound parameters**.

**BOUND PARAMETERS**

Bound parameters protect our program from getting confused by [SQL injections](https://en.wikipedia.org/wiki/SQL_injection) and special characters. Instead of interpolating variables into a string of SQL, we are using the `?` characters as placeholders. Then, the special magic provided to us by the SQLite3-Ruby gem's `#execute` method will take the values we pass in as an argument and apply them as the values of the question marks.

**HOW IT WORKS**

So, our `#save` method inserts a record into our database that has the name and album values of the song instance we are trying to save. We are not saving the Ruby object itself. We are creating a new row in our songs table that has the values that characterize that song instance.

**Important**: Notice that we **didn't** insert an ID number into the table with the above statement. Remember that the `INTEGER PRIMARY KEY` datatype will assign and auto-increment the id attribute of each record that gets saved.

**CREATING INSTANCES VS. CREATING TABLE ROWS**

The moment in which we create a new `Song` instance with the `#new` method is **different than the moment in which we save a representation of that song to our database**. The `#new` method creates a new instance of the song class, a new Ruby object. The `#save` method takes the attributes that characterize a given song and save it to the songs table in our database as its own table row.

At what point in time should we actually save a new record? While it is possible to save the record right at the moment the new object is created, i.e. in the `#initialize` method, this is not a great idea. We don't want to force our objects to be saved every time they are created, or make the creation of an object dependent upon/always coupled with saving a record to the database. As our program grows and changes, we may find the need to create objects and not save them. A dependency between instantiating an object and saving that record to the database would preclude this or, at the very least, make it harder to implement.

So, we'll keep our `#initialize` and `#save` methods separate:

```
class Song
 
  attr_accessor :name, :album, :id
 
  def initialize(name, album, id=nil)
    @id = id
    @name = name
    @album = album
  end
 
  def self.create_table
    sql =  <<-SQL 
      CREATE TABLE IF NOT EXISTS songs (
        id INTEGER PRIMARY KEY, 
        name TEXT, 
        album TEXT
        )
        SQL
    DB[:conn].execute(sql) 
  end
 
  def save
    sql = <<-SQL
      INSERT INTO songs (name, album) 
      VALUES (?, ?)
    SQL
 
    DB[:conn].execute(sql, self.name, self.album)
 
  end
 
end
```

Now, we can create and save songs like this:

```
Song.create_table
hello = Song.new("Hello", "25")
ninety_nine_problems = Song.new("99 Problems", "The Black Album")
 
hello.save
ninety_nine_problems.save
```

**GIVING OUR `SONG` INSTANCE AN `ID`**

When we `INSERT` the data concerning a particular `Song` instance into our database table, we create a new row in that table. That row would look something like this:

ID | Name | Album
---|------|------
1 | Hello | 25

Notice that the database table's row has a column for `Name`, `Album` and also `ID`. Recall that we created our table to have a column for the primary key, ID, of a given record. So, as each record gets inserted into the database, it is given an ID number automatically.

In this way, our `hello` instance is stored in the database with the name and album that we gave it, *plus* an ID number that the database assigns to it.

We want our `hello` instance to completely reflect the database row it is associated it, so that we can retrieve it from the table later on with ease. So, once the new row with `hello`'s data is inserted into the table, let's grab the `ID` of that newly inserted row and assign it to be the value of `hello`'s `id` attribute.

```
class Song
 
  attr_accessor :name, :album, :id
 
  def initialize(name, album, id=nil)
    @id = id
    @name = name
    @album = album
  end
 
  def save
    sql = <<- SQL
      INSERT INTO songs (name, album) 
      VALUES (?, ?)
    SQL
 
    DB[:conn].execute(sql, self.name, self.album)
 
    @id = DB[:conn].execute("SELECT last_insert_rowid() FROM songs")[0][0]
 
  end
```

At the end of our `save` method, we use a SQL query to grab the value of the `ID` column of the last inserted row, and set that equal to the given song instance's `id` attribute. Don't worry too much about how that SQL query works for now, we'll learn more about it later. The important thing to understand is the process of:

+ Instantiating a new instance of the `Song` class

+ Inserting a new row into the database table that contains the information regarding that instance

+ Grabbing the `ID` of that newly inserted row and assigned the given `Song` instance's `id` attribute equal to the `ID` of its associated database table row.

Let's revisit our code that instantiated and saved some songs:

```
Song.create_table
hello = Song.new("Hello", "25")
ninety_nine_problems = Song.new("99 Problems", "The Black Album")
 
hello.save
ninety_nine_problems.save
```

Here we:

+ Create the songs table.
+ Create two new song instances.
+ Use the `song.save` method to persist them to the database.

This approach still leaves a little to be desired, however. Here, we have to first create the new song and then save it, every time we want to create and save a song. This is repetitive and tedious. As programmers (you might remember), we are lazy. If we can accomplish something with fewer lines of code we do it. **Any time we see the same code being used again and again, we think about abstracting that code into a method.**

Since first creating an object and then saving a record representing that object is so common. Let's write a method that does just that.

**THE `#CREATE` METHOD**

This method will wrap the code we used above to create a new `Song` instance and save it.

```
class Song
  ...
 
  def self.create(name:, album:)
    song = Song.new(name, album)
    song.save
    song
  end
end
```

Here, we use keyword arguments to pass a name and album into our `#create` method. We use that name and album to instantiate a new song. Then, we use the `#save` method to persist that song to the database.

Notice that at the end of the method, we are returning the `song` instance that we instantiated. The return value of `#create` should always be the object that we created. Why? Imagine you are working with your program and you create a new song:

```
Song.create(name: "Hello", album: "25")
```

Now, we would have to run a separate query on our database to grab the record that we just created. That is way too much work for us. It would be much easier for our `#create` method to simply return the new object for us to work with:

```
song = Song.create(name: "Hello", album: "25")
# => #<Song:0x007f94f2c28ee8 @id=1, @name="Hello", @album="25">
 
song.name
# => "Hello"
 
song.album
# => "25"
```

**CONCLUSION**

The important concept to grasp here is the idea that we are **not** saving Ruby objects into our database. We are using the attributes of a given Ruby object to create a new row in our database table.

Think of it like a game of legos. You have a brand new lego box set to create a lego spaceship. The box comes with legos and instructions. The instructions are like the class: they are the directions for creating new spaceships. The box is like the database: it stores your legos.

You follow the instructions and create a new spaceship object out of individual legos. Then, your parents tell you it is time for bed and you need to put away your legos. You dismantle your spaceship back into its constituent parts and store them in the box––your database. The box doesn't fit the *entire assembled spaceship*, you have to break it down into the pieces out of which you made it and store those instead.

##Mapping Database Tables to Ruby Objects

###OBJECTIVES

+ Build methods that read from a database table
+ Build `Song.all` that returns all songs from the database
+ Build `Song.find_by_name` method that return a song from the database by the song's name
+ Convert what the database gives you into a Ruby object

Our Ruby program gets most interesting when we add data. To do this, we use a database. When we want our Ruby program to store things we send them off to a database. When we want to retrieve those things, we ask the database to send them back to our program. This works very well, but there is one small problem to overcome -- our Ruby program and the database don't speak the same language.

Ruby understands objects. The database understands raw data.

We don't store Ruby objects in the database, and we don't get Ruby objects back from the database. We store the raw data that describes a given Ruby object in a table row and we get back raw data that describes a ruby object when we select from that table.

When we query the database, it is up to us to write the code that takes that data and turns it back into an instance of whatever class. We, the programmers, will be responsible for translating the raw data that the database sends into Ruby objects that are instances of a particular class.

**EXAMPLE**

Let's use our song domain as an example. Imagine we have a `Song` class that is responsible for making songs. Every song will come with two attributes, a `title` and a `length`. We could make a bunch of new songs, but we want to look at all the songs we have that have already been created.

Imagine we have a database with 1 million songs. We need to build three methods to access all of those songs and convert them to Ruby objects.

**`.NEW_FROM_DB`**

The first thing we need to do is convert what the database gives us into a Ruby object. We will use this method to create all the Ruby objects in our next two methods.

The first thing to know is that the database, SQLite in our case, will return an array of data for each row. For example, a row for Michael Jackson's "Thriller" (356 seconds long) that has a db id of 1 would look like this: `[1, "Thriller", 356]`.

```
def self.new_from_db(row)
  new_song = self.new  # self.new is the same as running Song.new
  new_song.id = row[0]
  new_song.name =  row[1]
  new_song.length = row[2]
  new_song  # return the newly created instance
end
```

**`SONG.ALL`**

Now we can start writing our methods to retrieve the data. To return all the songs in the database we need the following SQL query: `SELECT * FROM songs`. Let's store that in a variable called `sql` using a heredoc (`<<-`) since our string will go onto multiple lines.

```
sql = <<-SQL
      SELECT *
      FROM songs
    SQL
```

Next, we will make a call to our database using `DB[:conn]`. This `DB` hash is located in the `config/environment.rb:` `DB = {:conn => SQLite3::Database.new("db/students.db")}`. Notice that the value of the hash is actually a new instance of the `SQLite3::Database class`. This is how we will connect to our database. Our database instance responds to a method called `execute` that accepts raw SQL as a string. Let's pass in that SQL we store above:

```
class Song
  def self.all
    sql = <<-SQL
      SELECT *
      FROM songs
    SQL
 
    DB[:conn].execute(sql)
  end
end
```

This will return an array of rows from the database that match our query. Now, all we have to do is iterate over each row and use the `new_from_db` method to create a new Ruby object for each row.

```
class Song
  def self.all
    sql = <<-SQL
      SELECT *
      FROM songs
    SQL
 
    DB[:conn].execute(sql).map do |row|
      self.new_from_db(row)
    end
  end
end
```

**`SONG.FIND_BY_NAME`**

This one is similar to `Song.all` with the small exception being that we have to include a name in our SQL statement. To do this, we use a question mark where we want name to be passed in, and include the name as the optional argument to the `execute method`.

```
class Song
  def self.find_by_name(name)
    sql = <<-SQL
      SELECT *
      FROM songs
      WHERE name = ?
      LIMIT 1
    SQL
 
    DB[:conn].execute(sql,name).map do |row|
      self.new_from_db(row)
    end.first
  end
end
```
##Updating Records in an ORM

###UPDATING RECORDS

It's hard to imagine a database that would stay totally static and never change. For example, a customer who uses your online market place updates their billing information or makes a new purchase. A user of your social networking site "friends" another user, creating a new association between them. A hospital updates the medical history of one of its patients. In each of these example apps that uses a database, we need to be able to update, or change, the records that are stored in that database.
What do we need to do in order to successfully update a record? First, we need to find the appropriate record. Then, we make some changes to it, and finally, save it once again.

In our Ruby ORM, where attributes of given Ruby objects are stored as an individual row in a database table, we will need to retrieve these attributes, reconstitute them into a Ruby object, make changes to that object using Ruby methods, and then save those (newly updated) attributes back into the database.

Let's walk through this process together.

###UPDATING A RECORD IN A RUBY ORM

For the purposes of this example, we'll be working with a fictitious music management app that allows the user to store their songs. Our app has a `Song` class that maps to a songs database table. Our `Song` class has all the methods it needs to create the songs table, insert records into that table and retrieve records from that table.

**THE `SONG` CLASS**

For this example, we'll assume that our database connection is stored in the `DB[:conn]` constant.

```
class Song
 
attr_accessor :name, :album
attr_reader :id
 
  def initialize(id=nil, name, album)
    @id = id
    @name = name
    @album = album
  end
 
  def save
    sql = <<-SQL
      INSERT INTO songs (name, album) 
      VALUES (?, ?)
    SQL
 
    DB[:conn].execute(sql, self.name, self.album)
    @id = DB[:conn].execute("SELECT last_insert_rowid() FROM songs")[0][0]
  end
 
  def self.create(name:, album:)
    song = Song.new(name, album)
    song.save
    song
  end
 
  def self.find_by_name(name)
    sql = "SELECT * FROM songs WHERE name = ?"
    result = DB[:conn].execute(sql, name)[0]
    Song.new(result[0], result[1], result[2])
  end
end
```

With the `Song` class as defined above, we can create new `Song` instances, save them to the database and retrieve them from the database:

```
ninety_nine_problems = Song.create(name: "99 Problems", album: "The Blueprint")
 
Song.find_by_name("99 Problems")
# => #<Song:0x007f94f2c28ee8 @id=1, @name="99 Problems", @album="The Blueprint">
```

Now that we've seen how to create a `Song` instance, save its attributes to the database, retrieve those attributes and use them to re-create a `Song` instance, let's move on to updating records and objects.

**UPDATING SONGS**

In order to update a record, we must first find it:

```
ninety_nine_problems = Song.find_by_name("99 Problems")
 
ninety_nine_problems.album
# => "The Blueprint"
```

Uh-oh, 99 Problems is off The Black Album, as we all know. Let's fix this.

```
ninety_nine_problems.album = "The Black Album"
 
ninety_nine_problems.album
# => "The Black Album"
```

Much better. Now we need to save this record back into the database:

To do so, we'll need to use an `UPDATE` SQL statement. That statement would look something like this:

```
UPDATE songs 
SET album="The Black Album"
WHERE name="99 Problems";
```

Let's put it all together using our SQLite3-Ruby gem magic. Remember, in this example, we assume our database connection is stored in `DB[:conn]`.

```
sql = "UPDATE songs SET album = ? WHERE name = ?"
 
DB[:conn].execute(sql, ninety_nine_problems.album, ninety_nine_problems.name)
```

Here we've updated the album of a given song. What happens when we want to update some other attribute of a song?
Let's take a look:

```
Song.create(name: "Hella", album: "25")
```

Let's correct the name of the above song from `"Hella"` to `"Hello"`.

```
hello = Song.find_by_name("Hella")
 
sql = "UPDATE songs SET name='Hello' WHERE name = ?"
 
DB[:conn].execute(sql, hello.name)
```

This code is almost exactly the same as the code we used to update the album of the first song. The only difference is in the particular attribute we wanted to update. In the first case, we were updating the album. In this case, we updated the name. Repetitious code has a smell. Let's extract this functionality of updating a record into an method, `#update`.

**THE `#UPDATE` METHOD**

How will we write a method that will allow us to update any attributes of any song? How will we know *which* attributes have been recently updated and which will remain the same?

The best way for us to do this is to simply update *all* the attributes whenever we update a record. That way, we will catch any changed attributes, while the un-changed ones will simply remain the same.

For example:

```
hello = Song.find_by_name("Hella")
 
sql = "UPDATE songs SET name = 'Hello', album = ? WHERE name = ?"
 
DB[:conn].execute(sql, hello.album, hello.name)
```

Here we update *both* the name and album attribute of the song, even though only the name attribute is actually different.

Okay, now that we've solved this problem, let's build our method:

```
class Song
  ...
 
  def update
    sql = "UPDATE songs SET name = ?, album = ? WHERE name = ?"
    DB[:conn].execute(sql, self.name, self.album, self.name)
  end
end
```

Now we can update a song with the following:

```
hello = Song.create(name: "Hella", album: "25")
hello.name = "Hello"
hello.update
```

Wait a second, you might be wondering, how can the `#update` method use `WHERE name = #{self.name}"` if we just changed the name of the song? **Well...it can't!**

The above `#update` method *will not* work if we are trying to update the name of a song. Think about it: If we change the name of `hello` from `"Hella"` to `"Hello"` with the following:

```
hello.name = "Hello"
```

Then the database table doesn't yet know that we changed the name. We haven't saved that change yet so the database row that stores `hello`'s information still has a value of `"Hella"` in the "name" column. So, after the above line of code is executed, our SQL query:

```
sql = "UPDATE songs SET name = ?, album = ? WHERE name = ?"
DB[:conn].execute(sql, self.name, self.album, self.name)
```

Would be interpreted like this:

```
DB[:conn].execute(sql, "Hello", "25", "Hello")
```

And, seeing as our database row still has a value of `"Hella"` in the "name" column, our query would fail to find the correct record and consequently fail to update it.

So using something changeable, like name, to identify the record we want to update, won't work. If only each individual database record and its analogous Ruby object had some kind of unique, un-changing identifier...

That's where the primary key ID of a database record and the `id` attribute of its analogous Ruby object come in.

**IDENTIFYING OBJECTS AND RECORDS USING `ID`**

We need a way to select a Ruby object's analogous table row using some fixed and unique attribute. Song records in the database table have a unique `id`, and our `Song` instances have an `id` attribute. Recall that we have been setting the id attribute of individual songs directly after the data regarding that song gets inserted into the database table, right at the end of our `#save` method. Why?

The unique `id` number of a `Song` instance should *come from the database*. When a song record gets inserted into the database, that row automatically gets assigned a unique ID number. We need to grab that ID number *from the database record* and assign it to the `Song` instance's `id` attribute.

If that sounds confusing, check out this diagram:

![id_diagram](http://readme-pics.s3.amazonaws.com/Untitled%20drawing.png)

Let's break it down:

+ We create a new instance of the `Song` class. That instance has a `name` and `album` attribute. But its `id` attribute is `nil`.
+ The name and album of this song instance are used to create a new database record––a new row in the songs table. That record has an ID of `1` (this would appear to be the first song we've ever saved in our database).
+ The ID of the newly created database record is then taken and assigned to the `id` attribute of the original song object.

What's so great about this? Well, with this pattern, every instance of the `Song` class that is ever saved into the database will be assigned a unique `id` attribute that we can use to differentiate it from the other `Song` objects we created and that we can use to find, retrieve and update unique songs.

Now that we are all convinced that this is the behavior we want to implement, take a closer look at the code that implements it.

**ASSIGNING UNIQUE IDS ON `#SAVE`**

At what point in time should a `Song` instance get assigned a unique `id`? Right after it gets `INSERT`ed into the database. At that point, its equivalent database record will have a unique ID in the ID column. We want to simply grab that ID and use it to assign the `Song` object its `id` value.

When do we `INSERT` a new record into our database? In the `#save` method:

```
def save
  sql = <<-SQL
    INSERT INTO songs (name, album) 
    VALUES (?, ?)
  SQL
  DB[:conn].execute(sql, self.name, self.album)
end
```

Right after we `execute` the SQL `INSERT` statement is an appropriate place to assign our `Song` object its unique `id` from the database.

How do we get the unique ID of the record we just created? We query the database table for the ID of the last inserted row:

```
SELECT last_insert_rowid() FROM songs
```

**Important**: When we execute the above SQL statement using our SQLite3-Ruby gem, we get back something that may feel unexpected:

```
DB[:conn].execute("SELECT last_insert_rowid() FROM songs")
# => [[1]]
```

Recall that whenever we execute SQL statements against our database using the SQLite3-Ruby gem's `#execute` method, we will get back an array of arrays. Here, we used the `last_insert_rowid()` SQL query to request one thing: the last inserted row's ID. Our SQLite3-Ruby gem obliged and gave us an array that contains one array that contains one element––the last inserted row ID. Phew!
So, let's put it all together with our new-and-improved `#save` method:

```
def save
  sql = <<-SQL
    INSERT INTO songs (name, album) 
    VALUES (?, ?)
  SQL
  DB[:conn].execute(sql, self.name, self.album)
  @id = DB[:conn].execute("SELECT last_insert_rowid() FROM songs")[0][0]
end
```

Now let's see what happens when we create a new song:

```
hello = Song.create(name: "Hello", album: "25")
 
hello.name
# => "Hello"
 
hello.album 
# => "25"
 
hello.id
# => 1
```

We did it! Now our individual `Song` objects will get assigned a unique `id` attribute, as soon as they are saved to the database. That means that we can refactor our `#update` method such that it will only update the correct, unique record.

**USING `ID` TO UPDATE RECORDS**

Our `#update` method should identify the correct record to update based on the unique ID that both the song Ruby object and the songs table row share:

```
class Song
  ...
 
  def update
    sql = "UPDATE songs SET name = ?, album = ? WHERE id = ?"
    DB[:conn].execute(sql, self.name, self.album, self.id)
  end
end
```

Now we will never have to worry about accidentally updating the wrong record, or being unable to find a record once we change its name.

**REFACTORING OUR `#SAVE` METHOD TO AVOID DUPLICATION**

Our `#save` method currently looks like this:

```
def save
  sql = <<-SQL
    INSERT INTO songs (name, album) 
    VALUES (?, ?)
  SQL
 
  DB[:conn].execute(sql, self.name, self.album)
  @id = DB[:conn].execute("SELECT last_insert_rowid() FROM songs")[0][0]
end
```

This method will always `INSERT` a new row into the database table. But, what happens if we accidentally call `#save` on an object has already been persisted? That already has an analogous database row?

It would have the effect of creating a new database row with the same attributes as an existing row. The only difference would be the `id` number:

```
hello = Song.new("Hello", "25")
hello.save
 
DB[:conn].execute("SELECT * FROM songs WHERE name = "Hello" AND album = "25")
# => [[1, "Hello", "25"]]
# what happens when we save the same song all over again?
hello.save
DB[:conn].execute("SELECT * FROM songs WHERE name = "Hello" AND album = "25")
# => [[1, "Hello", "25"], [2, "Hello", "25"]]
```

Oh no! We have two records in our songs table that contain the same information. It is clear that our `#save` method needs some failsafes to protect against this kind of thing.

We need our `#save` method to check to see if the object it is being called on has already been persisted. If so, don't `INSERT` a new row into the database, simply update an existing one. Now that we have our handy `#update` method ready to go, this should be easy.

How do we know if an object has been persisted? If it has an `id` that is not `nil`. Remember that an object's `id` attribute gets set only once it has been `INSERT`ed into the database.

Let's take a look at our new `#save` method:

```
def save
  if self.id
    self.update
  else
    sql = <<-SQL
      INSERT INTO songs (name, album) 
      VALUES (?, ?)
    SQL
    DB[:conn].execute(sql, self.name, self.album)
    @id = DB[:conn].execute("SELECT last_insert_rowid() FROM songs")[0][0]
  end 
end
```

Great, now our `#save` method will never create duplicate records!

##Preventing Record Duplication

###THE DREADED DUPLICATION

What happens when two Ruby objects get created using the same attributes? If we are trying to persist representations of such objects to a database, would we end up with essentially identical rows in our table? That would make for a very confusing database and our program would quickly become useless as a way to store and manage information.

For example, lets say we have a Song class that produces individual song objects, each of which have a name and album attribute.

Nothing stops us from creating two objects, each of which have the exact same name and album.

```
hello = Song.new("Hello", "25")
hello_again = Song.new("Hello", "25")
```

What happens when we save these objects to our database?

*For this example, we'll assume our connection to the database is stored in `DB[:conn]`*

```
hello.save
hello_again.save
 
DB[:conn].execute("SELECT * FROM songs WHERE name = "Hello")
# => [[1, "Hello", "25"], [2, "Hello", "25"]]
```

We have two records that contain the same information! How can we avoid this? When we try to save a new `Song` instance, we should first check to see the object we are trying to save already has an equivalent record in the database, if it does, we should simply update it, otherwise, we can go ahead and save it.

###SAVING VS. UPDATING

Let's say we have a song, `hello`:

```
hello = Song.new("Hello", "25")
```

Before we call `#save` on our `hello` object, we need to check and see if a record containing this name and album already exists in the database. The SQL statement to accomplish that would look something like this:

```
SELECT * FROM songs
WHERE name = "Hello", album = "25";
```

If this statement returns a record, we don't need to create a new record, only update the existing one. Otherwise, we need to insert a new record into our database table.

Let's build a method that will allow us to either *find an existing record or create and save* a new one.

**THE `#FIND_OR_CREATE_BY` METHOD**

Take a look at our `Song` class.

```
class Song
 
attr_accessor :name, :album
attr_reader :id
 
  def initialize(id=nil, name, album)
    @id = id
    @name = name
    @album = album
  end
 
  def save
    if self.id 
      self.update
    else
      sql = <<-SQL
        INSERT INTO songs (name, album) 
        VALUES (?, ?)
      SQL
 
      DB[:conn].execute(sql, self.name, self.album)
      @id = DB[:conn].execute("SELECT last_insert_rowid() FROM students")[0][0]
    end
  end
 
  def self.create(name:, album:)
    song = Song.new(name, album)
    song.save
    song
  end
 
  def self.find_by_id(id)
    sql = "SELECT * FROM songs WHERE id = ?"
    result = DB[:conn].execute(sql, id)[0]
    Song.new(result[0], result[1], result[2])
  end
 
  def update
    sql = "UPDATE songs SET name = ?, album = ? WHERE id = ?"
    DB[:conn].execute(sql, self.name, self.album, self.id)
  end
end
```

Let's build our `#find_or_create_by` method:

```
  def self.find_or_create_by(name:, album:)
    song = DB[:conn].execute("SELECT * FROM songs WHERE name = ? AND album = ?", name, album)
    if !song.empty?
      song_data = song[0]
      song = Song.new(song_data[0], song_data[1], song_data[2])
    else
      song = self.create(name: name, album: album)
    end
    song
  end 
```

Let's break this down:

First, we query the database: does a record exist that has this name and album?

```
song = DB[:conn].execute("SELECT * FROM songs WHERE name = ? AND album = ?", name, album)
```

If such a record exists, the `song` variable will now point to an array that would look something like this:

*song name and album name provided as a hypothetical example*

```
[[1, "Hello", "25"]]
```

If this is the case, then the statement: `!song.empty?` will return `true`. Therefore, we will *not* create a new song instance and record, we will instead grab and return the existing song record. That is what is accomplished with these lines:

```
song_data = song[0]
song = Song.new(song_data[0], song_data[1], song_data[2])
```

We grab the `song_data` from the `song` array of arrays, setting `song_data` equal to:

```
[1, "Hello", "25"]
```

Then, we use this array to create a new `Song` instance with the given `id`, `name` and `album`.

However, if no record exists that matches the name and album passed in to this method as arguments, then `!song.empty?` will return `false`, and we will instead create and save a new `Song` instance with the `#create` method.

At the end of our `#find_or_create_by` method, we will return the `song` object that we either found or created.

**OUR CODE IN ACTION**

Now, we can use our `Song` class without worrying about creating duplicate records:

```
Song.find_or_create_by(name: "Hello", album: "25")
Song.find_or_create_by(name: "Hello", album: "25")
 
DB[:conn].execute("SELECT * FROM songs WHERE name = Hello, album = 25")
# => [[1, "Hello", "25"]]
```

Although we called `#find_or_create_by` twice with the same data (gasp!), we only created one record with that data.

##Dynamic ORMs

###WHAT IS A DYNAMIC ORM?

A dynamic ORM allows us to map an existing database table to a class and write methods that can use nothing more than information regarding a specific database table to:

+ Create `attr_accessors` for a Ruby class.
+ Create shareable methods for inserting, updating, selecting and deleting data from the database table.

This pattern, of first creating the database table and having your program do all the work of writing your ORM methods for you, based on that table, is exactly how we will develop web applications in Sinatra and Rails.

###CREATING OUR ORM

**Important**: Writing an ORM is hard! It will require a lot of abstract thinking and we will be doing some metaprogramming. 

**STEP 1: SETTING UP THE DATABASE**
For this exercise, we'll be working with a `Song` class. To create a dynamic ORM, we start by creating our database and songs table. In `config/environment.rb` you'll find the following code:

```
require 'sqlite3'
 
 
DB = {:conn => SQLite3::Database.new("db/songs.db")}
DB[:conn].execute("DROP TABLE IF EXISTS songs")
 
sql = <<-SQL
  CREATE TABLE IF NOT EXISTS songs (
  id INTEGER PRIMARY KEY,
  name TEXT,
  album TEXT
  )
SQL
 
DB[:conn].execute(sql)
DB[:conn].results_as_hash = true
```

Here we are doing a couple of things:

1. Creating the database.
2. Drop `songs` to avoid an error.
3. Creating the `songs` table.

Lastly, we use the `#results_as_hash method`, available to use from the SQLite3-Ruby gem. This method says: when a `SELECT` statement is executed, don't return a database row as an array, return it as a hash with the column names as keys.

So, instead of `DB[:conn].execute("SELECT * FROM songs LIMIT 1")` returning something that looks like this:

```
[[1, "Hello", "25"]]
```

It will return something that looks like this:

```
{"id"=>1, "name"=>"Hello", "album"=>"25", 0 => 1, 1 => "Hello", 2 => "25"}
```

This will be helpful to us as we use information requested from our database table to build attributes and methods on our `Song` class, but more on that later.

Okay, now that we see how our database and table have been set up, let's move onto metaprogramming our `Song` class using information from our database.

**STEP 2: BUILDING `ATTR_ACCESSOR`S FROM COLUMN NAMES**

The next step of building our dynamic ORM is to use the column names of the `songs` table to dynamically create the `attr_accessors` of our `Song` class. In order to do that, we first need to collect the column names from our `songs` table. In order to collect the column names from the songs table we need to tell our `Song` class what table to query. However, we *don't* want to tell the `Song` class to query the `songs` table explicitly. This would not be flexible. If we defined a method that explicitly referenced the `songs` table, we would not be able to extract that method into a *shareable* method later on. Remember, the goal of our dynamic ORM is to define a series of methods that can be shared by *any class*. So, we need to avoid explicitly referencing table and column names.

Now that we understand what we need to do, let's write a method that returns the name of a table, given the name of a class:

**THE `#TABLE_NAME` METHOD**

```
class Song
  def self.table_name
    self.to_s.downcase.pluralize
  end
end
```

This method, which you'll see in the `Song` class in `lib/song.rb`, takes the name of the class, referenced by the `self` keyword, turns it into a string with `#to_s`, downcases (or "un-capitalizes") that string and then "pluralizes" it, or makes it plural.

**Note**: The `#pluralize` method is provided to use the by `active_support/inflector` code library, required at the top of `lib/song.rb`.

Now that we have a method that grabs us the table name we want to query for column names, let's build a method that actually grabs us those column names.

**THE `#COLUMN_NAMES` METHOD**

**Querying a table for column names:**

How do you query a table for the names of it's columns? For this we need to use the following SQL query:

```
PRAGMA table_info(<table name>)
```
**NOTE**: The `PRAGMA` command is a special command used to modify the operation of the SQLite library or to query the library for internal (non-table) data. The `PRAGMA` command is issued using the same interface as other SQLite commands (e.g. `SELECT`, `INSERT`).

This will return to us (thanks to our handy `#results_as_hash` method) an array of hashes describing the table itself. Each hash will contain information about one column. The array of hashes will look something like this:

```
 [{"cid"=>0,
  "name"=>"id",
  "type"=>"INTEGER",
  "notnull"=>0,
  "dflt_value"=>nil,
  "pk"=>1,
  0=>0,
  1=>"id",
  2=>"INTEGER",
  3=>0,
  4=>nil,
  5=>1},
 {"cid"=>1,
  "name"=>"name",
  "type"=>"TEXT",
  "notnull"=>0,
  "dflt_value"=>nil,
  "pk"=>0,
  0=>1,
  1=>"name",
  2=>"TEXT",
  3=>0,
  4=>nil,
  5=>0},
 {"cid"=>2,
  "name"=>"album",
  "type"=>"TEXT",
  "notnull"=>0,
  "dflt_value"=>nil,
  "pk"=>0,
  0=>2,
  1=>"album",
  2=>"TEXT",
  3=>0,
  4=>nil,
  5=>0}]
```

That's a lot of information! The only thing we need to grab out of this hash is the name of each column. Each hash has a `"name"` key that points to a value of the column name.

Now that we know how to get information about each column from our table, let's write our `#column_names` method:

**Building our method:**

```
def self.column_names
  DB[:conn].results_as_hash = true
 
  sql = "pragma table_info('#{table_name}')"
 
  table_info = DB[:conn].execute(sql)
  column_names = []
 
  table_info.each do |row|
    column_names << row["name"]
  end
 
  column_names.compact
end
```

Here we write a SQL statement using the `pragma` keyword and the `#table_name` method (to access the name of the table we are querying). We iterate over the resulting array of hashes to collect *just the name of each column*. We call `#compact` on that just to be safe and get rid of any `nil` values that may end up in our collection.

The return value of calling `Song.column_names` will therefore be:

```
["id", "name", "album"]
```

Now that we have a method that returns us an array of column names, we can use this collection to create the `attr_accessors` of our `Song` class.

**METAPROGRAMMING OUR `ATTR_ACCESSOR`S**

We can tell our `Song` class that it should have an `attr_accessor` named after each column name with the following code:

```
class Song
  def self.table_name
    #table_name code
  end
 
  def self.column_names
    #column_names code
  end
 
  self.column_names.each do |col_name|
    attr_accessor col_name.to_sym
  end
end
```

Here, we iterate over the column names stored in the `column_names` class method and set an `attr_accessor` for each one, making sure to convert the column name string into a symbol with the `#to_sym` method, since `attr_accessors` must be named with symbols.

This is metaprogramming because we are writing code that writes code for us. By setting the `attr_accessor`s in this way, a reader and writer method for each column name is dynamically created, without us ever having to explicitly name each of these methods.

**STEP 3: BUILDING AN ABSTRACT `#INITIALIZE` METHOD**

Now that our `attr_accessors` are defined, we can build the `#initialize` method for the `Song` class. Just like everything else about our dynamic ORM, we want our `#initialize` method to be abstract, i.e. not specific to the `Song` class, so that we can later remove it into a parent class that any other class can inherit from. Once again, we'll use metaprogramming to achieve this.

We want to be able to create a new song like this:

```
song = Song.new(name: "Hello", album: "25")
 
song.name
# => "Hello"
 
song.album
# => "25"
```

So, we need to define our `#initialize` method to take in a hash of named, or keyword, arguments. However, we *don't* want to explicitly name those arguments. Here's how we can do it:

**THE `#INITIALIZE` METHOD**

```
def initialize(options={})
  options.each do |property, value|
    self.send("#{property}=", value)
  end
end
```

Here, we define our method to take in an argument of `options`, which defaults to an empty hash. We expect `#new` to be called with a hash, so when we refer to `options` inside the `#initialize` method, we expect to be operating on a hash.

We iterate over the `options` hash and use our fancy metaprogramming `#send` method to interpolate the name of each hash key as a method that we set equal to that key's value. As long as each `property` has a corresponding `attr_accessor`, this `#initalize` method will work.

**STEP 4: WRITING OUR ORM METHODS**

Let's move on to write some of the conventional ORM methods, like `#save` and `#find_by_name` in a dynamic fashion. In other words, we will write these methods to be abstract, not specific to the `Song` class, so that we can later extract them and share them among any number of classes.

**SAVING RECORDS IN A DYNAMIC MANNER**

Let's take a look at the basic SQL statement required to save a given song record:

```
INSERT INTO songs (name, album)
VALUES 'Hello', '25';
```

In order to write a method that can `INSERT` any record to any table, we need to be able to craft the above SQL statement without explicitly referencing the songs table or column names and without explicitly referencing the values of a given song instance.
Let's take this one step at a time.

**ABSTRACTING THE TABLE NAME**

Luckily for us, we already have a method to give us the table name associated to any given class: `<class name>.table_name`.

Recall, however, that the conventional `#save` is an *instance* method. So, inside a `#save` method, `self` will refer to the instance of the class, not the class itself. In order to use a class method inside an instance method, we need to do the following:

```
def some_instance_method
  self.class.some_class_method
end
```

So, to access the table name we want to `INSERT` into from inside our `#save` method, we will use the following:

```
self.class.table_name
```

We can wrap up this code in a handy method, `#table_name_for_insert`:

```
def table_name_for_insert
  self.class.table_name
end
```

Great, now let's grab our column names in an abstract manner.

**ABSTRACTING THE COLUMN NAMES**

We already have a handy method for grabbing the column names of the table associated with given class:

```
self.class.column_names
```

In the case of our `Song` class, this will return:

```
["id", "name", "album"]
```

There's one problem though. When we `INSERT` a row into a database table for the first time, we *don't* `INSERT` the `id` attribute. In fact, our Ruby object has an `id` of `nil` before it is inserted into the table. The magic of our SQL database handles the creation of an ID for a given table row and then we will use that ID to assign a value to the original object's `id` attribute.

So, when we `save` our Ruby object, we should not include the id column name or insert a value for the id column. Therefore, we need to remove `"id"` from the array of column names returned from the method call above:

```
self.class.column_names.delete_if {|col| col == "id"}
```

This will return:

```
["name", "album"]
```

We're almost there with the list of column names needed to craft our `INSERT` statement. Let's take another look at what the statement needs to look like:

```
INSERT INTO songs (name, album)
VALUES 'Hello', '25';
```

Notice that the column names in the statement are comma separated. Our column names returned by the code above are in an array. Let's turn them into a comma separated list, contained in a string:

```
self.class.column_names.delete_if {|col| col == "id"}.join(", ")
```

This will return:

```
"name, album"
```

Perfect! Now that we have all the code we need to grab a comma separated list of the column names of the table associated with any given class.
We can wrap up this code in a handy method, `#col_names_for_insert`:

```
def col_names_for_insert
  self.class.column_names.delete_if {|col| col == "id"}.join(", ")
end
```

Lastly, we need an abstract way to grab the values we want to insert.

**ABSTRACTING THE VALUES TO INSERT**

When inserting a row into our table, we grab the values to insert by grabbing the values of that instance's `attr_readers`s. How can we grab these values without calling the reader methods by name?

Let's break this down.

We already know that the names of that `attr_accessor` methods were derived from the column names of the table associated to our class. Those column names are stored in the `#column_names` class method.

If only there was some way to invoke those methods, without naming them explicitly, and capture their return values...

In fact, we already know how to programmatically invoke a method, without knowing the exact name of the method, using the `#send` method.

Let's iterate over the column names stored in `#column_names` and use the `#send` method with each individual column name to invoke the method by that same name and capture the return value:

```
values = []
 
self.class.column_names.each do |col_name|
  values << "'#{send(col_name)}'" unless send(col_name).nil?
end
```

Here, we push the return value of invoking a method via the `#send` method, unless that value is `nil` (as it would be for the `id` method before a record is saved, for instance).

Notice that we are wrapping the return value in a string. That is because we are trying to craft a string of SQL. Also notice that each individual value will be enclosed in single quotes, `' '`, inside that string. That is because the final SQL string will need to look like this:

```
INSERT INTO songs (name, album)
VALUES 'Hello', '25';
```

SQL expects us to pass in each column value in single quotes.

The above code, however, will result in a `values` array

```
[" 'the name of the song' ", " 'the album of the song' "]
```

We need comma separated values for our SQL statement. Let's join this array into a string:

```
values.join(", ")
```

Let's wrap up this code in a handy method, `#values_for_insert`:

```
def values_for_insert
  values = []
  self.class.column_names.each do |col_name|
    values << "'#{send(col_name)}'" unless send(col_name).nil?
  end
  values.join(", ")
end
```

Now that we have abstract, flexible ways to grab each of the constituent parts of the SQL statement to save a record, let's put them all together into the `#save` method:

**THE `#SAVE` METHOD:**

```
def save
  sql = "INSERT INTO #{table_name_for_insert} (#{col_names_for_insert}) VALUES (#{values_for_insert})"
 
  DB[:conn].execute(sql)
 
  @id = DB[:conn].execute("SELECT last_insert_rowid() FROM #{table_name_for_insert}")[0][0]
end
```

**SELECTING RECORDS IN A DYNAMIC MANNER**

Now that we have a better understanding of how our dynamic, abstract, ORM works, let's build the `#find_by_name` method.

```
def self.find_by_name(name)
  sql = "SELECT * FROM #{self.table_name} WHERE name = #{name}"
  DB[:conn].execute(sql)
end
```

This method is dynamic and abstract because it does not reference the table name explicitly. Instead it uses the `#table_name` class method we built that will return the table name associated with any given class.

##Dynamic ORMs with Inheritance

###WHY INHERITANCE?

The great thing about a Dynamic ORM is that it provides us with a way to write an ORM that is almost entirely abstract. In other words, the methods we write that allow a given Ruby class and instances to communicate with a database are *not specific to any one class*.

This means we can use such methods again and again. We can define them in only one place and simply make them available, via inheritance, to any other class in our program.

**THE SUPER CLASS**

In this repo, in the lib directory, you'll see the `interactive_record.rb` file. Open it up and take a look.

You'll see that the `InteractiveRecord` class contains almost all of the code responsible for communicating between our Ruby program and our database. All of the methods defined there are abstract––they do not reference explicit class or attribute names nor do they reference explicit table or column names.

These are methods that can be used by *any Ruby class or instance*, as long as we make them available to that class or instance.

**THE CHILD CLASS**

Go ahead and open up `lib/song.rb`. Notice that the `Song` class *inherits* from `InteractiveRecord`. This means that *all* of the methods defined in `InteractiveRecord` are available to `Song`.

The only code the `Song` class needs to contain is the code to create the `attr_accessors` specific to itself. But even that code uses a method, `#column_names`, inherited from the super class.

**OUR CODE IN ACTION**

Open up the executable file in `bin/run`.

```
song = Song.new(name: "Hello", album: "25")
puts "song name: " + song.name
puts "song album: " + song.album
song.save
 
DB[:conn].execute("SELECT * FROM songs")
```

Here we create a new `Song` instance, `puts` out some information about it, save it to the database and then retrieve it from the database. The `#create`, `#save` and `#find_by_name` methods used here were inherited by `Song`, from `InteractiveRecord`.

If you run this file, you should see the following in your terminal:

```
song name: Hello
song album: 25
{"id"=>1, "name"=>"Hello", "album"=>"25", 0=>1, 1=>"Hello", 2=>"25"}
```

LOOKING AHEAD
As we begin to build complex web applications using Sinatra and Rails, this pattern of inheritance will become familiar. In fact, we'll use an ORM tool called Active Record that handles all of the ORM interactions for us, and simply inherit our Ruby classes from Active Record's classes.