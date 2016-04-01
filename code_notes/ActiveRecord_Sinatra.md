##ActiveRecord Setup in Sinatra
####OVERVIEW
Sinatra doesn't come with database support out of the box, but it's relatively easy to configure. In general, we'll be working from templates that have this pre-built, but it's good to understand what's going on under the hood. We're going to practice adding a database into our Sinatra applications.

####INSTRUCTIONS
Fork and clone this repository to get started! We have a basic sinatra application stubbed out with an `app.rb` file acting as the controller.

####ADDING YOUR GEMS
First, we'll add three gems to allow us to use ActiveRecord: `activerecord`, `sinatra-activerecord`, and `rake`. `activerecord` gives us access to the magical database mapping and association powers. `rake`, short for "ruby make", is a package that lets us quickly create files, folders, and automate tasks such as database creation, and `sinatra-activerecord` gives us access to some awesome Rake tasks. Make sure those three gems are in your Gemfile:

```ruby
    gem 'sinatra'
    gem 'activerecord'
    gem 'sinatra-activerecord'
    gem 'rake'
    gem 'thin'
    gem 'require_all'
```

Into our development group, we'll add two other gems: `sqlite3` and `tux`. `sqlite3` is our database adapter gem - it's what allows our Ruby application to communicate with a SQL database. `tux` will give us an interactive console that pre-loads our database and ActiveRecord relationships for us. Since we won't use either of these in production, we put them in our `:development` group - this way, they won't get installed on our server when we deploy our application.

```ruby
    gem 'sinatra'
    gem 'activerecord'
    gem 'sinatra-activerecord'
    gem 'thin'
    gem 'require_all'
 
    group :development do
        gem 'shotgun'
        gem 'pry'
        gem 'tux'
        gem 'sqlite3'
    end
```

Our Gemfile is up to date - awesome! Go ahead and run `bundle install` to get your system up to speed.

####CONNECTING TO THE DATABASE
We now have access to all of the gems that we need, but we still need to setup a connection to our database. Add the following block of code to your `environment.rb` file (underneath `Bundler.require(:default, ENV['SINATRA_ENV'])`).

```ruby
configure :development do
  set :database, "sqlite3:db/database.db"
end
```

This sets up a connection to a sqlite3 database named "database.db", located in a folder called "db." If we wanted our `.db` file to be called `dogs.db`, we could simply change the name of this file:

```ruby
configure :development do
  set :database, "sqlite3:db/dogs.db"
end
```

But for now, `database.db` is a great name! Notice that this didn't actually create those files or folders yet - that's what Rake will help us with!

####MAKING A RAKEFILE
As we mentioned, `rake` gives us the ability to quickly make files and setup automated tasks. We define these in a file called `Rakefile`. First, create a `Rakefile` in the root of our project directory (if you're using Nitrous, you'll need to refresh the file navigation tree to see the file). In your `Rakefile`, we'll require our `config/environment.rb` file to load up our environment, as well as `"sinatra/activerecord/rake"` to get Rake tasks from the `sinatra-activerecord` gem.

```ruby
require'./config/environment'
require "sinatra/activerecord/rake"
```

In the terminal, type `rake -T` to view all of your available rake tasks. You should see the following output:

```
rake db:create              # Creates the database from DATABASE_URL or config/database.yml for...
rake db:create_migration    # Create a migration (parameters: NAME, VERSION)
rake db:drop                # Drops the database from DATABASE_URL or config/database.yml for t...
rake db:fixtures:load       # Load fixtures into the current environment's database
rake db:migrate             # Migrate the database (options: VERSION=x, VERBOSE=false, SCOPE=blog)
rake db:migrate:status      # Display status of migrations
rake db:rollback            # Rolls the schema back to the previous version (specify steps w/ S...
rake db:schema:cache:clear  # Clear a db/schema_cache.dump file
rake db:schema:cache:dump   # Create a db/schema_cache.dump file
rake db:schema:dump         # Create a db/schema.rb file that is portable against any DB suppor...
rake db:schema:load         # Load a schema.rb file into the database
rake db:seed                # Load the seed data from db/seeds.rb
rake db:setup               # Create the database, load the schema, and initialize with the see...
rake db:structure:dump      # Dump the database structure to db/structure.sql
rake db:structure:load      # Recreate the databases from the structure.sql file
rake db:version             # Retrieves the current schema version number
```

Awesome!

####TESTING IT OUT
Let's test out our handiwork by creating a `dogs` table with two columns: `name` and `breed`. First, let's create our migration:

```
rake db:create_migration NAME=create_dogs
```

You should see the following output:

```
=># db/migrate/20150914201353_create_dogs.rb
```

The beginning of the file is a timestamp - yours should reflect the time that your `create_dogs` file was created! You've now created your first database migration inside of the `db` folder (Remember to refresh your file tree if you're using Nitrous)

Inside of our migration file, remove the default `change` method (we'll come back to this), and add methods for `up` and `down`

```ruby
class CreateDogs < ActiveRecord::Migration
  def up
  end
 
  def down
  end
end
```

Our `up` method should create our table with `name` and `breed` columns. Our down method should drop the table.

```ruby
class CreateDogs < ActiveRecord::Migration
  def up
    create_table :dogs do |t|
      t.string :name
      t.string :breed
    end
  end
 
  def down
    drop_table :dogs
  end
end
```

Now, run the migration from the terminal with `rake db:migrate`.

```
rake db:migrate
```

You should see the following output:

```
== 20150914201353 CreateDogs: migrating =======================================
-- create_table(:dogs)
   -> 0.0019s
== 20150914201353 CreateDogs: migrated (0.0020s) ==============================
```

THE `CHANGE` METHOD
The change method is actually a shorter way of writing `up` and `down` methods. We can refactor our migration to look like this:

```ruby
class CreateDogs < ActiveRecord::Migration
  def change
    create_table :dogs do |t|
      t.string :name
      t.string :breed
    end
  end
 
end
```

While the rollback (`down`) method is not included, it's implicit in the change method. Rolling back the database would work in exactly the same way as using the `down` method.

##ActiveRecord in Sinatra
####INTRODUCTION
We're familiar with how to preform the basic CRUD (Create, Read, Update, Delete) actions using Active Record. Let's take a moment and review. For this example, we'll use the class name `Model` to stand in for whatever model your app is working with (`Post`, `Student`, `Song`, you name it).

+ Create: `Model.create`
+ Read: `Model.all`/`Model.find(id_number)`
+ Update: `Model.update`
+ Delete: `Model.destroy`

Now, let's take a look at how to build out a Sinatra app that allows a user to implement these actions through the interface of the web. The details of what code gets written in each controller action are left slightly vague––you're going to need to do some careful thinking for yourself when it comes to building this out later on. The purpose of this reading is to illustrate the connections between the various controller actions and views needed to implement CRUD.

####CONNECTING CONTROLLER ACTIONS TO VIEWS FOR IMPLEMENTING CRUD
#####CREATE
The "create" part of CRUD is implemented in Sinatra by building a route, or controller action, to render the form for creating a new instance of your model.

+ The `get '/model/new'` route renders the view page with that form.
+ The `new.erb` is the view page that contains that form.
+ That form sends a `POST` request to another controller action, `post '/models'`. It is here that you place the code that extracts the form data from the `params` and uses it to create a new instance of your model class. Something along the lines of `Model.create(some_attribute: params[:some_attribute])`.

#####READ
There are two ways in which we can read data. We may want to "read" or deliver to our user, *all* of the instances of a class, or a *specific* instance of a class.

+ The `get '/models'` controller action handles requests for all instances of a class. It should load up all of those instances and set them equal to an instance variable: `@models = Model.all`. Then, it renders the `index.erb` view page.
+ The `index.erb` view page will use erb to render all of the instances stored in the `@models` instance variable.
+ The `get '/models/:id'` controller action handles requests for a given instance of your model. For example, if a user types in `www.yourwebsite.com/models/2`, this route will catch that request and get the `id` number, in this case `2`, from the params. It will then find the instance of the model with that id number and set it equal to an instance variable: `@model = Model.find(params[:id])`. Lastly, it will render the `show.erb` view page.
+ The `show.erb` view page will use erb to render the `@model` object.

#####UPDATE
To implement the update action, we need a controller action that renders an update form and we need a controller action to catch the post request sent by that form.

+ The `get 'models/:id/edit'` controller action will render the `edit.erb` view page.
+ The `edit.erb` view page will contain the form for editing a given instance of a model. This form will send a `PATCH` request to `patch '/models/:id'`.
+ The `patch '/models/:id'` controller action will find the instance of the model to update, using the `id` from `params`, update and save that instance.

We'll need to update `config.ru` to use the Sinatra Middleware that lets our app send `patch` requests.

`config.ru`:

```
use Rack::MethodOverride
run ApplicationController
```

From there, you'll need to add a line to your form:

`edit.erb`:

```html
<form action="/models/:id" method="post">
    <input id="hidden" type="hidden" name="_method" value="patch">
    <input type="text" ...>
</form>
```

The `MethodOverride` middleware will intercept every request sent and received by our application. If it finds a request with `name="_method"`, it will set the request type based on what is set in the `value` attribute. In this case `patch`.

#####DELETE
The delete part of CRUD is a little tricky. It doesn't get its own view page, but instead is implemented via a "delete button" on the show page of a given instance. This "delete button", however, isn't really a button, it's a form! The form should send a `DELETE` request to `delete '/models/:id/delete'` and should contain only a "submit" button with a value of "delete". That way, it will appear as only a button to the user. Here's an example:

```html
<form method="post" action="/models/<%=@model.id%>/delete">
  <input id="hidden" type="hidden" name="_method" value="DELETE">
  <input type="submit" value="delete">
</form>
```

The hidden input field is important to note here. This is how you can submit PATCH and DELETE requests via Sinatra. The form tag `method` attribute will be set to `post`, but the hidden input field sets it to `DELETE`.

#####CONCLUSION
Remember, the purpose of this reading is to help you understand which controller actions render which views, and which views have forms that send requests to which controller actions, as we implement CRUD. Check out the diagram below for the big picture:

![crud](http://readme-pics.s3.amazonaws.com/Screen%20Shot%202015-12-28%20at%2010.49.31%20AM.png)