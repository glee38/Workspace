#Table Relations
##Relational Databases

Let's say that you've been hired by a big and important company to do the payroll for all of their employees. We'll call it MyFace (inspired by nothing in particular). Every two weeks, you need to look up each and every employee and how much they get paid, and send them a check *and* send a notice of that check to their manager (managers, after all, should know when their employees are getting paid).

In addition, let's say that managers get paid every month, instead of every two weeks. So, every month we need to go through the spreadsheet again and find *just the managers* this time, and send them *their* checks. In such a situation, we would need a place to store all of the managers and employees.

Using a spreadsheet, your storage system might look something like this:

![storage](http://readme-pics.s3.amazonaws.com/Screen%20Shot%202015-09-03%20at%205.12.12%20PM.png)

So every two weeks, we would have to look through every single entry in this spreadsheet, send each person their check and then, figure out a way to identify an employee's manager to send that manager a confirmation that each employee has been paid. We need some way to *associate* the employees to their manager. We could add a "Manager" column to the spreadsheet that would be filled out with the name of that person's manager (if that person is an employee and not a manager themselves). This is getting messy. Not only do we have to do a lot of searching through the spreadsheet and manual detection of who is an employee and who is a manager, but we also have to match each employee with the name of their manager. If only there was some way to simplify our system!

Enter relational databases. A relation database, simply put, is **a database structured to recognize relations among stored items of information**. In such a system, it would be easy to tell an employee that they *belong to* a certain manager and to tell a manager that they *have many* employees. This might sound familiar if you've built object oriented Ruby programs in which instances of a class are related to one another. A relational database will allow us to store representations of our Ruby objects and preserve the relationships between those objects when we store them.

**RELATIONAL DATABASE STRUCTURE**

Continuing with our payroll example from earlier, employees and managers would be stored in their own tables. A table is like a spreadsheet, it has columns and rows.

Our managers table would look something like this:

![manageers](http://readme-pics.s3.amazonaws.com/Screen%20Shot%202015-09-04%20at%2011.17.31%20AM.png)

And our employees table would look something like this:

![employees](http://readme-pics.s3.amazonaws.com/Screen%20Shot%202015-09-04%20at%2011.17.25%20AM.png)

Our employees table has a "Manager ID" column, filled with the ID number of that person's manager. In a relational database, every row has a number, called a **primary key**. Relationships between tables can be established by using a **foreign key** column, like our "Manager ID" column, that uses that primary key of another table to refer to a member of that table.

Why should our foreign key, our point of reference between an employee and his or her manager, be a number? Why not just use the manager's name? Well, names are very rarely unique. What if MyFace hires a new manager, also named Steve? It's a popular name, after all. How would our database know *which* Steve manages which employees. Primary keys, on the other hand, **are always unique**!

Additionally, what if Steve gets sick of sharing his name with all of the other Steves out there and decides to change his name to Brittany? We would have to look up every single employee that had Steve as a manager and change a "Manager Name" column to "Brittany". We are programmers, we're lazy, we like to code for the future and our databases are no exception. If we use primary keys, i.e. numbers that never change and always refer to the same manager, our database can accommodate something like a manager's name change with ease.

**CODE ALONG I: RELATING CATS TO OWNERS**

Let's say we are creating an app that helps a veterinary office manage the pets it sees as patients and the owners of those pets. Let's say this vet is very particular and specializes in cats. Our app will have a database that has a `cats` table and an `owners` table. We will need a way to relate, or connect, these two tables such that a given cat is associated to its owner and a given owner is associated to the cat (or cats) it owns.
For this exercise, we'll be working with a `pets_database`. In your terminal, create the database with the `sqlite3 pets_database.db command`. Let's set up our two tables now.

**STEP 1: CREATING THE CATS TABLE**
In the `sqlite3>` prompt in your terminal, create the table with the following statement:

```
CREATE TABLE cats (
id INTEGER PRIMARY KEY,
name TEXT,
age INTEGER,
breed TEXT);
```

Now, go ahead and insert the following cats into the table:

```
INSERT INTO cats (name, age, breed)
VALUES ("Maru", 3, "Scottish Fold");
 
INSERT INTO cats (name, age, breed)
VALUES ("Hana", 1, "Tabby");
```

**STEP 2: CREATING THE OWNERS TABLE**
First, we need to create our owners table. An owner should have an ID that is a primary key integer and a name that is text:

```
CREATE TABLE owners (id INTEGER PRIMARY KEY, name TEXT);
```

Now that we have our owners table, we can add a foreign key column to the pets table.

**STEP 3: ADD FOREIGN KEY TO CATS TABLE**
Use the following statement to add this column:

```
ALTER TABLE cats ADD COLUMN owner_id INTEGER;
```

Check your `cats` schema with `.schema` and you should see the following:

```
CREATE TABLE cats (
id INTEGER PRIMARY KEY,
name TEXT,
age INTEGER,
breed TEXT,
owner_id INTEGER);
```

Great, now we're ready to associate cats to their owners by creating an owner and assigning that owner's ID to certain cat's `owner_id` column.

**STEP 3: ASSOCIATING CATS TO OWNERS**
First, let's make a new owner:

```
INSERT INTO owners (name) VALUES ("mugumogu");
```

Check that we did that correctly with the following statement:

```
SELECT * FROM owners;
```

You should see the following:

```
1 | mugumogu
```

Mugumogu is the owner of both Hana and Maru. Let's create our associations:

```
UPDATE cats SET owner_id = 1 WHERE name = "Maru";
UPDATE cats SET owner_id = 1 WHERE name = "Hana";
```

Let's check out our updated `cats` table:

```
SELECT * FROM cats WHERE owner_id = 1;
```

This should return:

```
1 | Maru | 3 | Scottish Fold | 1
2 | Hana | 1 | Tabby         | 1
```

**ESTABLISHING FOREIGN KEY: DETERMINING WHICH TABLE GETS A "FOREIGN KEY" COLUMN**
Why did we decide to give our `cats` table the foreign key column and not the `owners` table? Similarly, in the example from the beginning of this exercise, why would we give a `posts` table a foreign key of `user_id` and not the other way around?

Let's look at what would happen if we tried to add cats directly to the `owners` table.

Adding the first cat, "Maru", to the owner "mugumogu" would look something like this:

id | name | cat_id
---|------|-------
1|mugumogu|1

So far so good. But what happens when we need to add a second cat, "Hana", to the same owner?

id | name	| cat_id1	| cat_id2
---|------|---------|--------
1|mugumogu|1|2

What if this owner gets yet another cat? We'd have to keep growing our table horizontally, potentially forever. That is not efficient, or organized.

We can also think about the relationship between our owners and our cats in the context of a "has many" and "belongs to" relationship. An owner can have many cats, but (for the purposes of this example), a cat can only belong to one owner. Similarly, a user can have many posts that they've written but each post was written by, and therefore belongs to, only one user.

The thing that "has many" is considered to be the parent. The thing that "belongs to" we'll call the child. The child table gets the foreign key column, the value of which is the primary key of that data's/row's parent.

##SQL JOINS

###WHAT IS A JOIN?
A SQL JOIN clause is a way to combine rows from two or more tables, based on a common column between them. The great thing about relational databases is that they are just that––*relational*. Relational databases allow us not only to store data that is interconnected, but to retrieve that data in ways that reflect that interconnectivity.

Let's say, for example, we have two tables, a Cats table and an Owners table. Cats and owners are associated by a foreign key of `owner_id` in the Cats table. How would we craft a query that would grab us all of the cats with a particular owner, and even include information about that owner in the data returned to us by that query? We know how to write a `SELECT` statement that gets us all of the cats with a particular `owner_id`. For example:

```
SELECT * FROM cats WHERE owner_id = 2
```

This would return us the appropriate list of cats. But what if we wanted to query both the Cats and the Owners tables and return information about **both** cats and owners? This is where JOIN statements come in.

**JOIN TYPES**

There are several different types of joins that we will cover in this lesson. The following JOIN keywords will be crafted into `SELECT` statements to achieve the described return values.

Type | Description
-----|------------
INNER JOIN|Returns all rows when there is at least one match in BOTH tables
LEFT JOIN|Returns all rows from the left table, and the matched rows from the right table
RIGHT JOIN|Returns all rows from the right table, and the matched rows from the left table
FULL JOIN|Returns all rows when there is a match in ONE of the tables

In the following code-along, we'll be crafting JOIN statements of each of the above types in order to return data about a series of connected database tables.
For this code-along, we'll be working with a database of pets and owners. Let's set it up:

**SETTING UP THE DATABASE**

Create your database in your terminal with the following command:

```
sqlite3 pets_database.db
```

Now, inside the `sqlite3>` prompt, create the following tables and insert the values:

**Cats Table:**

```
CREATE TABLE cats (
id INTEGER PRIMARY KEY,
name TEXT,
age INTEGER,
breed TEXT,
owner_id INTEGER);
```

**Owners Table:**

```
CREATE TABLE owners (id INTEGER PRIMARY KEY, name TEXT);
```

**Insert Data:**

```
INSERT INTO owners (name) VALUES ("mugumogu");
INSERT INTO owners (name) VALUES ("Sophie");
INSERT INTO cats (name, age, breed, owner_id) VALUES ("Maru", 3, "Scottish Fold", 1);
INSERT INTO cats (name, age, breed, owner_id) VALUES ("Hana", 3, "Tabby", 1);
INSERT INTO cats (name, age, breed, owner_id) VALUES ("Nona", 4, "Tortoiseshell", 2);
INSERT INTO cats (name, age, breed) VALUES ("Lil' Bub", 2, "perma-kitten");
```
 
**A NOTE ON FOREIGN KEYS**

Note that the Cats table has an `owner_id` column. This column is a **foreign key** that connects each cat to an individual owner. If an individual cat has an `owner_id` of `2`, that indicates that that cat belongs to the owner who has an `id` of `2`.

To confirm this, you can run the following `SELECT` statement in your `sqlite3` prompt:

```
SELECT * FROM cats WHERE owner_id = 2;
```

You should see just one cat returned to us, the one that belongs to Sophie, our second owner:

```
id               name             age         owner_id    breed        
---------------  ---------------  ----------  ----------  -------------
3                Nona             4           2           Tortoiseshell
```

###CODE ALONG I: INNER JOIN

**DEFINITION**

An INNER JOIN query will return *all* the rows from both tables you are querying where a certain condition is met. In other words, INNER JOIN will select all rows from both tables as long as there is a match between the specified columns of each table.

Let's take a look at a boiler-plate INNER JOIN statement, before we try it out on our `pets_database`.

```
SELECT column_name(s)
FROM first_table
INNER JOIN second_table
ON first_table.column_name=second_table.column_name;
```

This may not make sense to you just yet. Let's try it out with our own database in order to gain a better understanding.

**WRITING INNER JOINS**

Enter into your database via `sqlite3 pets_database.db`, if you're not there already. Let's write an INNER JOIN query that will return the name and breed of the cat along with the name of that cat's owner.

```
SELECT Cats.name, Cats.breed, Owners.name 
FROM Cats 
INNER JOIN Owners
ON Cats.owner_id = Owners.id;
```

Let's break this down:

```
SELECT Cats.name, Cats.breed, Owners.name ...
```

Here, we are specifying which columns from each table we want to select data from. We use the `table_name.column_name` notation to grab columns from two different tables.

Next up, we join our two tables together with our INNER JOIN keyword:

```
...FROM Cats INNER JOIN Owners
```

Lastly, we tell our query how to connect, or join, the two tables. In other words, we tell our query which columns in each table function as the foreign key/primary key connection. Through this, our query will correctly identify which cat belongs to which owner and return that information accordingly.

```
...ON Cats.owner_id = Owners.id;
```

Here, we are telling our query that the `owner_id` column on the Cats table is filled with data that corresponds to data in the `id` column of the Owners table. We are indicating that a value of `1`, for example, in an individual cat's `owner_id` column refers to the individual owner who has an `id` of `1`. And we are telling our query to return *only* those cats and owners who share this connection.

The above statement should return the following:

```
name             breed            name
---------------  ---------------  ----------
Maru             Scottish Fold    mugumogu  
Hana             Tabby            mugumogu  
Nona             Tortoiseshell    Sophie  
```

We did it! We wrote an INNER JOIN query that returns to us all of the data in the specified columns from both tables.

Notice that the owner's name column is called `name` in the output above. That is because we requested the `name` column from the Owners table. For this particular output though, it would be great if the column could read "owners_name", to distinguish it from the cat's name column.

Let's run that query again, this time aliasing the `name` column of the Owners table as `owners_name`, using the `AS` keyword:

```
SELECT Cats.name, Cats.breed, Owners.name 
AS "owner_name" 
FROM Cats 
INNER JOIN Owners 
ON Cats.owner_id = Owners.id;
```

This should return:

```
name             breed            owner_name
---------------  ---------------  ----------
Maru             Scottish Fold    mugumogu  
Hana             Tabby            mugumogu  
Nona             Tortoiseshell    Sophie 
```

**A NOTE ON INNER JOINS, OR, WHERE'S LIL' BUB?**

When we say that an INNER JOIN returns all of the data for which a certain condition is true, we mean that any data that does not meet a JOIN condition will not be returned. The JOIN condition, in this case, is the thing that our two tables are joined *on*:

```
...ON Cats.owner_id = Owners.id;
```

Our query, therefore, will select all of the appropriate data concerning cats and owners who *are* joined by an `owner_id`/`id` foreign key/primary key relationship. In other words, it will select all of the cats who have a value in the `owner_id` column that matches a value in the `id` column of the Owners table. Any cats that have an empty `owner_id` column, or have a value in that column that does not match the `id` of an existing owner, will not be selected by the query.

You might have noticed that the data returned by our query did not include Lil' Bub. That's because when we inserted Lil' Bub into our Cats table, we didn't give her an `owner_id`.

Other types of JOIN statements, however, can return such data.

###CODE ALONG II: LEFT OUTER JOIN
I don't know about you, but I miss Lil' Bub. It would be nice if we could query our database for both cat and owner information *without* excluding her. With a LEFT OUTER JOIN we can do just that.

**DEFINITION**

A LEFT OUTER JOIN query returns **all** rows from the left, or first, table, regardless of whether or not they met the JOIN condition. The query will also return the matched data from the right, or second, table.

In the case of data from the first table that doesn't meet our JOIN condition, the resulting output will include `NULL`, or empty, values for the missing matched columns.

Let's take a look at a boiler-plate LEFT OUTER JOIN:

```
SELECT column_name(s)
FROM first_table
LEFT JOIN second_table
ON first_table.column_name=second_table.column_name;
```

Now let's try it out on our `pets_database`.

**WRITING LEFT OUTER JOINS**

Execute the following command in your `sqlite3>` prompt in your terminal:

```
SELECT Cats.name, Cats.breed, Owners.name 
FROM Cats 
LEFT OUTER JOIN Owners 
ON Cats.owner_id = Owners.id;
```

You should see the following output returned to you:

```
name             breed            name      
---------------  ---------------  ----------
Maru             Scottish Fold    mugumogu  
Hana             Tabby            mugumogu  
Nona             Tortoiseshell    Sophie 
Lil' Bub         perma-kitten              
```
  
Here, our LEFT OUTER JOIN has returned to us *all* of the cats (including Lil' Bub!), with matched data regarding owner's name for those cats that have an owner, and empty space in the owner's name column for the cat that doesn't have an owner.

**RIGHT OUTER JOIN AND FULL OUTER JOIN**

**Important**: SQLite doesn't currently support RIGHT OUTER JOINs or FULL OUTER JOINs. However, we'll review it briefly here so you can see how it works in other Databases, like Postgres. This section isn't a code-along, just read it through and try to get comfortable with the code provided.

**RIGHT OUTER JOIN**

The RIGHT OUTER JOIN is the reverse of the LEFT OUTER JOIN. It will return **all** data from the right, or second, table and the matched data from the left, or first table.

Let's take a look a boiler-plate RIGHT OUTER JOIN query:

```
SELECT column_name(s)
FROM first_table
RIGHT JOIN table2
ON first_table.column_name=second_table.column_name;
```

Before we (pretend to) write our own RIGHT OUTER JOIN, let's insert a new owner into our Owners table:

```
INSERT INTO owners (name) VALUES ("Penny");
```

Now we have an owner who is not currently associated to a cat. This gives us something to work with to illustrate our RIGHT OUTER JOIN.

**WRITING RIGHT OUTER JOINS**

The following query would constitute a RIGHT OUTER JOIN:

```
SELECT Cats.name, Cats.breed, Owners.name 
FROM Cats 
RIGHT OUTER JOIN Owners 
ON Cats.owner_id = Owners.id;
```

This would return:

```
name             breed            name      
---------------  ---------------  ----------
Maru             Scottish Fold    mugumogu  
Hana             Tabby            mugumogu  
Nona             Tortoiseshell    Sophie    
                                  Penny  
```

Notice that Lil' Bub is once again missing, but our cat-less owner, Penny, is present and accounted for. That is because the RIGHT OUTER JOIN will select **all** of the data from the second table and only the matched data from the first table.

**FULL OUTER JOIN**

FULL OUTER JOIN queries will combine the result of both a LEFT and RIGHT OUTER JOIN. In other words, they will return *all* the data from both the first and second tables.

Here's a boiler-plate example:

```
SELECT column_name(s)
FROM first_table
FULL OUTER JOIN second_table
ON first_table.column_name=second_table.column_name;
```

**WRITING FULL OUTER JOINS**

A FULL OUTER JOIN for our Cats and Owners tables would look like this:

```
SELECT Cats.name, Cats.breed, Owners.name
FROM Cats
FULL OUTER JOIN Owners
ON Cats.owner_id = Owner.id;
```

It would return:

```
name             breed            name      
---------------  ---------------  ----------
Maru             Scottish Fold    mugumogu  
Hana             Tabby            mugumogu  
Nona             Tortoiseshell    Sophie 
Lil' Bub         perma-kitten 
                                  Penny  
```
           
Our result includes both cats without owners and owners without cats. In other words, it includes **all** of our data.

##Table Relations: SQL Join Tables

###DATA RELATIONS

As programmers, we understand the data we are manipulating to be real. In other words, we write programs to solve real-world problems or handle real-world situations. Whether you're developing a web application that helps doctors and hospitals around the country manage patient information or coding a command line game, the code we write is modeled on real situations and things. This is true of our databases and tables as well as code we write in Ruby, Objective C, you name it.

We use databases to store information that represents real-world entities. We might have an employee database in which an individual row in an Employees table represents one real, individual employee. Such a database might also have a Managers table that is related to the employees table. Real-world objects and environments are relational. Employees belong to managers, pets belong to owners, a person has many friends. Our relational databases have the ability to reflect that related-ness.

In relational databases, we can actually categorize the type of relationship that exists between the data that we are storing. There are two basic types of relationship that we need to concern ourselves with right now: The "has many"/"belongs to" relationship and the "many-to-many" relationship. Let's take a closer look.

**THE "HAS MANY"/"BELONGS TO" RELATIONSHIP**
Let's imagine a database that keeps track of pets and their owners. This Pet's Database has an Owners table and a Cats table. The Cats table has a column, `owner_id`, that contains a foreign key corresponding to the `id` column of the Owners table.

In this way, an individual cat is associated to the person that owns it. Any number of cats can have the same owner. In other words, any number of cats can have the same `owner_id`.

Let's say we have the following owners:

```
id               name      
---------------  ----------
1                mugumogu  
2                Sophie    
3                Penny   
```

And the following cats:

```
id               name        age         owner_id    breed        
---------------  ----------  ----------  ----------  -------------
1                Maru        3           1           Scottish Fold
2                Hana        1           1           Tabby        
3                Nona        4           2           Tortoiseshell
4                Lil' Bub    2                       perma-kitten 
```

Note that both Maru and Hana have the same value in the `owner_id` column, a value of `1`. This corresponds to the owner from the Owners table who has an `id` of `1`. Both Maru and Hana, therefore, have the same owner: mugumogu. If we run a query to select all of the cats whose `owner_id` is `1`, we'll return both Maru and Hana.
The following query:

```
sqlite> SELECT * FROM cats WHERE owner_id = 1;
```

returns:

```
id               name        age         owner_id    breed        
---------------  ----------  ----------  ----------  -------------
1                Maru        3           1           Scottish Fold
2                Hana        1           1           Tabby
```
        
Our first owner, mugumogu, *has many* cats. Both Hana and Maru *belong* to mugumogu. This is the "has many"/"belongs to" relationship.

**ENACTING THE RELATIONSHIP THROUGH FOREIGN KEYS**

The "has many"/"belongs to" relationship is created through the use of foreign keys. The Cats table has an `owner_id` column which is the foreign key column. It contains information that corresponds to the `id` column of the Owners table.

The table that contains the foreign key column is the table that contains the entities that "belong to" another entity. The table that is referenced via the foreign key is the parent or owner entity that "has many" of something else. This relationship works because multiple entities in the "belonging" or child table can have the same foreign key.

What happens, though, when a cat realizes it can live the good life by hanging out with the family across the street for extra food and care? Such a cat would have *more than one owner*. Our "has many"/"belongs to" relationship is no longer adequate.

How might we account for a cat with many owners? Well, we could continue to add additional `owner_id` columns to the cats table. For example we could add an `owner_id_1`, `owner_id_2`, `owner_id_3` column and so on. This is not practical however. It requires us to change our schema by continuing to add columns every time a cat gains a new owner. This means our Cats table could grow to contain a possibly infinite number of columns (some cats are very popular, after all).

We can avoid this undesirable horizontal table growth with the use of a join table.

**JOIN TABLES AND THE "MANY-TO-MANY" RELATIONSHIP**

A **join table** contains common fields from two or more other tables. In this way, it creates a many-to-many relationship between data. Let's take a closer look at this concept by building our own join table in the following code-along.

###CODE ALONG I: BUILDING A JOIN TABLE

We want to create a many-to-many association between cats and owners, such that a cat can have many owners and an owner can have many cats. Our join table will therefore have two columns, one for each of the tables we want to relate. We will have a `cat_id` column and an `owner_id` column.

Let's set up our database to get started:

**SETTING UP THE DATABASE**

In your terminal, create the Pet's Database by running the following command:

```
sqlite3 pets_database.db
```

Create the following two tables:

**Cats Table**:

```
CREATE TABLE cats (
id INTEGER PRIMARY KEY,
name TEXT,
age INTEGER,
breed TEXT
);
```

**Owners Table**:

```
CREATE TABLE owners (id INTEGER PRIMARY KEY, name TEXT);
```

Insert the following data:

**Insert Data**:

```
INSERT INTO owners (name) VALUES ("mugumogu");
INSERT INTO owners (name) VALUES ("Sophie");
INSERT INTO owners (name) VALUES ("Penny");
INSERT INTO cats (name, age, breed) VALUES ("Maru", 3, "Scottish Fold");
INSERT INTO cats (name, age, breed) VALUES ("Hana", 3, "Tabby");
INSERT INTO cats (name, age, breed) VALUES ("Nona", 4, "Tortoiseshell" );
INSERT INTO cats (name, age, breed) VALUES ("Lil' Bub", 2, "perma-kitten");
```

**THE `CATS_OWNERS` JOIN TABLE**

**CREATING THE TABLE**

Now we're ready to create our join table. Since our table is creating a many-to-many relationship between cats and owners, we will call our table `cats_owners`. It is conventional to name your join tables using the names of the tables you are creating the many-to-many relationship between.

Inside the `sqlite3>` in your terminal, execute the following SQL statement to create our join table:

```
CREATE TABLE cats_owners (
cat_id INTEGER,
owner_id INTEGER
);
```

Let's confirm that worked by checking our schema:

```
sqlite3> .schema
CREATE TABLE cats (
id INTEGER PRIMARY KEY, 
name TEXT, 
age INTEGER,
owner_id INTEGER, breed TEXT);
 
CREATE TABLE owners (id INTEGER PRIMARY KEY, name TEXT);
 
CREATE TABLE cats_owners (
cat_id INTEGER,
owner_id INTEGER
);
```

Great! Now we're ready to start inserting some rows into our join table.

**INSERTING DATA INTO THE JOIN TABLE**

Each row in our join table will represent one cat/owner relationship. Let's say, for example, that Nona the cat has acquired a second owner, Penny. Now we want to represent that Nona has two owners, Sophie and Penny.

First, we'll insert the Nona/Sophie relationship into our join table. Recall that Nona the cat has an `id` of `3` and Sophie the owner has an `id` of `2`.

```
INSERT INTO cats_owners (cat_id, owner_id) VALUES (3, 2);
```

Now let's check the contents of our `cats_owners` table with a SELECT statement:

```
SELECT * FROM cats_owners;
```

This should return:

```
cat_id           owner_id  
---------------  ----------
3                2  
```

Now let's insert the Nona/Penny relationship into our join table:

```
INSERT INTO cats_owners (cat_id, owner_id) VALUES (3, 3);
```

We'll confirm this insertion with another SELECT statement:

```
SELECT * FROM cats_owners;
```

This should return:

```
cat_id           owner_id  
---------------  ----------
3                2         
3                3    
```

Now our table reflects that Nona, the cat with an `id` of `3`, has many (in this case two) owners.

The great thing about our join table, however, is that it allows for the many-to-many relationship. We have a cat with many owners, now let's insert a row that will give a particular owner many cats.

Sophie's dream has come true and now she is a co-owner of Maru the cat. Let's insert the appropriate row into our join table. Remember that Sophie has an `id` of `2` and Maru has an `id` of `1`. Let's insert that row:

```
INSERT INTO cats_owners (cat_id, owner_id) VALUES (1, 2);
```

Let's run a SELECT statement to confirm that our insertion worked:

```
SELECT * FROM cats_owners;
```

This should return:

```
cat_id           owner_id  
---------------  ----------
3                2         
3                3         
1                2    
```

Nona, our cat with an `id` of `3` has may owners and Sophie, our owner with an `id` of `2`, has many cats. Our many-to-many relationship is up and running.

Now let's take advantage of this association by running some queries that utilize our join table to return information about these complex relationships.

###CODE ALONG II: QUERYING THE JOIN TABLE

**BASIC QUERIES**

Let's SELECT from our join table all of the owners who are associated to cat number 3.

```
 SELECT cats_owners.owner_id 
 FROM cats_owners 
 WHERE cat_id = 3;
```

This should return:

```
owner_id       
---------------
2              
3       
```

Now let's SELECT all of the cats who are associated with owner number 2:

```
SELECT cats_owners.cat_id 
FROM cats_owners 
WHERE owner_id = 2;
```

That should return:

```
cat_id         
---------------
3              
1   
```

These queries are great, but it would be even better if we could write queries that would return us some further information about the cats and owners we are returning here, such as their names. Otherwise it becomes a little difficult to constantly remember cats and owners by ID only. We can do so by queries our join tables using JOIN statements.

**ADVANCED QUERIES**

Execute the following query in your `sqlite3>` prompt in your terminal:

```
SELECT Owners.name 
FROM Owners 
INNER JOIN cats_owners 
ON Owners.id = cats_owners.owner_id WHERE cats_owners.cat_id = 3;
```

This should return:

```
name           
---------------
Sophie         
Penny  
```

Let's break down the above query:

+ `SELECT Owners.name` - Here, we declare the column data that we want to actually have returned to us.
+ `FROM Owners` - Here, we specify the table whose column we are querying.
+ `INNER JOIN cats_owners ON Owners.id = cats_owners.owner_id` - Here, we are joining the `cats_owners` table on the `Owners` table. We are telling our query to look for owners whose `id` column matches up to the `owner_id` column in the `cats_owners` table.
+ `WHERE cats_owners.cat_id = 3;` - Here, we are adding an additional condition to our query. We are telling our query to look at the `cats_owners` table rows where the value of the `cat_id` column is `3`. Then, for those rows only, cross reference the `owner_id` column value with the `id` column in the Owners table.

Let's take a look at a boiler-plate query that utilizing a JOIN statement to query a join table:

```
SELECT column(s)
FROM table_one
INNER JOIN table_two
ON table_one.column_name = table_two.column_name
WHERE table_two.column_name = condition;
```

Giving this one more try, let's query the join table for the names of all of the cats owned by Sophie:

```
SELECT Cats.name
FROM Cats
INNER JOIN cats_owners
ON Cats.id = cats_owners.cat_id
WHERE cats_owners.owner_id = 2;
```

This should return:

```
name           
---------------
Nona           
Maru 
```

##Table Relations: Grouping and Sorting Data

###GROUPING AND SORTING DATA

SQL isn't picky about how it returns data to you, based on your queries. It will simply return the relevant table rows in the order in which they exist in the table. This is often insufficient for the purposes of data analysis and organization.

How common is it to order a list of items alphabetically? Or numerically from least to greatest?

We can tell our SQL queries and aggregate functions to group and sort our data using a number of clauses:

+ `ORDER BY()`
+ `LIMIT`
+ `GROUP BY()`
+ `HAVING` and `WHERE`
+ `ASC`/`DESC`

Let's take a closer look at how we use these keywords to narrow our search criteria as well as to order and group it.

**SETTING UP THE DATABASE**

Some cats are very famous, and accordingly very wealthy. Our Pet's Database will have a Cats table in which each cat has a name, age, breed and net worth. Our database will also have an Owners table and cats_owners join table so that a cat can have many owners and an owner can have many cats.

Creating the Database:

Create the database in your terminal with the following:

```
sqlite3 pets_database.db
```

Creating the tables:

In the `sqlite3>` prompt in your terminal:

Cats table:

```
CREATE TABLE cats (
id INTEGER PRIMARY KEY,
name TEXT,
age INTEGER,
breed TEXT, 
net_worth INTEGER
);
```

Owners Table:

```
CREATE TABLE owners (id INTEGER PRIMARY KEY, name TEXT);
Cats Owners Table:
CREATE TABLE cats_owners (
cat_id INTEGER,
owner_id INTEGER
);
```

Inserting the values:

Cats:

```
INSERT INTO cats (id, name, age, breed, net_worth) VALUES (1, "Maru", 3, "Scottish Fold", 1000000);
INSERT INTO cats (id, name, age, breed, net_worth) VALUES (2, "Hana", 1, "Tabby", 21800);
INSERT INTO cats (id, name, age, breed, net_worth) VALUES (3, "Grumpy Cat", 4, "Persian", 181600);
INSERT INTO cats (id, name, age, breed, net_worth) VALUES (4, "Lil' Bub", 2, "Tortoiseshell", 2000000);
```

Owners:

```
INSERT INTO owners (name) VALUES ("mugumogu");
INSERT INTO owners (name) VALUES ("Sophie");
INSERT INTO owners (name) VALUES ("Penny");
```

Cats Owners:

```
INSERT INTO cats_owners (cat_id, owner_id) VALUES (3, 2);
INSERT INTO cats_owners (cat_id, owner_id) VALUES (3, 3);
INSERT INTO cats_owners (cat_id, owner_id) VALUES (1, 2);
```

###CODE ALONG I: `ORDER BY()`

**SYNTAX**

```
SELECT column_name, column_name
FROM table_name
ORDER BY column_name ASC|DESC, column_name ASC|DESC;
```

`ORDER BY()` will automatically sort the returned values in ascending order. Use the `DESC` keyword, as above, to sort in descending order.

**EXERCISE**

Imagine you're working for an important investment firm in Manhattan. The investors are interested in investing in a lucrative and popular cat. They need your help to decide which cat that will be. They want a list of famous and wealthy cats. We can do that with a basic `SELECT` statement:

```
SELECT * FROM cats WHERE net_worth > 0;
```

This will return:

```
name             age         breed          net_worth 
---------------  ----------  -------------  ----------
Maru             3           Scottish Fold  1000000   
Hana             1           Tabby          21800     
Grumpy Cat       4           Persian        181600    
Lil' Bub         2           Tortoiseshell  2000000   
```

Our investors are busy people though. They don't have time to manually sort through this list of cats for the best candidate. They want you to return the list to them with the cats sorted by net worth, from greatest to least.
We can do so with the following lines:

```
SELECT * FROM cats ORDER BY(net_worth) DESC;
```

This will return:

```
name             age         breed          net_worth 
---------------  ----------  -------------  ----------
Lil' Bub         2           Tortoiseshell  2000000   
Maru             3           Scottish Fold  1000000   
Grumpy Cat       4           Persian        181600    
Hana             1           Tabby          21800     
```

###CODE ALONG II: THE `LIMIT` KEYWORD

Turns out our investors are very impatient. They don't want to review the list themselves, they just want you to return to them the wealthiest cat. We can accomplish this by using the `LIMIT` keyword with the above query:

```
SELECT * FROM cats ORDER BY(net_worth) DESC LIMIT 1;
```
```
name             age         breed          net_worth 
---------------  ----------  -------------  ----------
Lil' Bub         2           Tortoiseshell  2000000   
```

The `LIMIT` keyword specifies how many of records that result from the query you'd like to actually return.

###CODE ALONG III: `GROUP BY()`

The `GROUP BY()` keyword is very similar to `ORDER BY()`. The only difference is that `ORDER BY()` sorts the resulting data set of basic queries while `GROUP BY()` sorts the result sets of aggregate functions.

**SYNTAX**

```
SELECT column_name, aggregate_function(column_name)
FROM table_name
WHERE column_name operator value
GROUP BY column_name;
```

**EXERCISE**

Let's calculate the sum of the net worth of all of the cats that belong to our second owner:

```
SELECT SUM(Cats.net_worth) 
FROM Owners 
INNER JOIN cats_owners 
ON Owners.id = cats_owners.owner_id 
JOIN Cats ON cats_owners.cat_id = Cats.id 
WHERE cats_owners.owner_id = 2;
```

This should return:

```
SUM(Cats.net_worth) 
--------------------
1181600 
```

In the above query, we use the `SUM(Cats.net_worth)` aggregator. `SUM` looks at the all of the values in the `net_worth` column of the Cats table (or whatever column you specify in parentheses) and takes the sum of the those values.

###CODE ALONG IV: `HAVING` VS `WHERE` CLAUSE

Suppose we have a table called `employee_bonus` as shown below. Note that the table has multiple entries for employees Abigail and Matthew.

employee_bonus

```
Employee	Bonus
Matthew	1000
Abigail	2000
Matthew	500
Tom	700
Abigail	1250
```

To calculate the total bonus that each employee received, we would write a SQL statement like this:

```
SELECT employee, sum(bonus) from employee_bonus group by
 employee;
```

This should return:

```
Employee	Bonus
Matthew	1500
Abigail	3250
Tom	700
```

Now, suppose we wanted to find the employees who received more than $1,000 in bonuses for the year of 2007. You might think that we could write a query like this:

```
BAD SQL:
select employee, sum(bonus) from employee_bonus 
group by employee where sum(bonus) > 1000;
```

Unfortunately the above will not work because the where clause doesn’t work with aggregates – like sum, avg, max, etc. What we need to use is the `HAVING` clause. The having clause was added to sql just so we could compare aggregates to other values – just how the ‘where’ clause can be used with non-aggregates. Now, the correct sql will look like this:

```
GOOD SQL:
select employee, sum(bonus) from employee_bonus 
group by employee having sum(bonus) > 1000;
```

**DIFFERENCE BETWEEN HAVING AND WHERE CLAUSE**

The difference between the having and where clause in sql is that the where clause can not be used with aggregates, but the having clause can. One way to think of it is that the having clause is an additional filter to the where clause.

##SQL Complex Joins

**OBJECTIVES**

1. Know what an outer join is
2. Distinguish an inner join from an outer join
3. Identify different types of outer joins: left, right, and full

**WHY IS THIS IMPORTANT?**

**GRADE EXAMPLE (INNER JOIN)**

Imagine you want to get a list of all the students with an "A" in the class. We only want those students in the class with top grades, ignoring the other students in the class.

![inner_join](http://readme-pics.s3.amazonaws.com/Grade%20example%20Venn%20Diagram.png)

**FIELD TRIP EXAMPLE (COMPLEX/OUTER JOIN)**

Now imagine another scenario where the class is going on a field trip. The cost of the field trip is 10 dollars per student. As a teacher, we want to keep track of which students have paid AND which students still need to pay.
Everything we've done up until this point looks like the Grade Example. This is an inner join. We only want the students with a certain grade. You can imagine a Venn Diagram where one circle is "Grades" and another circle is "Students". We only want the overlapping (or "inner") parts of the two circles.

Complex joins are useful and important when it comes to situations like the Field Trip Example. Sticking with the Venn Diagrams, we can think about "Students" as one circle and "Payments" as another circle. A complex join (or outer join) will return the overlap between the two circles AND the rest (or the "outer" part) of the "Students" circle as well.

![outer_join](http://readme-pics.s3.amazonaws.com/Payment%20example%20Venn%20Diagram.png)

We'll elaborate more on visualizing joins in the Venn Diagrams section below.

**OVERVIEW**

A complex join in SQL is also referred to as an outer join. It is not necessarily more complex than an inner join. It is referred to as "complex" simply because SQL is conducting an inner join in addition to gathering a little more information from one or more tables. What is that extra bit of information? We will discover this by looking at the difference between outer and inner joins.

**DIFFERENCE BETWEEN INNER JOIN AND OUTER JOIN
INNER JOIN**

As you may recall, an inner join is going to return only the rows from the database that match the query. For example, imagine we have the following tables:

```
TEACHERS TABLE             STUDENTS TABLE
teacher_id                 student_id   teacher_id
---------------            ------------------------
1                          1            NULL
2                          2            1
3                          3            NULL
```

Let's look at an inner join.

```
SELECT * 
FROM Teachers 
INNER JOIN Students 
ON Teacher.teacher_id = Student.teacher_id;
```

This query returns only the teacher with the `id = 1` because student 2 is in the first teacher's class.

```
teacher_id  |  student_id
--------------------------
1           |  2
```

**OUTER JOIN**

Outer Joins, on the other hand, will return all of the matching rows AND all of the additional rows from the specified table. Which table/additional rows are determined by the type of outer join. There are three types of outer joins: Left Outer Join, Right Outer Join, and Full Outer Join.

**LEFT OUTER JOIN**

This is the most common outer join, and the one you'll use most often. This returns the normal inner join result and also returns all of the rows from the left-most (i.e. first mentioned) table.

```
SELECT * 
FROM Teachers 
LEFT OUTER JOIN Students 
ON Teacher.teacher_id = Student.teacher_id;
```
```
teacher_id  |  student_id
--------------------------
1           |  2
2           |  NULL
3           |  NULL
```

Notice that every row from the teacher's table is returned whether there is a corresponding student or not.

**RIGHT OUTER JOIN**

As you might imagine, this is the same as the Left Outer Join with the minor difference being that it ***returns all of the rows from the right-most (i.e. last mentioned) table***. Sticking with our example:

```
SELECT * 
FROM Teachers 
RIGHT OUTER JOIN Students 
ON Teacher.teacher_id = Student.teacher_id;
```
```
teacher_id     |  student_id
--------------------------
NULL           |  1
1              |  2
NULL           |  3
```

**FULL OUTER JOIN**

The full ***returns all of the rows from the all tables***.

```
SELECT * 
FROM Teachers 
FULL OUTER JOIN Students 
ON Teacher.teacher_id = Student.teacher_id;
```
```
teacher_id     |  student_id
--------------------------
NULL           |  1
1              |  2
NULL           |  3
2              |  NULL
3              |  NULL
```

**VENN DIAGRAMS**

It is helpful to think about our queries as a Venn Diagram. Each table can be represented by a circle.
An Inner Join just returns the overlapping areas of the Venn Diagram.

![inner_join2](http://readme-pics.s3.amazonaws.com/Inner%20Join%20Venn%20Diagram.png)

A Left Outer Join returns all the the data from the left circle, and it includes the overlapping information from the right circle.

![left_outer_join](http://readme-pics.s3.amazonaws.com/Left%20Outer%20Join%20Venn%20Diagram.png)

A Right Outer Join returns all the the data from the right circle, and it includes the overlapping information from the left circle.

![right_outer_join](http://readme-pics.s3.amazonaws.com/Right%20Outer%20Join%20Venn%20Diagram.png)

A Full Outer Join returns all the data from all the tables, including the overlapping data.

![full_outer_join](http://readme-pics.s3.amazonaws.com/Full%20Outer%20Join%20Venn%20Diagram.png)

**EXAMPLES**

**CREATE OUR STUDENTS TABLE**

```
CREATE TABLE students (
    id INTEGER PRIMARY KEY,
    name TEXT,
    teacher_id INTEGER);
```

**INSERT STUDENTS**

```
INSERT INTO students (name, teacher_id)
    VALUES ("Dave", 1);
INSERT INTO students (name, teacher_id)
    VALUES ("Jessie", 1);
INSERT INTO students (name, teacher_id)
    VALUES ("Bob", 1);
INSERT INTO students (name, teacher_id)
    VALUES ("Sara", 2);
INSERT INTO students (name, teacher_id)
    VALUES ("George",  2);
INSERT INTO students (name, teacher_id)
    VALUES ("Alexis",  NULL);
```

**STUDENTS SCHEMA**

```
id               name        teacher_id
---------------  ----------  ----------  
1                Dave           1
2                Jessie         1
3                Bob            1
4                Sara           2
5                Rob            2
6                Alexis
```

**CREATE OUR TEACHERS TABLE**

```
CREATE TABLE teachers (
    id INTEGER PRIMARY KEY,
    name TEXT);
INSERT INTO TEACHERS
INSERT INTO teachers (name)
    VALUES ("Steven");
INSERT INTO teachers (name)
    VALUES ("Joe");
INSERT INTO teachers (name)
    VALUES ("Jeff");
```

**TEACHERS SCHEMA**

```
id               name
---------------  ---------
1                Joe
2                Steven
3                Jeff
```

**LEFT OUTER JOIN**

```
SELECT * from teachers
   LEFT OUTER JOIN students on teachers.id = students.teacher_id;
```

This query will return all of the records in the left table (teachers) regardless if any of those records have a match in the right table (students). It will also return any matching records from the right table. So for our example, it first returns all of the teachers followed by any student that has a `teacher_id`. You can see that Alexis was not returned because her `teacher_id` column is `NULL`.

**RESULTS**

```
id  teacher_name    id      name     teacher_id
--- ------------   ----    ------    -----------
1      Joe           2       Bob          1
1      Joe           1       Dave         1  
1      Joe           3       Jess         1
2      Steven        5       Rob          1
2      Steven        4       Sara         1
3      Jeff          NULL    NULL         NULL
```

**RIGHT OUTER JOIN**

```
SELECT * from teachers
   RIGHT OUTER JOIN students on teachers.id = students.teacher_id;
```

This query will return all of the records in the right table (students) regardless if any of those records have a match in the left table (teachers). It will also return any matching records from the left table. You can see that all of the students were returned, but this time Jeff was left out.

**RESULTS**

```
id    teacher_name   id      name     teacher_id
---   ------------  ----    ------    -----------
1        Joe         2       Bob          1
1        Joe         1       Dave         1  
1        Joe         3       Jess         1
2        Steven      5       Rob          1
2        Steven      4       Sara         1
NULL     NULL        6       Alexis       NULL
```

**FULL JOIN**

```
SELECT * from teachers
   FULL OUTER JOIN students on teachers.id = students.teacher_id;
```

This Join can be referred to as a FULL OUTER JOIN or a FULL JOIN. This query will return all of the records from both tables, joining records from the left table (teachers) that match records from the right table (students).

```
id    teacher_name   id      name     teacher_id
---   ------------  ----    ------    -----------
1        Joe         2       Bob          1
1        Joe         1       Dave         1  
1        Steven      3       Jess         1
2        Steven      5       Rob          1
2        Steven      4       Sara         1
3        Jeff       NULL     NULL        NULL
NULL     NULL        6       Alexis      NULL
```