#MVC and Forms
##Intro to MVC
###MODEL-VIEW-CONTROLLER

[MVC Overview](https://www.youtube.com/watch?v=IT54uYUISQg)

We could create a web application in one file, with thousands of lines of code in the same document. It would work. But it would also present us with some very big challenges. It would be close to impossible to debug our program, and our code would be virtually unreadable.

Instead, we use frameworks (Sinatra being one of them), to separate an application's code by function and make writing, reading, and debugging code a much more pleasant and simple experience.

The Model-View-Controller paradigm is a popular way of building frameworks for web applications - it provides a *separation of concerns* where groups of files have specific jobs and interact with each other in very defined ways. In a nutshell:

+ **Models**: The 'logic' of a web application. This is where data is manipulated and/or saved.

+ **Views**: The 'front-end', user-facing part of a web application - this is the only part of the app that the user interacts with directly. Views generally consist of HTML, CSS, and Javascript.

+ **Controllers**: The go-between for models and views. The controller relays data from the browser to the application, and from the application to the browser.

####THE RESTAURANT ANALOGY
If you've ever been to a restaurant, you'll know that there is a clear separation of concerns that takes place. The person cooking the food is not the same as the person delivering the food, and the person eating the food is someone completely different. Let's think of MVC as if it were a restaurant.

**MODELS**

First, there are the cooks (the models) that make the food. They take orders (from the waiter), and prepare the customer's meal. Once ready, they give it to the waiter to deliver to the customer.

In Sinatra, models are generally written as Ruby classes. Models can also connect to databases to persist data. Think of models as the main logic behind your web application.

**VIEWS**

The customers (views) place orders and receive food. The orders are placed with the waiter, who takes them back to the kitchen.

In Sinatra, views are written as `.erb` files, consisting of HTML and embedded Ruby (Ruby code written within html). They are what the user actually sees when they use your web application.

**CONTROLLERS**

The waiters (controllers) shuttle between the kitchen and the front of the restaurant. They take requests from the customer to the kitchen, and take prepared meals from the kitchen to the customer. Without the waiter, our customers would be hungry and our chefs would have nothing to do.

In Sinatra, controllers are written in Ruby and consist of 'routes' that take requests sent from the browser ("GET this data", "POST that data"), run code based on those requests by using models, and then render the erb (view) files for the user to see.

##Sinatra MVC File Structure
####KEEPING CODE ORGANIZED
We could, if we wanted to, write our entire app in a single file. As you might imagine, this would make things very difficult to read and debug.

Keeping our code organized is crucial when developing complex applications. This concept is called `separation of concerns` and `single responsibility`. Each file in our application will have a different responsibility and we'll keep these responsibilities split up into reasonable chunks.

You'll be coding along in this lesson so fork and clone this lab. There are tests to run to make sure your solutions are working. Find the instructions underneath the descriptions for each file you'll be editing.

####WHAT DOES A SINATRA MVC FILE STRUCTURE LOOK LIKE?
Take a look at the file structure in this directory. It's okay if it feels overwhelming at first. We're going to walk through the different files and folders and discuss what their responsibilities are.

```
├── Gemfile
├── README.md
├── app
│   ├── controllers
│   │   └── application_controller.rb
│   ├── models
│   │   └── model.rb
│   └── views
│       └── index.erb
├── config
│   └── environment.rb
├── config.ru
├── public
│   └── stylesheets
└── spec
    ├── controllers
    ├── features
    ├── models
    └── spec_helper.rb
```

**`GEMFILE`**

This holds a list of all the gems needed to run the application. The bundler gem provides us access to a terminal command: `bundle install`. Bundler will look in the Gemfile and install any gems, as well as any gem dependencies for this application.

Go ahead and enter this command in terminal. It will create a `Gemfile.lock` file for you, which is just a documentation of what versions of the gem you have installed and should use. The lock word is actually because it makes sure that only one thing is running bundle install at a time.

**`APP` DIRECTORY**

This folder holds our MVC directories - `models`, `views`, and `controllers`. We spend most of our time coding in this directory.

**`MODELS` DIRECTORY**

This directory holds the logic behind our application. Typically, these files represent either a component of your application, such as a User, Post, or Comment, or a unit of work. Each file in models typically contains a different class. For example, `dog.rb` would contain a class called `Dog`. As you might have guessed, models represent the "M" components of the MVC paradigm.

Models represent the data and object logic of our application.

Create a new file in the models directory to create a dog class. This class should have name, breed, and age attributes which can be set on initialization. You should be able to read and write to these attributes.

**`CONTROLLERS` DIRECTORY**

The controllers, such as `application_controller.rb`, are where the application configurations, routes, and controller actions are implemented. There is typically a class, which in this case we will call `ApplicationController`, that represents an instance of your application when the server is up and running. The `application_controller.rb` file represents the "C" components of the MVC paradigm.

(In some simple applications -including several labs and code-alongs in this track - the Application Controller will simply be called `app.rb` and will live in the root directory of the project)

Sometimes our other controllers will use `ApplicationController` as an inheritance point so that they inherit all the defaults and behaviors defined in our main `ApplicationController`. Other times our other controllers will simply inherit from `Sinatra::Base`.

Controllers represent the application logic, generally, the interface and flow of our application.

Let's go ahead and fill in our controller. You'll notice in `application_controller.rb`, we have a class `ApplicationController` which inherits from `Sinatra::Base`. When we start up a server, the server will spin up an instance of the `ApplicationController` class to run our app.

You'll also notice there is a `configure` block already in the controller. This configure block tells the controller where to look to find the views (your pages with HTML to display text in the browser.) and the public directory.

```ruby
class ApplicationController < Sinatra::Base

  configure do
  	set :views, "app/views"
  	set :public_dir, "public"
  end

  get "/" do
  	erb :index
  end
end
```

When a client makes a request to a server to load an application, the request is received and processed by the controller. We need to set up a controller action to accept the request and respond with the appropriate HTML.

We've created a controller action that can receive and respond to a `GET` request to the root URL `'/'`. This `GET` request loads the `index.erb` file.

**`VIEWS` DIRECTORY**

This directory holds the code that will be displayed in the browser. In a Sinatra app we use `.erb` files instead of `.html` files because `.erb` files allow us to include regular, old HTML tags AND special erb tags which contain Ruby code. We can name them anything we like, but by convention, our file names will match up with the action that renders them. For example, a GET request to `/` typically renders a file called `index.erb`.

Views represent how things look and are displayed in our application. We've created a file `index.erb` that contains some some basic HTML code.

We've already told the controller how to load this file in the view.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Sinatra MVC</title>
  </head>

  <body>
    <h1>Index</h1>
    <p> I love programming </p>
  </body>
</html>
```

**`CONFIG.RU` FILE**

A `config.ru` file is necessary when building Rack-based applications and using `rackup`/`shotgun` to start the application server (the ru stands for rackup).

`config.ru` is first responsible for loading our application environment, code, and libraries.

Once all our code is loaded, `config.ru` then specifies which controllers to load as part of our application using `run` or `use`.

```
require_relative './config/environment'

run ApplicationController
```

In this case, our `config.ru` file has this line `run ApplicationController`, creating an instance of our ApplicationController class which can respond to requests from a client.

**`CONFIG` DIRECTORY**

This directory holds an `environment.rb` file. We'll be using this file to connect up all the files in our application to the appropriate gems and to each other.

This `environment.rb` file loads Bundler and thus all the gems in our Gemfile, as well as the `app` directory.

```ruby
ENV['SINATRA_ENV'] ||= "development"
ENV['RACK_ENV'] ||= "development"

require 'bundler/setup'
Bundler.require(:default, ENV['SINATRA_ENV'])

require_all 'app'
```

**`PUBLIC` DIRECTORY**

The `public` directory holds our front-end assets. In the example above, it holds a `css` directory with a stylesheet. Javascript directories and any other front-end assets (like image files) should also be stored in `public`.

**`SPEC`** DIRECTORY

The `spec` directory contains any tests for our applications. These tests set up any expectations for the rest of the project. These are often broken down into unit tests for models, controller tests for routes, and feature tests, which check the actual behavior for users.

##Sinatra Views
####PART 1: RENDERING HTML
Rendering plain text is a great way to test the behaviors of our routes, but it doesn't give us any control over how the content is displayed. We'd like to structure our content using HTML and render that to the browser instead. The most basic way to do this is to include html tags as a part of the string we're rendering. If you haven't already, fork, clone, and open this lab. In the "/" route of our `app.rb` file, try the following:

```ruby
    get '/' do
      "<h1>Hello World</h1>"
    end
```

Run `shotgun` and go to `http://localhost:9393`. You're "Hello World" should now appear as an `h1`. Nice!

####PART 2: USING AN ERB FILE
You can imagine that writing HTML this way would get very messy, very quickly. Luckily, most web frameworks include a templating engine that allows us to render HTML content from a different file. We call these files "views." Views represent what the user sees and makes up the "V" of our "MVC".

By default, Sinatra uses a templating engine called ERB, or Embedded Ruby. Our view files will use the extension `.erb` and we can write HTML code there just like in a plain old `.html` file. Sinatra is also configured by default to look for our `.erb` files in a directory called `views`.

Create a new file called `index.erb` inside of the `views` directory. Add the following code into that file.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>My First View!!</title>
    </head>
    <body>
        <h1>Hello World</h1>
        <p>This HTML code is inside of a '.erb' file in the views directory, where it belongs.</p>
    </body>
</html>
```

Now, we just need to update our controller to render the `index.erb` file at the "/" route. The syntax for this is as follows:

```ruby
    get '/' do
      erb :index
    end
```

This tells Sinatra to render a file called `index.erb` inside of a directory called `views`. Save your files and refresh your preview to see your changes. Awesome, right?

####PART 3: MULTIPLE ROUTES WITH MULTIPLE VIEWS
We can create as many routes and views as we want. Let's create a route called "/info" in our controller.

```ruby
    get '/' do
      erb :index
    end
 
    get "/info" do
      "Testing the info page"
    end
```

With `shotgun` running, head to `http://localhost:9393/info`. You should see "Testing the info page" rendered there. This lets us know that our route is defined properly. Next, let's have this route render a separate file instead. Inside of the `views` directory, create a file called `info.erb`. Add whatever HTML code you like.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Info Page</title>
    </head>
    <body>
        <h1>Info Page</h1>
        <p>This is the info page: here's some information about me!</p>
    </body>
</html>
```

Finally, update our controller to render that file.

```ruby
    get '/' do
      erb :index
    end
 
    get "/info" do
      erb :info
    end
```

It's important to note that the name of the file doesn't have to match the name of the route. For example, if we wanted our "/info" route to render a file called `dogs.erb`, we could do so like this:

```ruby
    get '/' do
      erb :index
    end
 
    get "/info" do
      erb :dogs
    end
```

By convention though, we keep our routes and our erb files named the same. This makes it easier to keep track of as our projects get bigger.