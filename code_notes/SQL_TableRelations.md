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