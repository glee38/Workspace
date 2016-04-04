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

##User Authentication in Sinatra
####USER AUTHORIZATION: USING SESSIONS
####LOGGING IN
What does it mean for a user to "log in"? The action of logging in is the simple action of storing a user's ID in the `session` hash. Here's a basic user log in flow:

1. User visits the log in page and fills out a form with their email and password. They hit "submit" and `POST` that data to a controller route.

2. That controller route accesses the user's email and password from the params. That info is used to find the appropriate user from the database with a line such as `User.find_by(email: params[:email], password: params[:password])`. **Then, that user's ID is stored at the value of session[:id]**.

3. As a result, we can introspect on the `session` hash in *any other controller route* and grab current user by matching up a user ID with the `session[:id]`. That means that, for the duration of session (i.e. the time between someone logs in and logs out of your app), our app will know who the "current user" is on every page.

#####A NOTE ON PASSWORD ENCRYPTION
For the time being, we will simply store a user's password in the database in it's raw form. However, that is not safe! In an upcoming lesson, we'll learn about password encryption: the act of scrambling a user's password into a super-secret code and storing a de-crypter that will be able to match up a user's password, when they log in, to the encrypted password we'll store in our database.

####LOGGING OUT
+ What does it mean to log out? Conceptually, it means we are terminating the "session", the time in which a given user is interacting with our app. The action of "logging out" is really just the action of clearing all the data, including the user's ID, from the session hash. Luckily for us, there is already a Ruby method to empty a hash: `#clear`.

####USER REGISTRATION
Before a user can sign in, they need to sign up! What does it mean to "sign up"? A new user submits their information (for example, name, email and password) via a form. When that form gets submitted, a POST request is sent to a route defined in your controller. That route will have code that does the following:
+ Get the new user's name, email and password from the params.
+ Use that info to create and save a new user. For example: `User.create(name: params[:name], email: params[:email], password: params[:password])`.
+ Lastly, once a user has "signed up", we should sign them in. It would be annoying if you had to create a new account on a site and then sign in right afterwards. So, in the same controller route in which we create a new user, we set the `session[:id]` equal to the new user's id, effectively logging them in.
+ Lastly, we redirect the user somewhere else, like their personal home page.

####PROJECT STRUCTURE
#####OUR PROJECT
Our file structure looks like this:

```
-app
  |- controllers
      |- application_controller.rb
  |- models
      |- user.rb
  |- views
      |- home.erb
      |- registrations
          |- signup.erb
      |- sessions
          |- login.erb
      |- users
          |- home.erb
-config
-db
-spec
...
```

#####THE `APP` FOLDER
The `app` folder contains the models, views and controllers that make up the core of our Sinatra application. Get used to seeing this set up. It is conventional to group these files under an `app` folder.

#####APPLICATION CONTROLLER
+ The `get '/registrations/signup'` route. This route has one responsibility: render the sign up form view. This view can be found in `app/views/registrations/signup.erb`. Notice we have separate view sub-folders to correspond to the different controller action groupings.
+ The `post '/registrations'` route. This route is responsible for receiving the `POST` request that happens when a user hits "submit" on that signup form. It will have the code that gets the new user's info from the params, creates a new user, signs them in and then redirects them somewhere else.
+ The get `'/sessions/login'` route. This route is responsible for rendering the login form.
+ The `post '/sessions'` route. This route is responsible for receiving the `POST` request that gets sent when a user hits "submit" on that login form. This route has the code that grabs the user's info from the params, finds that user from the database and signs that user in.
+ The get `'/sessions/logout'` route. This route is responsible for logging the user out by clearing the `session` hash.
+ The `get '/users/home'` route. This route is responsible for rendering the user's home page view.

#####THE `MODELS` FOLDER
The models folder is pretty straightforward. It contains one file because we only have one model in this app: the `User` model.

The code in `app/models/user.rb` will be pretty basic. We'll validate some of the attributes of our user by writing code that makes sure no one can sign up without inputing their name, email and password. More on this later.

#####THE `VIEWS` FOLDER
This folder has a few sub-folders we want to take a look at. Since we have our different controllers responsible for different functions/features, we want our view folder structure to match up. So, we have:

+ The **`views/registrations`** sub-directory. This directory has just one file, the template for the new user signup form. That template will be rendered by the `get '/registrations/signup'` route in our controller. This form will `POST` to the `post '/registrations'` route in our controller
+ The **`views/sessions sub-directory`**. This directory also has just one file, the template for the login form. This template is rendered by the `get '/sessions/login'` route in the controller. The form on this page `POST`s to the `post '/sessions'` route.
+ The **`views/users`** sub-directory. This directory has just one file, the template for the user's homepage. This page is rendered by the `get '/users/home'` route in the controller.
+ We also have a `home.erb` file in the top level of the views directory. This is the page rendered by the root route, `get '/'`.

####PART I: MODELS AND MIGRATIONS
Our `User` model has a few attributes. A user has a name, email and password.

#####STEP 1: MIGRATION
Write a migration that creates a `Users` table with name, email and password. Run `rake db:migrate` and then run your test suite.

In terminal:

```
rake db:create_migration NAME=create_users
```

You'll see that you're passing a number of tests, including tests like these:

```
User
  is invalid without a name
  is invalid without a email
  is invalid without an password
```

Let's think about the concept of validations...

####PART II: CONTROLLERS AND VIEWS
#####STEP 1: THE ROOT PATH AND THE HOMEPAGE
First things first, let's set up our root path and our home page.

+ Open up `app/controllers/application_controller.rb` and check out the `get '/'` route. This route should render the `app/views/home.erb` page with the following code:

```
erb :home
```

+ Run your test suite again with `learn` or `rspec` in the command line and you should be passing these two tests:

```
ApplicationController
  home page: GET /
    responds with a 200 status code
    renders the home page view, 'home.erb'
```

+ Start up your app by running `shotgun` in the terminal. Visit the home page at `localhost:9393` and you should see message that welcomes you to Hogwarts and shows you a link to sign up and a link to log in.
+ Let's look at the code behind this view. Open up that `app/views/home.erb` view and you should see the following:

```html
<h1>Welcome to Hogwarts</h1>
  <h4>Please sign up or log in to access your @hogwarts.edu email account</h4>
  <a href="/registrations/signup">Sign Up</a>
  <a href="/sessions/login">Log In</a>
```

Notice that we have two links, the "Sign Up" link and the "Log In" link. Let's take a closer look:

+ The "href" or destination of the first link is `/registrations/signup`. This means that this link points to the route `get 'registrations/signup'`.
+ The "href" or destination of the second link is `/sessions/login`. This means that this link points to the route `get 'sessions/login'`.

Let's move on to step 2, the building our user sign up flow.

#####STEP 2: USER SIGN UP
+ In your controller you should see two routes dedicated to sign up. Let's take a look at the first route, `get '/registrations/signup'`, which is responsible for rendering the signup template.

```ruby
get '/registrations/signup' do
    erb :'/registrations/signup'
end
```

+ Navigate to `localhost:9393/registrations/signup`. You should see a page that says `"sign up below:"`. Let's make a sign up form!
+ Open up `app/views/registrations/signup.erb`. Our signup form needs a field for name, email and password. It needs to `POST` data to the `'/registrations'` path, so your form action should be `'/registrations'` and your form method should be `POST`.
+ Once you've written your form, go ahead and add the line: `puts params` inside the `post '/registrations'` route in the controller. Then, fill out the form in your browser and hit the `"Sign Up"` button.
+ Hop on over to your terminal and you should see the params outputted there. It should look something like this (but with whatever info you entered into the form):

```ruby
{"name"=>"Beini Huang", "email"=>"beini@bee.com", "password"=>"password"}
```

+ Okay, so we're inside our `post '/registrations'` route, we have our params that contains the user's name, email and password. Inside the `post '/registrations'` route, place the following code:

```ruby
@user = User.new(name: params["name"], email: params["email"], password: params["password"])
@user.save
```

+ We did it! We registered a new user! Now we just need to sign them in. On the following line, set the `session[:id]` equal to our new user's ID:

```ruby
session[:id] = @user.id
```

+ Take a look at the last line of the method:

```ruby
redirect '/users/home'
```

Now that we've signed up and logged in our user, we want to take him or her to their home page.

Go ahead and run the test suite again and you should see that almost all of the user sign up tests are passing.

#####STEP 3: FETCHING THE CURRENT USER
Open up the view file: `app/views/users/home.erb` and look at the following line of code:

```
"Welcome, <%=@user.name%>!"
```

Looks like this view is trying to operate on a `@user` variable. We know that the only variables that a view has access to are instance variables that are set in the controller route that renders that view page. For this page, that route can be found in the Users Controller.

Remember, after a user signs up and is signed in via the code we wrote in the previous step, we redirect to this path: `'/users/home'`. Let's go check out that route right now.

+ Again, take a look at the controller. You should be able to find the route `get '/users/home'`. This route is responsible for finding the current user, based on the ID in the `session` hash and setting that user equal to a variable, `@user` that we can render in our view page. Let's do it:

```ruby
get '/users/home' do
    @user = User.find(session[:id])
    erb :'/users/home'
  end
```

+ Run the tests again and we should be passing all of our user sign up tests.

#####STEP 4: LOGGING IN
+ Go back to your home page and look at the second of the two links:

```html
<a href="/sessions/login">Log In</a>
```

+ This is a link to the `get '/sessions/login'` route. Checkout the two routes defined in the controller for logging in and out. We have our `get '/sessions/login'` route and our `post '/sessions'` route.
+ The `get /sessions/login'` route renders the Log In view page. Restart your app by executing `Command + C` and then typing `shotgun` in your terminal. Navigate back to the root page, `localhost:9393` and click on the `"Log In"` link. It should take you to a page that says `"log in below:"` Let's make our log in form!
+ Open up `app/views/sessions/login.erb`. We need a form that sends a `POST` request to `/sessions` and has an input field for email and password. Make your form with a submit button that says "Log In". Then, place this line: `puts params`, in the `post '/sessions'` route. Fill our the form and hit "Log In".
+ Hop on over to your terminal and you should see the outputted params, looking something like this (but with whatever information you filled out into the form):

```ruby
{"email"=>"beini@bee.com", "password"=>"password"}
```

+ Inside the `post '/sessions'` route, let's write the lines of code that will find the correct user from the database and log them in by setting the `session[:id]` equal to that user's ID.

```ruby
@user = User.find_by(email: params["email"], password: params["password"])
session[:id] = @user.id
```

+ Notice that the last line of the route redirect the user to their home page. We already coded that route in the Users Controller to retrieve the current user based on the ID stored in `session[:id]`.
+ Run the test suite again and you should be passing the user log in tests.

#####STEP 5: LOGGING OUT
+ Open up `app/views/users/home.erb` and check out the following link:

```html
<a href="/sessions/logout">Log Out</a>
```

+ We have a link that takes us to the `get '/sessions/logout'` route, which is responsible for logging us out by clearing the `session` hash.
+ In the logout route in the `get '/sessions/logout'` controller, put:

```ruby
session.clear
```

+ Run the test suite again and you should passing everything.

####CONCLUSION
Phew! This was a big code-along, and we were introduced to some new concepts. Before moving on, play around with your app a bit. Practice signing up, logging out, logging in and get used to the general flow.

There's a lot to think about here, but there are a few take-aways:

+ Separate out your views to their different concerns if there are some views that pertain to those specific controller routes, give them their own sub-folder.
+ Signing up for an app is nothing more than submitting a form, grabbing data from params and using it to create a new user.
+ Logging in is nothing more that finding the right user and setting their user id equal to an `:id` key in the `session` hash.
+ Logging out is nothing more than clearing all the data from the `session` hash.

Another important take-away from this lab is the flow of information between the different routes and views of an application. If you're still confused by the flow of signing up, logging out/logging in, try drawing it out. Can you map out where your web requests go from the point at which you click the "Sign Up" link all the way through until you sign up, log out and then even log back in? Give it a shot.

##Securing Passwords in Sinatra
####OVERVIEW
Securing users' data is one of the most important jobs of a web developer. Despite frequent warnings against it, many of your users will use the same username and password combination across many different websites. This means that, in general, people will use the same password for our applications that they do for their bank.

Because of this, we never want to store our users' passwords in plain text in our database. Instead, we'll run the passwords through a hashing algorithm. A hashing algorithm manipulates data in such a way that it cannot be un-manipulated. This is to say that if someone got a hold of the hashed version of a password, they would have no way to turn it back into the original. In addition to hashing the password, we'll also add a "salt". A salt is simply a random string of characters that gets added into the hash. That way, if two of our users use the password "fido", they will end up with different hashes in our database.

We'll use an open-source gem, `bcrypt`, to implement this strategy.

####STARTER CODE
We've got a basic Sinatra MVC application. In our `application_controller` we have two helper methods defined: `logged_in?` returns true or false based on the presence of a `session[:user_id]` and `current_user` returns the instance of the logged in user, based on the `session[:user_id]`. We have five actions defined:

+ `get "/" do` renders an `index.erb` file with links to signup or login.
+ `get '/signup'` renders a form to create a new user. The form includes fields form `username` and `password`.
+ `get '/login'` renders a form for logging in.
+ `get '/success'` renders a `success.erb` page, which should be displayed once a user successfully logs in
+ `get '/failure'` renders a `failure.erb` page. This will be accessed if there is an error logging in or signing up.
+ `get '/logout'` clears the session data and redirects to the home page.

We've also stubbed out a user model in `app/models/user.rb` that inherits from `ActiveRecord::Base`.

####PASSWORD ENCRYPTION WITH BCRYPT
BCrypt will store a salted, hashed version of our users password in our database in a column called `password_digest`. Essentially, once a password is salted and hashed, there is no way for anyone to de-code it. This method requires that hackers use a "brute force" approach to gain access to someone's account - still possible, but more difficult.

####IMPLEMENTING BCRYPT
First, let's create a migration for our users table using `rake db:create_migration NAME=create_users`. We'll have two columns: one for `username` and one for `password_digest`.

```ruby
class CreateUsers < ActiveRecord::Migration
  def up
    create_table :users do |t|
        t.string :username
        t.string :password_digest
    end
  end
 
  def down
    drop_table :users
  end
end
```

Run this migration using `rake db:migrate`. Awesome job!

####ACTIVERECORD'S `HAS_SECURE_PASSWORD`
Next, let's update our user model so that it includes `has_secure_password`. This ActiveRecord macro gives us access to a few new methods. A macro is a method that when called, creates methods for you. This is meta programming, which you don't need to worry about now. Just know that using a macros is just like calling a normal ruby method.

In this case, the macro `has_secure_password` is being called just like a normal ruby method. It works in conjunction with a gem called `bcrypt` and gives us all of those abilities in a secure way that doesn't actually store the plain text password in the database.

```ruby
class User < ActiveRecord::Base
 
    has_secure_password
 
end
```
 
Next, let's handle signing up. In our `post '/signup'` action, let's make a new instance of our user class with a username and password from params. Note that even though our database has a column called `password_digest`, we still access the attribute of `password`. This is given to us by `has_secure_password`. You can read more about that in the [Ruby Docs](http://api.rubyonrails.org/classes/ActiveModel/SecurePassword/ClassMethods.html#method-i-has_secure_password).

```ruby
    post "/signup" do
        user = User.new(:username => params[:username], :password => params[:password])
    end
```

Because our user has `has_secure_password`, we won't be able to save this to the database unless our user filled out the password field. Calling `user.save` will return false if the user can't be persisted. Let's update this route so that we redirect to `'/login'` if the user is saved, or `'/failure'` if the user can't be saved. (For now, we'll make the user log in after they sign up successfully).

```ruby
    post "/signup" do
        user = User.new(:username => params[:username], :password => params[:password])
        if user.save
            redirect "/login"
        else
            redirect "/failure"
        end
    end
```

Awesome! Test this feature out in your browser. Leaving the password field blank should land you at the "failure" page, while creating a valid user should take you to login.

Next, create at least one valid user, then let's build out our login action. In `post '/login'`, let's find the user by username.

```ruby
    post "/login" do
        user = User.find_by(:username => params[:username])
    end
```

Next, we need to check two conditions: first, did we find a user with that username? This can be written as `user != nil`, or simply `user`.

```ruby
    post "/login" do
        user = User.find_by(:username => params[:username])
        if user
            redirect "/success"
        else
            redirect "/failure"
        end
    end
```

We also need to check if that user's password matches up with our password_digest. We can use a method called `authenticate`. The method is provided for us by the bcrypt gem. Our `authenticate` method takes a string as an argument. If the string matches up against the password digest, it will return the user object, otherwise it will return false. Therefore, we can check that we have a user AND that the user is authenticated. If so, we'll set the `session[:user_id]` and redirect to the `/success` route. Otherwise, we'll redirect to the `/failure` route so our user can try again.

```ruby
    post "/login" do
        user = User.find_by(:username => params[:username])
        if user && user.authenticate(params[:password])
            session[:user_id] = user.id
            redirect "/success"
        else
            redirect "/failure"
        end
    end
```

Awesome job! We've now built out a basic authentication system for a user without storing a plain-text password in our database.

##Sinatra RESTful Routes
####WHAT IS A RESTFUL ROUTE?
The internet would be a really confusing place without a convention for how to handle URLS - to delete a Facebook post might be www.facebook.com/delete-this-wallpost, but Twitter might be www.twitter.com/remove-this-tweet. Without a specific convention to follow, it would be hard to create new content, edit content, and delete it. RESTful routes provides a design pattern that allows for easy data manipulation. It's nicer for users and nicer for developers to have everything consistent.

A RESTful route is a route that provides mapping between HTTP verbs (get, post, put, delete, patch) to controller CRUD actions (create, read, update, delete). Instead of relying solely on the URL to indicate what site to visit, a RESTful route also depends on the HTTP verb **and** the URL.

What this means is that when your application receives an HTTP request, it introspects on that request and identifies the HTTP method and URL, connects that with a corresponding controller action that has that method and URL, executes the code in that action, and determines which response gets sent back to the client. We don't need to worry about how the mechanics of the pattern matching occurs, just that it does happen.

It's important to note that much of the CRUD actions are different actions that occur on the same resource. Let's take the example of a blog post with the ID 4. If we wanted to view the post, we would make a `GET` request to `/posts/4`. But what about when I want to update that post? Am I hitting a different resource? Nope! Just doing a different action to that same resource. So instead of a `GET` against `post/4` we do a `PUT`. That's why separating what you're talking to (the resource/noun) from the action you're doing (the HTTP verb) is important! That's key to REST.

####BROWSER CAVEAT
Browsers behave a little strange as it relates to `PUT`, `PATCH` and `DELETE` requests, in that they don't know how to send those requests. Forms that delete and edit need to be submitted via `POST` requests.

####ROUTES OVERVIEW
Let's take a blog website as an example. You'd want to have a controller action to create a new post (new route), to display one post (show route), to display all posts (index route), to delete a post (delete route), and a page to edit a post (edit route).

HTTP | VERB | ROUTE | Action | Used For
-----|------|-------|--------|---------
GET	 | '/posts' | index action | index page to display all posts
GET | '/posts/:id' | show action | displays one blog post based on ID in the url
PATCH (Sinatra POST) | '/posts/:id/edit' | edit action | edits one blog post based on ID in the url
DELETE (Sinatra POST) | '/posts/:id/delete' | delete action | deletes one blog post based on ID in the url
POST | '/posts' | create action | creates one blog post

####THE ROUTES
#####INDEX ACTION

```ruby
get '/posts' do
  @posts = Post.all
  erb :index
end
```

The controller action above responds to a `GET` request to the route `'/posts'`. This action is the `index action` and allows the view to access all the posts in the database through the instance variable `@posts`.

#####NEW ACTION

```ruby
get '/posts/new' do
  erb :new
end
 
post '/posts' do
  @post = Post.create(:title => params[:title], :content => params[:content])
  redirect to '/posts/#{@post.id}'
end
```

Above, we have two controller actions. The first one is a `GET` request to load the form to create a new blog post. The second action is the `create action`. This action responds to a `POST` request and creates a new post based on the params from the form and saves it to the database. Once the item is created, this action redirects to the `show` page.

#####SHOW ACTION

```ruby
get '/posts/:id'
  @post = Post.find_by_id(params[:id])
  erb :show
end
```

In order to display a single post, we need a `show action`. This controller action responds to a `GET` request to the route `'/posts/:id'`. Because this route uses a dynamic URL, we can access the ID of the post in the view through the `params` hash.

#####EDIT ACTION

```ruby
get '/posts/:id/edit' do  #load edit form
    @post = Post.find_by_id(params[:id])
    erb :edit
  end
 
patch '/posts/:id' do #edit action
  @post = Post.find_by_id(params[:id])
  @post.title = params[:title]
  @post.content = params[:content]
  @post.save
  redirect to "/posts/#{@post.id}"
end
```

The first controller action above loads the edit form in the browser by making a `GET` request to `posts/:id/edit`.

The second controller action handles the edit form submission. This action responds to a `PATCH` request to the route `/posts/:id`. First, we pull the blog post by the ID from the URl, then we update the title and content attributes and save. The action ends with a redirect to the blog post show page.

We do have to do a little extra work to get the edit form to submit via a `PATCH` request.

Your form must include a hidden input field that will submit our form via `patch`.

```html
<form action="/posts/:id" method="post">
  <input id="hidden" type="hidden" name="_method" value="patch">
  <input type="text" name="title">
  <input type="text" name="content">
  <input type="submit" value="submit">
</form>
```

The second line above `<input type="hidden" name="_method" value="patch">` is what does this for us.

#####USING `PATCH`, `PUT` AND `DELETE` REQUESTS WITH `RACK::METHODOVERRIDE` MIDDLEWARE
The hidden input field shown above uses `Rack:MethodOverride` which is part of [Sinatra middleware](https://github.com/rack/rack/blob/master/lib/rack/method_override.rb).

In order to use this middleware, and therefore use `PATCH` and `DELETE` requests, you *must* tell your app to use the middeleware.

In the `config.ru` file, you'll need the following line to be placed *above* the `run ApplicationController` line:

```ruby
use Rack::MethodOverride
```

This middleware will then run for every request sent by our application. It will interpret any requests with `name="_method"` by translating the request to whatever is set by the `value` attribute. In this example, the `post` gets translated to a `patch` request. The middleware handles `patch` and `delete` in the same way.

#####DELETE ACTION

```ruby
delete '/posts/:id/delete' do #delete action
  @post = Post.find_by_id(params[:id])
  @post.delete
  redirect to '/posts'
end
```

On the blog post show page, we have a form to delete it. The form is submitted via a `DELETE` request to the route `/posts/:id/delete`. This action finds the blog post in the database based on the ID in the url parameters, and deletes it. It then redirects to the index page `/posts`.

Again, this delete form needs the hidden input field:

```html
<form action="/posts/:id/delete" method="post">
  <input id="hidden" type="hidden" name="_method" value="delete">
  <input type="text" name="title">
  <input type="text" name="content">
  <input type="submit" value="submit">
</form>
```

##Using Tux in Sinatra with ActiveRecord
####WHAT IS TUX
Tux is an incredible Ruby gem that lets you access your database and perform all CRUD operations on it through the terminal. It also loads a full environment in the console that allows you to see all routes and views. Primarily, you'll use Tux to make sure your database is set up properly, play around with Ruby objects, and make sure your ActiveRecord associations are working properly.

In this repo there is a simple sinatra app with a `User` model and a method called `say_name`. I want to test that the `say_name` method works.

In order to do that, I need to open up IRB in terminal and require the file `models/user.rb`.

But then I get an error that it doesn't recognize `ActiveRecord`. But when you try to require the ActiveRecord gem, you get the following:

![require activerecord](https://s3.amazonaws.com/learn-verified/require-activerecord.png)

So suddenly, trying to test this method is a big big mess. We keep running into issues. This is why Tux exists. By using the Tux gem, we can easily create objects, and test our methods to manipulate those objects.

####SETUP
Setting up Tux is fairly simple. All you need to do is include it in your Gemfile and run `bundle install` in terminal.

####USING TUX
We've got a full Sinatra application with a single `User` class set up. We've already created the migration for you, but make sure you actually run the migration to create the user table.

Next, it's time to use Tux. In terminal in the directory of this walk-through enter `tux`. 

The tux console has now loaded. Regular terminal commands won't work at this point, but you can use Ruby and ActiveRecord methods.

####CREATE
Just like in our controller action, we can create a user.

```ruby
user = User.create(:name => "Tricia", :email => "tricia@test.com", :fav_icecream => "mint chocolate chip")
```

Or:

```ruby
user = User.new
user.name = "Beth"
user.email = "beth@beth.com"
user.fav_icecream = "rocky road"
user.save
```

####EDIT
We can edit a user that's already been saved to the database. Let's edit the first user.

```ruby
user = User.first
user.name = "tricia yearwood"
user.save
```

####DELETE
Now let's delete the first user:

```ruby
user = User.first
user.delete
```

####SEARCH FOR SPECIFIC USERS
All the `find` methods work in Tux too!

```ruby
user = User.find_by_id(2)
user = User.find_by(:name => "Beth")
user = User.first
user = User.last
```

Once you're done, just exit Tux by entering `exit`.

##ActiveRecord Associations in Sinatra
####INTRODUCTION
Previously, we learned about foreign and primary keys in SQL and how they relate tables to one another. In this lesson, we're going to learn how to do this using ActiveRecord. This lab has pre-written code that you can follow along with - feel free to fork and clone the repo locally.

####PRIMARY KEYS
The end goal of this readme is to create a relationship in our app that mimics the real life cat-owner relationship: owners can **have many** cats and cats **belong to** an owner. Let's assume we have two tables in our database: `cats` and `owners`, which we created from the command line using rake.

####REVIEW: CREATING A TABLE WITH ACTIVERECORD
First, we create a cats table from the command line:
`rake db:create_migration NAME="create_cats"`

This will give us an empty migration in our `db/migrate/` folder. Now lets give our cats table attributes: `name`, `age` and `breed`. This will go into our change method.

```ruby
class CreateCats < ActiveRecord::Migration
  def change
    create_table :cats do |t|
      t.string :name
      t.integer :age
      t.string :breed
    end
  end
end
```

####REVIEW: PRIMARY KEYS
A primary key uniquely identifies each record in a table. It must be unique and cannot have NULL values. Luckily, ActiveRecord will create the primary key for us and will also auto-increment it every time we save a new row in our table.

Go ahead and use Tux to create three instances of the Cat class:

```
Cat.create(:name => "Maru", :age => 3, :breed => "Scottish Fold")
Cat.create(:name => "Hannah", :age => 2, :breed => "Tabby")
Cat.create(:name => "Patches", :age => 2, :breed => "Calico")
```

And two instances of our Owner class:

```
Owner.create(:name => "Sophie")
Owner.create(:name => "Ann")
```
 
Our `cats` table looks like this:

id | name | age | breed
---|------|-----|------
1 | Maru | 3 | Scottish Fold
2 | Hannah | 2 | Tabby
3 | Patches | 2 | Calico

Our `owners` table looks like this:

id | name
---|-----
1 | Sophie
2 | Ann

Now, we need to tell our tables how to relate to each other. This is where we'll use a foreign key.

####USING FOREIGN KEYS
A foreign key points to a primary key in another table. In ActiveRecord we will use the `tablename_id` convention. To add the foreign key to our cats table, we will create another migration.

The foreign key always sits on the table of the object that belongs to. In this case, because cats belong to an owner, the owner_id becomes a column in the cats table.

```ruby
class AddColumnToCats < ActiveRecord::Migration
  def change
    add_column :cats, :owner_id, :integer
  end
end
```

Our `cats` table should look like this:

id | name | age | breed | owner_id
---|------|-----|-------|---------
1 | Maru | 3 | Scottish Fold | 1
2 | Hannah | 2 | Tabby | 2
3 | Patches | 2 | Calico | 1

We now know what our table should look like. However, we haven't told our application how to relate the models to each other.

####`BELONG_TO` AND `HAS_MANY`
Before we write our association let's think about our table structure: A cat belongs to an owner, and an owner can have many cats.

This translates into ruby like this:

```ruby
class Cat
  belongs_to :owner
end
```
```ruby
class Owner
  has_many :cats
end
```

Whenever we use a `has_many` we also have to use the `belongs_to` (and vice-versa) in the other model. ***Keep in mind***: The model with the `belongs_to` association also has the foreign key.

####CREATING OBJECTS
After setting our associations, we can create a cat and a owner and save them to our database. Try using Tux to play around. Create objects, view them, edit them, delete them!

```ruby
sophie = Owner.create(name: "Sophie")
maru = Cat.new(name: "Maru", age: 3, breed: "Scottish Fold")
maru.owner = sophie
maru.save
```

We used the `.create` method to instantiate and save the owner to our database. To instantiate the cat object we used the `.new` method, after that we set "Maru's" owner to the owner we created. Because the `.new` method did not save the cat object to our database the last line will persist the cat object to our database.

The `has_many`/`belongs_to` relationship is the most used association, but there are others as well. You can read more about ActiveRecord Associations [here](http://guides.rubyonrails.org/association_basics.html).

##ActiveRecord Associations: Join Table
By now, you're familiar with the basic ActiveRecord associations: `has_many` and `belongs_to`. But how would you handle an e-commerce site? If you think about the models, there would be a `User` class and an `Item` class, which would contain all the items a user could buy. A user could select to purchase many items, so your first instinct might be to store `user_id` as a column of the Items table. But with an online store, many many users could select to buy the same item, so you would need to store more than one user_id. Clearly that option wouldn't work. Basically, we're dealing with a many-to-many relationship. A user can have many items, and an item can belong to many users.

This is where **Join Tables** come to play, with the `has_many :through` association, which is used for object associations where more than one object can own many objects of the same class.

In the owner and pet `has_many` and `belongs_to` relationship, we store the `owner_id` on the `pets` table. We use the foreign key to store our relationship.

A join table is a table that only has two columns. To keep up with the online store example, this table would contain a `user_id` and `item_id`. Each row in this table would contain a user's ID and an item's ID. We call this join table `user_items`. The `has_many :through` is always singular and in the first part of the join table name, and the `belongs_to` portion of the relationship is pluralized in the second part of the table name.

####MIGRATIONS
First, we need to create three migrations: one for the users table, one for the items table, and the join table -- user_items.

```ruby 
class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
      t.string :name
      t.timestamps null: false
    end
end
 
class CreateItems < ActiveRecord::Migration
  def change
    create_table :items do |t|
      t.string :name
      t.integer :price
      t.timestamps null: false
    end
  end
end
 
class CreateUserItems < ActiveRecord::Migration
  def change 
    create_table :user_items do |t|
      t.integer :user_id
      t.integer :item_id
      t.timestamps null: false
    end
  end
end
```
 
In these migrations, we create three tables with columns. You'll notice the last table is our join table. The `user_items` table has two columns `user_id` and `item_id`.

####MODELS
Next we need to define the appropriate relationships in our three models.

```ruby
class User < ActiveRecord::Base
  has_many :user_items
  has_many :items, through: user_items
end
 
class Item < ActiveRecord::Base
  has_many :user_items
  has_many :users, through: user_items
end
 
class UserItem < ActiveRecord::Base 
  belongs_to :user
  belongs_to :item
end
```

Our `User` class has two associations, the first is a `has_many` association with the join table, and the second is the `has_many through`: with to connect users and items.

We set up a similar pattern of relationships for the `Item` class, this time to connect items to users.

And finally, we have the model for the join table, `UserItem`. In this model, we set up a `belongs_to` for both users and items.

####ACTIVERECORD METHODS
This gives us access to the users who have purchased a particular item, as well as all the items purchased by a specific user.