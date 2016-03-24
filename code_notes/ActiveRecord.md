#ActiveRecord
##ActiveRecord Mechanics

###WHY IS THIS USEFUL?

Up until now, we've built our own ORM. We even worked up to creating a dynamic ORM that could give us lots of functionality via inheritance. Active Record is like the dynamic ORM we built. It maps database tables to Ruby classes. Imagine our dynamic ORM on steroids. We don't have to write much code -- as long as we follow conventions. And as a result, Active Record gives you enormous functionality (Think of our ORM with a lot more methods).

###ORM VS ACTIVE RECORD

By now you should already be familiar with the concept of an [ORM](http://en.wikipedia.org/wiki/Object-relational_mapping), and have written something of your own via the Ruby Student Class.

Building your own little ORM for a single Class is a great way to learn about how object oriented programming languages commonly interact with a database, but imagine you had two, or three, or ten classes in your application that all corresponded to different tables in the database. Building those same kinds of methods into each of them would be a lot of work! Ok, maybe you're thinking: I bet I could just write one Class that would have methods that could work with any table, and use that instead. Go for it! Don't really though, that will become a lot of code really quick, and as your demands grow, maintenance and stability will quickly become an issue as well! The best ORMs develop over time and require a lot of testing.

So, chances are, you just don't know enough about Ruby or databases yet to make something flexible or efficient enough to meet your needs as a professional web application developer. Not to worry, you're not the first person to have this problem, and there are already plenty of great libraries out there that will make your life easier. Meet [ActiveRecord](http://guides.rubyonrails.org/active_record_basics.html), the database toolkit for Ruby!

###ACTIVERECORD ORM

Active Record is a Ruby gem, meaning we get an entire library of code just by running `gem install activerecord` or by including it in our `Gemfile`. As a result, we can get an enormous amount of functionality simply by following a few conventions.

**CONNECT TO DB**

This is how we would connect to a database:

```
connection = ActiveRecord::Base.establish_connection(
  :adapter => "sqlite3",
  :database => "db/songs.sqlite"
)
``` 
 
**CREATE A TABLE**

Let's create our table using SQL:

```
sql = <<-SQL
  CREATE TABLE IF NOT EXISTS songs (
  id INTEGER PRIMARY KEY, 
  title TEXT, 
  length INTEGER
  )
SQL
 
ActiveRecord::Base.connection.execute(sql)
```

It starts to get interesting when you make use of ActiveRecord's built-in ORM utilities to extend your Ruby classes with ActiveRecord's `Model` class.

With ActiveRecord, and other ORMs the way this is managed is through [Class Inheritance](http://rubylearning.com/satishtalim/ruby_inheritance.html).

**ACTIVE RECORD METHODS**

To add `ActiveRecord::Base`'s methods to your class you'd do:

```
class Student < ActiveRecord::Base
end
```

Now your `Student` class has a whole bunch of [new methods](http://guides.rubyonrails.org/active_record_basics.html#creating-active-record-models) available to it, that are already built into ActiveRecord.

**`.COLUMN_NAMES`**

Retrieve a list of all the columns in the table:

```
Student.column_names
#=> [:id, :name]
```

**`.CREATE`**

Create a new student:

```
Student.create(name: 'Jon')
# INSERT INTO students (name) VALUES ('Jon')
```

**`.FIND`**

Retrieve a Student from the database by id:

```
Student.find(1)
```

**`.FIND_BY`**

Find by name, or any attribute:

```
Student.find_by(name: 'Jon')
# SELECT * FROM artists WHERE (name = 'Jon') LIMIT 1
```

**`ATTR_ACCESSORS`**

You can get or set attributes of instances of a Student once you have retrieved it:

```
student = Student.find_by(name: 'Jon')
student.name
#=> 'Jon'
 
student.name = 'Steve'
 
student.name
#=> 'Steve'
```

**`#SAVE`**

And then save those changes to the database:

```
student = Student.find_by(name: 'Jon')
student.name = 'Steve'
student.save
```

Note that our Student class doesn't have any methods defined for `#name` either. Nor does it make use of Ruby's built-in `attr_accessor` method.

```
class Student < ActiveRecord::Base
end
```

##Intro to Rake

###WHAT IS RAKE?

Rake is a tool that is available to us in Ruby that allows us to automate certain jobs––anything from execute SQL to `puts`-ing out a friendly message to the terminal.

Rake allows us to define something called "Rake tasks" that execute these jobs. Once we define a task, that task can be executed from the command line.

###WHY RAKE?

Every program has some jobs that must be executed now and then. For example, the task of creating a database table, the task of making or maintaining certain files. Before Rake was invented, we would have to write scripts that accomplish these tasks in BASH, or we would have to make potentially confusing and arbitrary decisions about what segment of our Ruby program would be responsible for executing these tasks.

Writing scripts in BASH is tough, and BASH just isn't as powerful as Ruby. On the other hand, for each developer to make his or her own decisions about where to define and execute certain common tasks related to databases or file maintenance is confusing.

Rake provides us a standard, conventional way to define and execute such tasks using Ruby.

###WHERE DID RAKE COME FROM?
In fact, the C community was the first to implement the pattern of writing all their recurring system maintenance tasks in a separate file. They called this file the MakeFile because it was generally used to gather all of the source files and make it into one compiled executable file.

Rake was later developed by Jim Weirich as the task management tool for Ruby.

###HOW TO DEFINE AND USE RAKE TASKS

Building a Rake task is easy, since Rake is already available to us as a part of Ruby. All we need to do is create a file in the top level of our directory called `Rakefile`. Here we define our task:

```
task :hello do
  # the code we want to be executed by this task 
end
```

We define tasks with `task` + `name of task as a symbol` + a block that contains the code we want to execute.

If you open up the Rakefile in this directory, you'll see our `:hello` task:

```
task :hello do 
  puts "hello from Rake!"
end
```

Now, in your terminal in the directory of this project, type:

`rake greeting:hello`

You should see the following outputted to your terminal:

```
hello from Rake!
```

**DESCRIBING OUR TASKS FOR `RAKE -T`**

Rake comes with a handy command, `rake -T`, that we can run in the terminal to view a list of available Rake tasks and their descriptions. In order for `rake -T` to work though, we need to give our Rake tasks descriptions. Let's give our `hello` task a description now:

```
desc 'outputs hello to the terminal'
task :hello do 
  puts "hello from Rake!"
end
```

Now, if we run `rake -T` in the terminal, we should see the following:

```
rake hello       # outputs hello to the terminal
```

So handy!

**NAMESPACING RAKE TASKS**

It is possible to namespace your Rake tasks. What does "namespace" mean? A namespace is really just a way to group or contain something, in this case our Rake tasks. So, we might namespace a series of greeting Rake tasks, like `hello`, above, under the `greeting` heading.

Let's take a look at namespacing now. Let's say we create another greeting-type Rake task, `hola`:

```
desc 'outputs hola to the terminal'
task :hola do 
  puts "hola de Rake!"
end
```

Now, let's namespace both `hello` and `hola` under the `greeting` heading:

```
desc 'outputs hello to the terminal'
namespace :greeting do 
  task :hello do 
    puts "hello from Rake!"
  end
 
  desc 'outputs hola to the terminal'
  task :hola do 
    puts "hola de Rake!"
  end
end
```

Now, to use either of our Rake tasks, we use the following syntax:

```
rake greeting:hello
hello from Rake!
 
rake greeting:hola
hola de Rake!
```

**COMMON RAKE TASKS**

As we move towards developing Sinatra and Rails web applications, you'll begin to use some common Rake tasks that handle the certain database-related jobs.

**`RAKE DB:MIGRATE`**

One common pattern you'll soon become familiar with is the pattern of writing code that creates database tables and then "migrating" that code using a rake task.

Our `Student` class currently has a `#create_table` method, so let's use that method to build out our own `migrate` Rake task.

We'll namespace this task under the `db` heading. This namespace will contain a few common database-related tasks.

We'll call this task `migrate`, because it is a convention to say we are "migrating" our database by applying SQL statements that alter that database.

```
desc 'migrate changes to your database' 
namespace :db do 
  task :migrate => :environment do 
    Student.create_table
  end
end
```

Now, if we run `rake db:migrate`, our database table will be created.

**TASK DEPENDENCY**

You might be wondering what is happening with this snippet:

```
task :migrate => :environment do 
...
```

This creates a *task dependency*. Since our `Student.create_table` code would require access to the `config/environment.rb` file (which is where the student class and database are loaded), we need to give our task access to this file. We can do so by defining yet another Rake task that we can tell to run before the `migrate` task is run.

Let's check out that `environment` task:

```
# in Rakefile
 
task :environment do
  require_relative './config/environment'
end
```

**`RAKE DB:SEED`**

Another task you will become familiar with is the `seed` task. This task is responsible for "seeding" our database with some dummy data.

The conventional way to seed your database is to have a file in the `db` directory, `db/seeds.rb`, that contains some code to create instances of your class.

If you open up `db/seeds.rb` you'll see the following code to create a few students:

```
require_relative "../lib/student.rb"
 
Student.create(name: "Melissa", grade: "10th")
Student.create(name: "April", grade: "10th")
Student.create(name: "Luke", grade: "9th")
Student.create(name: "Devon", grade: "11th")
Student.create(name: "Sarah", grade: "10th")
```

Then, we define a rake task that executes the code in this file. This task will also be namespaced under db:

```
desc 'seed the database with some dummy data'
namespace :db do
 
  ...
 
  task :seed do 
    require_relative './db/seeds.rb'
  end
end
```

Now, if we run `rake db:seed` in our terminal (provided we have already run `rake db:migrate` to create the database table), we will insert five records into the database.

If only there was some way to interact with our class and database without having to run our entire program...
Well, we can build a Rake task that will load up a Pry console for us.

**`RAKE CONSOLE`**

We'll define a task that starts up the Pry console. We'll make this task dependent on our `environment` task so that the `Student` class and the database connection load first.

```
desc 'drop into the Pry console'
task :console => :environment do
  Pry.start 
end
```

Now, provided we ran `rake db:migrate` and `rake db:seed`, we can drop into our console with the following:

```
rake console
```

This should bring up the following in the your terminal:

```
[1] pry(main)>
```

Let's check to see that we did in fact successfully migrate and seed our database:

```
[1] pry(main)> Student.all
=> [[1, "Melissa", "10th"],
 [2, "April", "10th"],
 [3, "Luke", "9th"],
 [4, "Devon", "11th"],
 [5, "Sarah", "10th"]]
```

We did it!

##Mechanics of Migrations

### MIGRATIONS
Migrations are a convenient way for you to alter your database in a structured and organized manner. You could edit fragments of SQL by hand but you would then be responsible for telling other developers that they need to go and run them. You’d also have to keep track of which changes need to be run against the production machines next time you deploy.

Migrations also allow you to describe these transformations using Ruby. The great thing about this is that it is database independent: you don’t need to worry about the precise syntax of CREATE TABLE any more than you worry about variations on SELECT * (you can drop down to raw SQL for database specific features). For example, you could use SQLite3 during development, but Postgres in production.

Another way to think of migrations is like version control for your database. You might create a table, add some data to it, and then make some changes to it later on. By adding a new migration for each change you make to the database, you won't lose any data you don't want to, and you can easily revert changes.

Executed migrations are tracked by ActiveRecord in your database, so they aren't used twice. Using the migrations system to apply the schema changes is easier than keeping track of the changes manually and executing them manually at the appropriate time.

**SETTING UP YOUR MIGRATION**

1. If you haven't done so already, fork and clone this repository via Github. (This is marked as a Readme so you'll need to open this on Github.) Make a directory in the directory called `db`. Then within the `db` directory, create a `migrate` directory.
2. In the migrate directory, create a file called `01_create_artists.rb` (we'll talk about why we added the 01 later).

```
# db/migrate/01_create_artists.rb
 
class CreateArtists < ActiveRecord::Migration
  def up
  end
 
  def down
  end
end
```

**ACTIVE RECORD MIGRATION METHODS: UP, DOWN, CHANGE**

Here we're creating a class called `CreateArtists` which inherits from ActiveRecord's `ActiveRecord::Migration` module. Within the class we have an `up` method to define what code to execute when the migration is run, and in the `down` method we define what code to execute when the migration is rolled back. Think of it like "do" and "undo."

Another method is available to use besides `up` and `down`: `change`, which is more common for basic migrations.

```
# db/migrate/01_create_artists.rb
 
class CreateArtists < ActiveRecord::Migration
  def change
  end
end
```

Which is just short for do this, and then undo it on rollback. Let's look at creating the rest of the migration to generate our artists table and add some columns.

**CREATING A TABLE**

Remember how we created a table using SQL with ActiveRecord.

First, we'd have to connect to a database:

```
connection = ActiveRecord::Base.establish_connection(
  :adapter => "sqlite3",
  :database => "db/songs.sqlite"
)
```
 
Then, we'd create our table using SQL:

```
sql = <<-SQL
  CREATE TABLE IF NOT EXISTS songs (
  id INTEGER PRIMARY KEY, 
  title TEXT, 
  length INTEGER
  )
SQL
 
ActiveRecord::Base.connection.execute(sql)
```

Now that we have access to `ActiveRecord::Migration`, we can create tables using only Ruby. Yay!

```
# db/migrate/01_create_artists.rb
def change
  create_table :artists do |t|
  end
end
```

Here we've added the `create_table` method, and passed the name of the table we want to create as a symbol. Pretty simple, right? Other methods we can use here are things like `remove_table`, `rename_table`, `remove_column`, `add_column` and others. See [this list](http://guides.rubyonrails.org/migrations.html#writing-a-migration) for more.

No point in having a table that has no columns in it, so lets add a few:

```
# db/migrate/01_create_artists.rb
 
class CreateArtists < ActiveRecord::Migration
  def change
    create_table :artists do |t|
      t.string :name
      t.string :genre
      t.integer :age
      t.string :hometown
    end
  end
end
```

Looks a little familiar? On the left we've given the data type we'd like to cast the column as, and on the right we've given the name we'd like to give the column. The only thing that we're missing is the primary key. ActiveRecord will generate that column for us, and for each row added, a key will be autoincremented.

And that's it! You've created your first ActiveRecord migration. Next, we're going to see it in action!

**RUNNING MIGRATIONS**

The simplest way is to run our migrations through a raketask that we're given through the activerecord gem. How do we access these?

Run `rake -T` to see the list of commands we have.

Let's look at the `Rakefile`. The way in which we get these commands as raketasks is through `require 'sinatra/activerecord/rake'`.

Now take a look at `environment.rb`, which our Rakefile also requires:

```
require 'bundler/setup'
Bundler.require
 
ActiveRecord::Base.establish_connection(
  :adapter => "sqlite3",
  :database => "db/artists.sqlite"
)
```

This file is requiring the gems in our Gemfile and giving our program access to them. We're going to connect to our artists db, which will be created in the migration, via sqlite3 (the adapter).

Let's run rake `db:migrate`
4) Take a look at `artist.rb`. Let's create an `Artist` class.

```
# artist.rb
 
class Artist
end
```

Next, we'll extend the class with `ActiveRecord::Base`

```
# artist.rb
 
class Artist < ActiveRecord::Base
end
```

To test it out, let's use the rake task `rake console`, which we've created in the `Rakefile`.

**TRY OUT THE FOLLOWING**:

View that the class exists:

```
Artist
#=> Artist (call 'Artist.connection' to establish a connection)
```

View that database columns:

```
Artist.column_names
#=> [:id, :name, :genre, :age, :hometown]
```

Instantiate a new Artist named Jon, set his age to 30, save him to the database:

```
a = Artist.new(name: 'Jon')
#=> #<Artist id: nil, name: "Jon", genre: nil, age: nil, hometown: nil>
 
a.age = 30
#=> 30
 
a.save
#=> true
```

The `.new` method creates a new instance in memory, but in order for that instance to persist, we need to save it. If we want to create a new instance and save it all in one go, we can use `.create`.

```
Artist.create(name: 'Kelly')
#=> #<Artist id: 2, name: "Kelly", genre: nil, age: nil, hometown: nil>
```

Return an array of all Artists from the database:

```
Artist.all
#=> [#<Artist id: 1, name: "Jon", genre: nil, age: 30, hometown: nil>,
 #<Artist id: 2, name: "Kelly", genre: nil, age: nil, hometown: nil>]
```

Find an Artist by name:

```
Artist.find_by(name: 'Jon')
#=> #<Artist id: 1, name: "Jon", genre: nil, age: 30, hometown: nil>
```

There are a number of methods you can now use to create, retrieve, update, and delete data from your database, and a whole lot more.

Take a look at these [CRUD methods](http://guides.rubyonrails.org/active_record_basics.html#crud-reading-and-writing-data) here and play around with them.

**USING MIGRATIONS TO MANIPULATE EXISTING TABLES**

Here is another place where migrations really shine. Let's add a gender column to our artists table. Remember that ActiveRecord keeps track of what migrations we've already run, so adding it to our `01_create_artists.rb` won't work because it won't get executed when we run our migrations again, unless we drop our entire table before rerunning the migration. But that isn't best practices, especially with a production database.

To make this change we're going to need a new migration, which we'll call `02_add_gender_to_artists.rb`.

```
# db/migrate/02_add_gender_to_artists.rb
 
class AddGenderToArtists < ActiveRecord::Migration
  def change
    add_column :artists, :gender, :string
  end
end
```

Pretty awesome, right? We basically just told ActiveRecord to add a column to the artists table, call it gender, and it's going to be a string.

Notice how we incremented the number in the file name there? Imagine for a minute that you deleted your original database and wanted to execute the migrations again. ActiveRecord is going to execute each file, but it has to do so in some order and it happens to do that in alpha-numerical order. If we didn't have the numbers, our add_column migration would have tried to run first ('a' comes before 'c') and our artists table wouldn't have even been created yet! So we used some numbers to make sure they execute in order. In reality our two-digit system is very rudimentary. As you'll see later on, frameworks like rails have generators that create migrations with very accurate timestamps so you'll never have that problem.

Now that you've save the migration, back to the terminal to run it:

`rake db:migrate`

Awesome! Now go back to the console: `rake console`
and check it out:

```
Artist.column_names
#=> ["id", "name", "genre", "age", "hometown", "gender"]
```

Great!

Nope- wait. Word just came down from the boss- you weren't supposed to ship that change yet! OH NO! No worries, we'll rollback to the first migration.

Run `rake -T`. Which command should we use?

`rake db:rollback`

Then double check:

```
Artist.column_names
#=> ["id", "name", "genre", "age", "hometown"]
```

Oh good, your job is saved. Thanks ActiveRecord! Now when the boss says it's actually time to add that column, you can just run it again!

`rake db:migrate`