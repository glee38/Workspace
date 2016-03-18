#SQL - Structured Query Language
##What is SQL?

SQL (Structured Query Language) is a language for managing data in a database. Unlike some other programming languages, it's only used for one thing: talking to databases. Thus, you might hear it referred to as a "special purpose", or "domain specific" programming language. This means that you won't be using SQL to write the next big web app, but you might use it to interact with the database that powers it.

Even though SQL has just one purpose, it is used by many different database systems such as MySQL, PostgreSQL, or the system we'll being using in this course: SQLite. There are a handful of things other SQL systems like Postgres, or MySQL can do that are not supported by SQLite. Every database system has its own strengths and weaknesses, and as you learn more about them you should evaluate them thoughtfully when deciding which to use for what purpose. For us, SQLite provides a low barrier to entry, and is simple to get up and running.

##SQL Database Basics
###Objectives

1. Describe how relational databases store data in tables composed of columns and rows
2. Use lower case and snake_case conventions for column names
3. Use the `CREATE TABLE` keyword to create a new table with columns, including the `id` column
4. Use the `.help` command to get a complete list of SQL commands
5. Use the `.tables` command to list all the tables in a database
6. Use the `.schema` command to look at the structure of a database
7. Use the `ALTER TABLE` keywords to add columns to a table
8. Use the `DROP TABLE` keywords to delete a table

###Database Structure

Relational Databases like SQLite store data in a structure we refer to as a table. You can think of a table in a database a lot like you would a spreadsheet. We define specific columns in our table, and then we store any number of what we refer to as 'records' as rows in our database. A record is just information referring to one specific entity. For instance, if you had a table called "People" you could imagine a structure like this:

name | age | email
-----|-----|------
Bob |29|	bob@flatironschool.com
Avi |28|	avi@flatironschool.com
Adam |28|	adam@flatironschool.com

Each column has a name, and each row contains the corresponding information about a person.

**NOTE ON COLUMN NAMES**

When we name columns in our database, there are a couple of conventions we will follow. The first is that we will always use lowercase letters when referring to columns in our database. SQLite isn't case sensitive about its commands or column names, but it is generally best practice for us to stick to lowercase for our column names.

The second convention we want to follow is more important. That is, when we have multiple words in a column name, we link them together using underscores rather than spaces. We call this convention `"snake_case"`. So, for instance, if we wanted to be more specific with our email column above, we can name it `email_address`. If we wanted to split up name to first and last we might have columns called `first_name` and `last_name`.

###Database Tables

In the following sections, we'll cover how to create, alter, and delete database tables. This reading is accompanied by a code along exercise that you can do in your terminal. You don't need to fork this repository, and there are no tests to pass. Follow along with the reading and code along instructions.

**CREATE TABLE**

When we create a new database, it comes like a sort of blank slate.

Once you create a database (which you can do with the `sqlite3 database_name.db` command), we create a table using the following statement:

```
CREATE TABLE table_name;
```

But before we're able to store any actual data in a table, we'll need to define the columns in the table as well as the specific type of data each column will store.

Let's give it a shot. For the purposes of this code along, you'll be typing these commands into your terminal.

**CODE ALONG I: CREATING A TABLE**

+ Open up your terminal and create our new database:

```
sqlite3 pet_database.db
```

Now, let's create our table:

```
sqlite> CREATE TABLE cats;
```

You should see the following error:

```
sqlite> CREATE TABLE cats;
Error: near ";": syntax error
```

SQLite expects us to include at least some definition of the structure of this table as well. In other words, when we create database tables, we need to specify some column names, along with the type of data we are planning to store in each column. More on data types later.

Let's try that table statement again:

```
sqlite> CREATE TABLE cats (
        id INTEGER PRIMARY KEY,
                name TEXT, 
                age INTEGER
            );
```

Let's break down the above code:

1. Use the `CREATE TABLE` command to create a new table called "cats"

2. Include a list of column names along with the type of data they will be storing. `TEXT` means we'll be storing plain old text, `INTEGER` means we'll store a number. Note that the use of capitalization is arbitrary, but it is a convention to help separate the SQL commands from the names we make up for our tables and columns.

3. Every table we create, regardless of the other column names and data types, should be defined with an `id INTEGER PRIMARY KEY` column + data type. Our SQLite database tables *must be indexed by a number*. We want each row in our table to have a number, which we'll call "id", just like in an Excel spreadsheet. Numbering our table rows makes our data that much easier to access, update, and organize. SQLite comes with a data type designation called "Primary Key". Primary keys are unique and auto-incrementing, meaning they start at 1 and each new row automatically gets assigned the next numeric value.

Okay, let's check and make sure that we successfully created that table. To do this we'll be using SQL commands. To get a complete list of commands, you can type `.help` into the sqlite prompt.

```
sqlite> .help
.backup ?DB? FILE      Backup DB (default "main") to FILE
.bail ON|OFF           Stop after hitting an error.  Default OFF
.databases             List names and files of attached databases
.dump ?TABLE? ...      Dump the database in an SQL text format
                         If TABLE specified, only dump tables matching
                         LIKE pattern TABLE.
.echo ON|OFF           Turn command echo on or off
.exit                  Exit this program
.explain ?ON|OFF?      Turn output mode suitable for EXPLAIN on or off.
                         With no args, it turns EXPLAIN on.
.header(s) ON|OFF      Turn display of headers on or off
.help                  Show this message
.import FILE TABLE     Import data from FILE into TABLE
.indices ?TABLE?       Show names of all indices
                         If TABLE specified, only show indices for tables
                         matching LIKE pattern TABLE.
.log FILE|off          Turn logging on or off.  FILE can be stderr/stdout
.mode MODE ?TABLE?     Set output mode where MODE is one of:
                         csv      Comma-separated values
                         column   Left-aligned columns.  (See .width)
                         html     HTML <table> code
                         insert   SQL insert statements for TABLE
                         line     One value per line
                         list     Values delimited by .separator TEXT
                         tabs     Tab-separated values
                         tcl      TCL list elements
.nullvalue TEXT      Print TEXT in place of NULL values
.output FILENAME       Send output to FILENAME
.output stdout         Send output to the screen
.prompt MAIN CONTINUE  Replace the standard prompts
.quit                  Exit this program
.read FILENAME         Execute SQL in FILENAME
.restore ?DB? FILE     Restore content of DB (default "main") from FILE
.schema ?TABLE?        Show the CREATE statements
                         If TABLE specified, only show tables matching
                         LIKE pattern TABLE.
.separator TEXT      Change separator used by output mode and .import
.show                  Show the current values for various settings
.stats ON|OFF          Turn stats on or off
.tables ?TABLE?        List names of tables
                         If TABLE specified, only list tables matching
                         LIKE pattern TABLE.
.timeout MS            Try opening locked tables for MS milliseconds
.vfsname ?AUX?         Print the name of the VFS stack
.width NUM1 NUM2 ...   Set column widths for "column" mode
.timer ON|OFF          Turn the CPU timer measurement on or off
```

Woah, that's a lot. Don't worry too much about all of these different commands right now. Just know that you can always use `.help` to check out the available options.

Okay, let's check out our new table. To list all the tables in the database we'll use the `.tables` command. Type it into the sqlite prompt and hit enter, you should get:

```
sqlite> .tables
cats
```

We can look at the structure, or "schema", of our database (i.e. the tables and their columns + column data types) with the `.schema` command:

```
sqlite> .schema
CREATE TABLE cats(
  id INTEGER PRIMARY KEY,
  name TEXT,
  age INTEGER,
);
```

Let's move on to altering our table.

**ALTER TABLE**

Let's say that, after creating a database and creating a table to live inside that database, we decide we want to add or remove a column. We can do so with the `ALTER TABLE` statement.

**CODE ALONG II: ADDING, REMOVING AND RENAMING COLUMNS**

+ Let's say we want to add a new column, breed, to our cats table.
```
sqlite> ALTER TABLE cats ADD COLUMN breed TEXT;
```

Let's check out our schema now:

```
sqlite> .schema
CREATE TABLE cats(
  id INTEGER PRIMARY KEY,
  name TEXT,
  age INTEGER,
  breed TEXT
);
```

Notice that the `ALTER` statement isn't here, but instead SQLite has updated our original `CREATE` statement. The schema reflects the current structure of the database, which is reflected as the `CREATE` statement necessary to create that structure.

+ Unfortunately, altering a column name and/or deleting a column can be tricky in SQLite3. There are workarounds, however. We're not going to get into that right now, but you can explore the documentation on this topic [here](https://www.sqlite.org/lang_altertable.html).

Fortunately, SQLite still supports most of what we'll need to use it for one way or another. For now, if you need to change a column name, it's best to simply delete the table and re-create it.

**DROP TABLE**

Lastly, we'll discuss how to delete a table from a database with the `DROP TABLE` statement.

**CODE ALONG III: DELETING A TABLE**

Deleting a table is very simple:

```
sqlite> DROP TABLE cats;
```

And that's it! You can exit out of the sqlite prompt with the `.quit` command.

##SQL Databases and Text Editors

###Code Along

**CREATING A DATABASE AND TABLE**

+ In the terminal, create a database with the following command:

`sqlite3 pets_database.db`

Once you create your database, exit the sqlite prompt with the `.quit` command.

Open up a text editor (such as Sublime Text) and create and save a file `01_create_cats_table.sql`. In this file, write your create statement:

```
CREATE TABLE cats (
  id INTEGER PRIMARY KEY,
    name TEXT, 
    age INTEGER
);
```

+ Execute that file in the command line. Before running the below command, make sure that you've exited the sqlite prompt that you were in earlier when you created the database.

```
sqlite3 pets_database.db < 01_create_cats_table.sql
```

*Note:* If running the above command gives you an error that the Cats table already exists, that means you created a table with that name in a previous exercise. You can remove your old table with:

```
sqlite3> DROP TABLE cats;
```

**CONFIRMING OUR SQL EXECUTION**

Let's confirm that the above execution of the SQL commands in our `.sql` file, we can do the following.

1. In your terminal, enter into your Pets Database with the sqlite3 pets_database.db command.
2. Run the following .schema command. You should see the following schema printed out, confirming that we did in fact create our Cats table successfully.

```
CREATE TABLE cats (
  id INTEGER PRIMARY KEY,
    name TEXT, 
    age INTEGER
);
```

**Remember to exit out of the `sqlite3` prompt with `.quit`.**

**OPERATING ON OUR DATABASE FROM THE TEXT EDITOR**

To carry out any subsequent actions on this database––adding a column to the cats table, dropping that table, creating a new table––we can create new `.sql` files in the text editor and execute them in the same way as above. Let's give it a shot.

+ To add a column to our cats table:

Create a file `02_add_column_to_cats.sql` and fill it out with:

```
ALTER TABLE cats ADD COLUMN breed TEXT;
```

Then, execute the file in your command line:

```
sqlite3 pets_database.db < 02_add_column_to_cats.sql.
```

+ Confirm that your execution of the `.sql` file worked by entering into your database in the terminal with the `sqlite3 pets_database.db` command. Once there, execute the `.schema` command and you should see that the schema of the Cats table does include the `breed` column.

##SQL Data Types

###Why Do Data Types Matter?

We've learned that when we create a table, we need to include a name for it as well as define at least one column. We define columns in a `CREATE` statement by including a name and a datatype to let SQLite know the kind of data it we will be storing there. The practice of explicitly declaring a type is known as "typing."

Why is it important that we use typing in our database? Simply put, typing allows us to exercise some level of control over our data. Typing not only informs our database of the kind of data we plan to store in a column but it also restricts it. For instance, look at the age column below in our cats table. What do we mean by age? What if we had this:

name | breed | age
-----|-------|----
Maru|Scottish Fold|3
Hannah|Tabby|two
Lil' Bub|American Shorthair|5.5

Did we intend age to be represented as a whole-number, a word, or a decimal? If we asked you to add up the ages of all the cats you could simply convert the 'two' to 2 in your head, but your database can't do that. It doesn't have that ability because the logic involved in converting a word into a number would be dense and inefficient. What about different languages? What about different spellings? Capitalization, typos, or different hyphenation conventions? These are just some reasons this might start to get crazy. In other words, because databases are designed to store large amounts of data, they are very concerned with storing, accessing, and acting upon that data as efficiently and normally as possible.

Typing gives us the ability to perform all kinds of operations with predictable results. For instance, the ability to perform Math operations like `SUM` - i.e. summing integers - doesn't just depend on everything being an integer of some sort but would also expect it. If you tried, for example, to `SUM` all of the cats in the above table, SQLite would actually attempt to convert, or cast, their type to something it can `SUM`. It would try to convert anything it can to an `INTEGER` and ignore alpha characters. This can lead to real problems. Without typing, our data might get complicated and messy, and it would be difficult to ask the database questions about large sets of data.

We're going to adhere strictly to only storing data that fits with the datatype we have given to a particular column.

###Datatypes

Different database systems also have different datatypes available, which are important and useful to know whenever you are dealing with those systems. SQLite is a good starting point to learn about datatypes because it only has four basic categories of datatypes; they are:

```
TEXT
INTEGER
REAL
BLOB
```

Let's explore each category in more detail.

**TEXT**

Any alphanumeric characters which we want to represent as plain text. The body of this paragraph is text. Your name is text. Your email address is a piece of text. Your height, weight, and age, however, are probably not.

**INTEGER**

Anything we want to represent as a whole number. If it's a number and contains no letter or special characters or decimal points then we should store it as an integer. If we use it to perform math or create a comparison between two different rows in our database, then we definitely want to store it as an integer. If it's just a number, it's generally not a bad idea to store it as an integer. You might never add two house address numbers together, but you might want to sort them numerically. For example in the preceding case, you might want to get the biggest number and not the longest piece of text.

**REAL**

Anything that's a plain old decimal like 1.3 or 2.25. SQLite will store decimals up to 15 characters long. You can store 1.2345678912345 or 1234.5678912345, but 1.23456789123456789 would only store 1.2345678912345. In other database systems this is called 'double precision.'

With these three types in hand, we are going to be able to work our way through the next several topics, and this whole typing concept is going to quickly be second nature for you.

**BLOB**

You may encounter the `BLOB` datatype while you're Googling or doing any further reading on SQLite. For now, we will not use `BLOB`. It is generally used for holding binary data.

###Bonus: Note on SQLite

To increase its compatibility with other database engines (E.G. mySQL or PostgreSQL), SQLite allows the programmer to use other common datatypes outside of the four mentioned above. This is why we are referring to `TEXT INTEGER REAL BLOB` as datatype "categories". All other common datatypes are lumped into one of the four existing datatypes recognized by SQLite.

For example, `INT` is a common datatype used outside of SQLite. SQLite won't complain if you define a column as an `INT` datatype. It will simply lump it into the `INTEGER` category and store it as such.

To accommodate this, SQLite has a pretty complicated system of categorizing datatypes that involves `Storage Classes`, `Type Affinities`, and `Datatypes`. For a deeper dive, check out the [SQLite3 Documentation on Datatypes](http://www.sqlite.org/datatype3.html).

##SQL Inserting, Updating, and Selecting

###Objectives

1. Use the `INSERT INTO` command to insert data (i.e. rows) into a database table
2. Use `SELECT` statements to select data from a database table
3. Use the `WHERE` Clause to select data from specific table rows
4. Use comparison operators, like `<` or` >`, to select specific data
5. Use `UPDATE` statements to update data within a database table
6. Use `DELETE` statements to delete data from a database table

###Setting Up Our Database

In this code along, we'll be creating a cats table in a `pets_database.db`. So, let's navigate to our terminal and get started.

First let's create our `pets_database` by running the following command.

```
sqlite3 pets_database.db
```

Now that we have a database, let's create our `cats` table along with `id`, `name`, `age` and `breed` columns.

```
CREATE TABLE cats (
    id INTEGER PRIMARY KEY,
    name TEXT,
    age INTEGER,
    breed TEXT
);
```

Okay, let's start storing some cats.

**CODE ALONG I: INSERT INTO**

In your terminal, in the sqlite prompt, type the following:

```
INSERT INTO cats (name, age, breed) VALUES ('Maru', 3, 'Scottish Fold');
```

We use the `INSERT INTO` command, followed by the name of the table to which we want to add data. Then, in parentheses, we put the column names that we will be filling with data. This is followed by the `VALUES` keyword, which is accompanied by a parentheses enclosed list of the values that correspond to each column name.

**Important**: Note that we *didn't specify* the "id" column name or value. Since we created the `cats` table with an "id" column whose type is `INTEGER PRIMARY KEY`, we don't have to specify the id column values when we insert data. Primary Key columns are auto-incrementing. As long as you have defined an id column with a data type of `INTEGER PRIMARY KEY`, a newly inserted row's id column will be automatically given the correct value.

Let's add a few more cats to our table. This time we'll do this via our text editor. Create a file, `01_insert_cats_into_cats_table.sql`. Use several `INSERT INTO` statements to insert the following cats into the table:

id | name | age | breed
---|------|-----|------
1|"Lil' Bub"|5|American Shorthair
2|"Hannah"|1|Tabby

Each `INSERT INTO` statement gets its own line in the `.sql` file in your text editor. Each line needs to end with a `;`. Run the file with the following code in your terminal:

```
sqlite3 pets_database.db < 01_insert_cats_into_cats_table.sql
```
**NOTE**: This is a bash command, run this from your bash not sqlite console.

Now, we'll learn how to `SELECT` data from a table, which will help us to confirm that we inserted the above data correctly.

###Selecting Data

Now that we've inserted some data into our cats table, we likely want to read that data. This is where the `SELECT` statement comes in. We use it to retrieve database data, or rows.

**CODE ALONG II: SELECT FROM**

A basic `SELECT` statement works like this:

```
SELECT [names of columns we are going to select] FROM
 [table we are selecting from];
```

We specify the names of the columns we want to SELECT and then tell SQL the table we want to select them FROM.

We want to select all the rows in our table, and we want to return the data stored in any and all columns in those rows. To do this, we could pass the name of each column explicitly:

```
SELECT id, name, age, breed FROM cats;
```

Which should give us back:

```
1 | Maru     | 3 | Scottish Fold
2 | Hannah   | 1 | Tabby
3 | Lil' Bub | 5 | American Shorthair
```

A faster way to get data from every column in our table is to use a special selector, known commonly as the 'wildcard', `*` selector. The `*` selector means: "Give me all the data from all the columns for all of the cats" Using the wildcard, we can `SELECT` all the data from all of the columns in the cats table like this:

```
SELECT * FROM cats;
```

Now let's try out some more specific `SELECT` statements:

**Selecting by Column Names**

To select just certain columns from a table, use the following:

```
SELECT name FROM cats;
```

That should return the following:

```
Maru
Hannah
Lil' Bub
```

You can even select more than one column name at a time. For example, try out:

```
SELECT name, age FROM cats;
```

**Top-Tip**: If you have duplicate data (for example, two cats with the same name) and you only want to select unique values, you can use the `DISTINCT` keyword. For example:

```
SELECT DISTINCT name FROM cats;
```

**Selecting Based on Conditions: The `WHERE` Clause**

What happens when we want to retrieve a specific table row? For example the row that belongs to Maru? Or to retrieve all the baby cats who are younger than two years old? We can use the `WHERE` keyword to select data based on specific conditions. Here's an example of a boilerplate `SELECT` statement using a `WHERE` clause.

```
 SELECT * FROM [table name] WHERE [column name] = 
 [some value];
```

Let's retrieve just Maru from our cats table:

```
 sqlite> SELECT * FROM cats WHERE name = "Maru";
```

That statement should return the following:

```
1|Maru|3|Scottish Fold
```

We can also use comparison operators, like `<` or `>` to select specific data. Let's give it a shot. Use the following statement to select the older cats:

```
 SELECT * FROM cats WHERE age > 2;
```

**Advanced**: The SQL statements we're learning here will eventually be used to integrate the applications you'll build with a database. For example, it's easy to imagine a web application that has many users. When a user signs into your app, you'll need to access your database and select the user that matches the credentials an individual is using to log in.

###Updating Data

Let's talk about updating, or changing, data in our table rows. We do this with the `UPDATE` keyword.

**CODE ALONG III: UPDATE**

A boilerplate `UPDATE` statement looks like this:

```
UPDATE [table name] SET [column name] = [new value] WHERE [column name] = [value];
```

The `UPDATE` statement uses a `WHERE` clause to grab the row you want to update. It identifies the table name you are looking in and resets the data in a particular column to a new value.

Let's update one of our cats. Turns out Maru's friend Hannah is actually Maru's friend *Hana*. Let's update that row to change the name to the correct spelling:

```
sqlite> UPDATE cats SET name = "Hana" WHERE name = "Hannah";
```

One last thing before we move on: deleting table rows.

###Deleting Data

To delete table rows, we use the `DELETE` keyword.

**CODE ALONG IV: DELETE**

A boilerplate `DELETE` statement looks like this:

```
DELETE FROM [table name] WHERE [column name] = [value];
```

Let's go ahead and delete Lil' Bub from our cats table (sorry Lil' Bub):

```
sqlite> DELETE FROM cats WHERE id = 3;
```

Notice that this time we selected the row to delete using the Primary Key column. Remember that every table row has a Primary Key column that is unique. Lil' Bub was the third row in the database and thus had an id of `3`.

##Basic SQL Queries

###Objectives

1. Define a query as an SQL statement that retrieves data from a database
2. Use the `ORDER BY` modifier to order tables by specific `SELECT` statements
3. Use the `ASC` and `DESC` modifier to sort queries in ascending and descending orders
4. Use the `LIMIT` modifier to determine the number of records to retrieve from a dataset
5. Use the `BETWEEN` modifier to retrieve a specific data set between two ranges
6. Use the `NULL` data type keyword to insert new records into a table
7. Use the `COUNT` function to count the number of records that meet a certain condition

###What is a SQL Query?

The term "query" refers to any SQL statement that retrieves data from your database. In fact, we've already written a number of SQL queries using basic `SELECT` statements. We've already seen how to retrieve single units of data, or rows, with queries like these:

To select all of the rows from a `cats` table:

```
SELECT * FROM cats;
```

To select only rows representing data meeting certain conditions:

```
SELECT * FROM cats WHERE name = "Maru";
```

What if, however, we wanted to select the oldest cat? Or all of the cats that don't currently belong to an owner? Or all of the cats with short names?

Data storage isn't very useful if we can't manipulate, view, and analyze that data. Luckily for us, SQL is actually a powerful tool for doing just that.

In this exercise, we'll walk through executing a handful of common and handy SQL queries. If you want to take a deeper dive, however, feel free to check out the resources at the bottom of the page.

###Code Along: SQL Queries

**CREATING OUR DATABASE**

In this code along, we'll be creating a `cats` table in a `pets_database.db`. So, let's navigate to our terminal and get started.

First let's create our `pets_database` by running the following command.

```
sqlite3 pets_database.db
```

Good work. Let's type `ls` in the terminal and see what just happened. A new file should appear called `pets_database.db`! This is the binary representation of the database. You can think of this like a `.jpg` file. It won't open up in a text editor, but it does open up in the image viewer app. It is the same way for `.db` files. They won't open in your editor, but they can be read by the appropriate database engine.

Now that we have a database, let's create our `cats` table along with `id`, `name`, `age`, `breed`, and `owner_id` columns.

    CREATE TABLE cats (
        id INTEGER PRIMARY KEY,
        name TEXT,
        age INTEGER,
        breed TEXT,
        owner_id INTEGER
    );
Let's add some cats to our `cats` table to make this more interesting:


	sqlite> INSERT INTO cats (name, age, breed, owner_id) VALUES ("Maru", 3 , "Scottish Fold", 1);
	sqlite> INSERT INTO cats (name, age, breed, owner_id) VALUES ("Hana", 1 , "Tabby", 1);
	sqlite> INSERT INTO cats (name, age, breed) VALUES ("Lil' Bub", 5, "American Shorthair");
	sqlite> INSERT INTO cats (name, age, breed) VALUES ("Moe", 10, "Tabby");
	sqlite> INSERT INTO cats (name, age, breed) VALUES ("Patches", 2, "Calico");
	
Let's checkout our cats table now:

```
sqlite> SELECT * FROM cats;
```

This should return:

```
1|Maru|3|Scottish Fold|1
2|Hana|1|Tabby|1
3|Lil' Bub|5|American Shorthair|
4|Moe|10|Tabby|
5|Patches|2|Calico|
```

**Top-Tip**: You can format the output of your select statements with a few helpful options:

```
.header on       # output the name of each column
.mode column     # now we are in column mode, enabling us to run the next two .width commands
.width auto      # adjusts and normalizes column width
# or
.width NUM1, NUM2 # customize column width
```

Run the first two commands and then execute the above `SELECT` statement instead and you should see something like this:

```
id  name        age    breed          owner_id  
--  ----------  -----  -------------  ----------
1   Maru        3      Scottish Fold  1         
2   Hana        1      Tabby          1         
3   Lil' Bub    5      American Shor            
4   Moe         10     Tabby                    
5   Patches     2      Calico     
```

Much better.

**`ORDER BY`**

The first query modifier we'll explore is `ORDER BY`. This modifier allows us to order the table rows returned by a certain `SELECT` statement. Here's a boilerplate `SELECT` statement that uses `ORDER BY`:

```
SELECT column_name FROM table_name ORDER BY column_name ASC|DESC;
```

Let's select our cats and order them by age:

```
sqlite> SELECT * FROM cats ORDER BY age;
```

This should return the following:

```
id  name        age    breed       owner_id  
--  ----------  -----  ----------  ----------
2   Hana        1      Tabby       1         
5   Patches     2      Calico                
1   Maru        3      Scottish F  1         
3   Lil' Bub    5      American S            
4   Moe         10     Tabby  
```

When using `ORDER BY`, the default is to order in ascending order. If we want to specify though, we can use `ASC` for "ascending" or `DESC` for "descending." Let's try to select all of our cats and sort them by age in descending order.

```
sqlite> SELECT * FROM cats ORDER BY age DESC;
```

This should return

```
id  name        age    breed       owner_id  
--  ----------  -----  ----------  ----------
4   Moe         10     Tabby                 
3   Lil' Bub    5      American S            
1   Maru        3      Scottish F  1         
5   Patches     2      Calico                
2   Hana        1      Tabby       1 
```

**`LIMIT`**

What if we want the oldest cat? If we want to select extremes from a database table––for example, the employee with the highest paycheck or the patient with the most recent appointment––we can use `ORDER BY` in conjunction with `LIMIT`.

`LIMIT` is used to determine the number of records you want to return from a dataset. For example:

```
SELECT * FROM cats ORDER BY age DESC LIMIT 1;
```

This part of the statement: `SELECT * FROM cats ORDER BY age DESC` returns all of the cats in order from oldest to youngest. Setting a `LIMIT` of `1` returns just the first, i.e. oldest, cat on the list.

Execute the above statement in your terminal and you should see:

```
id  name        age    breed       owner_id  
--  ----------  -----  ----------  ----------
4   Moe         10     Tabby                 
```

Let's get the two oldest cats:

```
SELECT * FROM cats ORDER BY age DESC LIMIT 2;
```

Execute that statement and you should see:

```
id  name        age    breed       owner_id  
--  ----------  -----  ----------  ----------
4   Moe         10     Tabby                 
3   Lil' Bub    5      American S  
```
          
**`BETWEEN`**

As we've already established, being able to sort and select specific data sets is important. Continuing on with our example, let's say we urgently need to select all of the cats whose age is between 1 and 3. To create such a query, we can use `BETWEEN`. Here's an boilerplate `SELECT` statement using `BETWEEN`:

```
SELECT column_name(s) FROM table_name WHERE column_name BETWEEN value1 AND value2;
```

Let's try it out on our cats table:

```
SELECT name FROM cats WHERE age BETWEEN 1 AND 3;
```

This should return:

```
Maru
Hana
Patches
```

**`NULL`**

Let's say the adminstrator of our Pets Database has found a new cat. This kitty doesn't have a name yet, but should be added to our database right away. We can add data with missing values using the `NULL` keyword.

Let's insert our new cat into the database. Our abandoned kitty has a breed, but no name or age as of yet:

```
INSERT INTO cats (name, age, breed) VALUES (NULL, NULL, "Tabby");
```

Now, if we look at our cats data with `SELECT * FROM cats;`, we should see:

```
id          name        age         breed          owner_id  
----------  ----------  ----------  -------------  ----------
1           Maru        3           Scottish Fold  1         
2           Hana        1           Tabby          1         
3           Lil' Bub    5           American Shor            
4           Moe         10          Tabby                    
5           Patches     2           Calico                   
6                                   Tabby  
```
          
We can even select the mysterious, nameless kitty with the following query:

```
SELECT * FROM cats WHERE name IS NULL;
```

This should return the following:

```
id          name        age         breed       owner_id  
----------  ----------  ----------  ----------  ----------
6                                   Tabby      
```

**`COUNT`**

Lastly, we'll talk about one SQL aggregate function, `COUNT`.

**SQL aggregate functions** are SQL statements that retrieve minimum and maximum values from a column, sum values in a column, get the average of a column's values, or count a number of records that meet a certain conditions. You can learn more about the these SQL aggregators [here](http://www.sqlclauses.com/sql+aggregate+functions) and [here](http://zetcode.com/db/sqlite/select/).

For now, we'll just focus on `COUNT`. `COUNT` will count the number of records that meet certain condition. Here's a standard SQL query using `COUNT`:

```
 "SELECT COUNT([column name]) FROM [table name] WHERE [column name] = [value]"
```

Let's try it out and count the number of cats who have an `owner_id` of `1`:

```
SELECT COUNT(owner_id) FROM cats WHERE owner_id = 1;
```

This should return:

```
COUNT(owner_id)
---------------
2
```

**NOTE ON `SELECT`**

We are now familiar with this syntax:

```
SELECT name FROM cats;
```

However, you may not know that this can be written like this as well:

```
SELECT cats.name FROM cats;
```

Both return:

```
name      
----------
Maru      
Hana
Lil' Bub
Moe
Patches 
```
   
SQLite allows us to explicitly state the tableName.columnName we want to select. This is particularly useful when we want data from two different tables.

Imagine we have another table called `dogs` with a column for the dog names:

```
    CREATE TABLE dogs (
      id INTEGER PRIMARY KEY,
      name TEXT
   );
```   

    sqlite> INSERT INTO dogs (name) VALUES ("Clifford");
    
If we want to get the names of all the dogs and cats, we can no longer run a query with just the column name.
`SELECT name FROM cats,dogs;` will return `Error: ambiguous column name: name`.

Instead, we must explicitly follow the tableName.columnName syntax.

```
SELECT cats.name, dogs.name FROM cats, dogs;
```

You may see this in the future. Don't let it trip you up.

##SQL Aggregate Functions

###Aggregate Functions

Aggregate functions perform a calculation on specified values, queried from a database table. We will cover the following aggregators here:

1. `AVG`
2. `SUM`
3. `COUNT`
4. `MIN`
5. `MAX`

We'll craft queries that select a desired set of values from a table and then aggregate that data using the above aggregators, in addition to clauses that will group and or order the returned data based on various conditions.

For this walk-through, we'll be utilizing a database of pets and owners.

###Setting up the Database

Some cats are very famous, and accordingly very wealthy. Our pets database will have a cats table in which each cat has a name, age, breed and net worth.

**Creating the Database:**

Create the database in your terminal with the following:

```
sqlite3 pets_database.db
```

**Creating the table:**

In the `sqlite3>` prompt in your terminal:

```
CREATE TABLE cats (
name TEXT,
age INTEGER,
breed TEXT, 
net_worth INTEGER
);
```

**Inserting the values:**

```
INSERT INTO cats (name, age, breed, net_worth) VALUES ("Maru", 3, "Scottish Fold", 1000000);
INSERT INTO cats (name, age, breed, net_worth) VALUES ("Hana", 1, "Tabby", 21800);
INSERT INTO cats (name, age, breed, net_worth) VALUES ("Grumpy Cat", 4, "Persian", 181600);
INSERT INTO cats (name, age, breed, net_worth) VALUES ("Lil' Bub", 2, "Tortoiseshell", 2000000);
```

**Confirming our Data**

```
SELECT * FROM cats;
```

should return:

```
name             age         breed          net_worth 
---------------  ----------  -------------  ----------
Maru             3           Scottish Fold  1000000   
Hana             1           Tabby          21800     
Grumpy Cat       4           Persian        181600    
Lil' Bub         2           Tortoiseshell  20000000   
```

###Using Aggregators

**CODE ALONG I: `AVG()`**

The average, `AVG()`, function returns the average value of a column. Here's how it works:

```
SELECT AVG(column_name) FROM table_name;
```

Let's write a query to grab the average net worth of our very lucrative cats.

```
SELECT AVG(net_worth) FROM cats;
```

This should return:

```
AVG(net_worth) 
---------------
800850.0 
```

That return value is a little ugly, however. Let's use the `AS` keyword to rename the column. This is called "aliasing the return value".

```
SELECT AVG(net_worth) AS average_net_worth FROM cats;
```

This should return:

```
average_net_worth   
--------------------
800850.0 
```

**CODE ALONG II: `SUM()`**

The sum, `SUM()`, function returns the sum of all of the values in a particular column.

Here's how it works:

```
SELECT SUM(column_name) FROM table_name;
```

Let's try it out by calculating the sum of the net worths of all of our cats:

```
SELECT SUM(net_worth) FROM cats;
```

This should return:

```
SUM(net_worth)      
--------------------
3203400   
```

**CODE ALONG II: `MIN()` AND `MAX()`**

The minimum and maximum aggregator functions return the minimum and maximum values from a specified column respectively.

Here's how it works:

```
SELECT MIN(column_name) FROM table_name;
SELECT MAX(column_name) FROM table_name;
```

Let's try it out:

```
SELECT MIN(net_worth) FROM cats;
```

This should return:

```
MIN(net_worth)      
--------------------
21800   
```

**CODE ALONG III: `COUNT()`**

The count function returns the number of rows that meet a certain condition.

Here's how it works:

```
SELECT COUNT(column_name) FROM table_name;
```

We can use the `COUNT()` function to calculate the total number of rows in a table that are not `NULL`. `NULL` means empty. All of our cats have a `name` so we can call `COUNT` on the name column like this:

```
SELECT COUNT(name) FROM cats;
```

This should return:

```
COUNT(name)            
--------------------
4
```

We have a total of four cats in our Cats table with a name. If we really didn't care about a specific column and we just wanted the total number of rows in our database we can call `COUNT(*)`. `*` means everything. Sometimes it's called the "wildcard." This `COUNT(*)` will count the rows where at least one column has data in it.

We can also use `COUNT()` to count the total number of rows in a table that meet a certain criteria. Let's use this aggregator to count the number of cats whose net worth is greater than one million:

```
SELECT COUNT(*) FROM cats WHERE net_worth > 1000000;
```

This should return:

```
COUNT(*)            
--------------------
1    
```