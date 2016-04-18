##Rails Application Basics

###WHAT IS RUBY ON RAILS?
+ **A web framework** - A web framework provides developers the tools they need in order to build applications. While every application is unique there are certain components that can be found in almost every application, such as: routing, asset management, database connections, and the list goes on. A good web framework gives developers these baseline tools so that they don't have to create the base application functionality for each new project.

+ **A Ruby Gem** - At its core, Ruby on Rails is simply a set of Ruby code libraries, and since the entire codebase is open source you have the ability to review the framework to better understand how it works.

+ **A MVC framework** - MVC stands for Model-View-Controller, this essentially means that Rails takes advantage of the popular application architecture that helps developers naturally separate concerns and organize their applications properly. This setup encourages a specific set of conventions, such as placing the logic for the application in the model files, the code flow in the controllers, and the views to simply display content to the user.

###WHAT RUBY ON RAILS IS NOT
+ **A programming language** - This is one of the most common misconceptions. Ruby on Rails is not a programming language; instead it is a set of code libraries built in Ruby.

+ **A slow framework** - Due to the fact that Rails is one of the most straightforward frameworks to learn, it can lead to a number of poor coding practices from beginners. However if built properly, Rails projects can be as fast as any other framework. Furthermore, with Rails service based architecture it makes it a perfect candidate for microservice applications, which can be some of the fastest and best performing applications on the web.

###CREATING YOUR FIRST RAILS PROJECT
Before you continue, this assumes that you have Ruby, RubyGems, and Bundler installed on your system

###INSTALLING THE RAILS GEM
As mentioned above, Rails is simply a Ruby Gem, to install it on your system, run the following command in the terminal of your computer:

`gem install rails`

Depending on your system configuration you may need to prepend the command with `sudo` to install the gem as the root user. Once the gem is installed you can create Rails applications!

###GENERATING A NEW RAILS APPLICATION
Our application is going to be called BlogFlash. To create the application, run the following command:

`rails new blog-flash`

There are a number of common naming conventions for Rails app names. Typically you will want to use all lower case letters, separated by '-', as shown in our `blog-flash` naming structure. In the same way that there are rules for naming methods, variables, classes, etc. in Ruby, there are naming rules for application names. Since the application name is used as the app constant and throughout the application, the best approach is to keep your naming simple and to follow a standard naming practice.

####RAILS FILE STRUCTURE OVERVIEW
Since you will be working with this file structure on a daily basis, it is very important to understand and become familiar with the file system. Below is a breakdown for each directory:

+ **app** – contains the models, views, and controllers, along with the the rest of the core functionality of the application. This is the one directory where you can make a change and not have to restart the Rails server. The majority of your time will be spent working in this directory. In addition to the full MVC structure, this directory also contains non Ruby files, such as: css files, javascripts, images, fonts, etc.

+ **bin** – some built-in Rails tasks that you most likely will never have to work with.

+ **config** – the config directory manages a number of settings that control the default behavior, including: the environment settings, a set of modules that are initialized when the application starts, the ability to set language values, the application settings, the database settings, the application routes, and lastly the secret key base.

+ **db** – within the db directory you will find the database schema file that lists the database table, their columns and the column’s associated data types. The db directory also contains the seeds.rb file, which lets you create some data that can be utilized in the application. This is a great way to quickly integrate data in the application without having to manually add records through a web form element. The schema file can be found at `db/schema.rb`

+ **lib** – while many developers could build full applications without ever entering the lib directory, you will discover that it can be incredibly helpful. The lib/tasks directory is where custom rake tasks are created. You have already used a built-in rake task when you ran the database creation and migration tasks; however, creating custom rake tasks can be very helpful and sometimes necessary. An example of a rake task I have created recently was a rake task that I had the server run in the background that called an outside API and synced the API data into the application’s database.

+ **log** – within the log directory you will discover the application logs. This can be helpful for debugging purposes, but for a production application I will typically use an outside service since they can offer more advanced services such as querying and alerts.

+ **public** – this directory contains some of the custom error pages, such as 404 errors, along with the robots.txt file which will let developers control how search engines index the application on the web.

+ **test** – by default Rails will install the test suite in this directory. This is where all of your specs, factories, test helpers, and test configuration files can be found. *Side note: We always use RSpec, which means this directory will actually be called `spec`.*

+ **tmp** – this is where the temporary items are stored and is rarely accessed by developers.

+ **vendor** – this directory has been utilized for varying purposes in the past, in Rails 4+ its main purpose is for integrating client side MVC frameworks, such as AngularJS.

+ **Gemfile** – the Gemfile contains all of the gems that are included in the application; this is where you will place outside libraries that are utilized in the application. After any change to the Gemfile you will need to run: bundle. This will call in all of the code dependencies into the application. The Gem process can seem like a mystery to new developers, but it is important to realize that the Gems that are brought into an application are simply Ruby files that help extend the functionality of the app.

+ **Gemfile.lock** – this file should not be edited; it displays all of the dependencies that each of the Gems contain along with their associated versions. There have been times where I discovered application bugs due to varying Gem’s dependencies.

+ **README.rdoc** – the readme file is an important place to document the details of the application. If the application is an open-source project, this is where you can place instructions to other developers, such as how to get the app up and running locally.

####CREATING THE DATABASE
Before we can startup the rails server, first you can create the database by running:

`rake db:create`

####STARTING UP THE RAILS SERVER
To startup the Rails server, make sure that you are in the root of the application in the terminal and run:

`rails s`

This will startup the rails server and you will see output such as the following:

```
=> Booting WEBrick
=> Rails 4.2.3 application starting in development on http://localhost:3000
=> Run `rails server -h` for more startup options
=> Ctrl-C to shutdown server
[2015-11-14 22:16:54] INFO  WEBrick 1.3.1
[2015-11-14 22:16:54] INFO  ruby 2.1.2 (2014-05-08) [x86_64-darwin13.0]
[2015-11-14 22:16:54] INFO  WEBrick::HTTPServer#start: pid=3080 port=3000
```

Now that the server is running properly, you can go and verify that it's working properly in the browser by going to: `http://localhost:3000/`

Here you will see the 'Welcome aboard' page that ships with Rails and it shows that we're ready to start building the application!

####USING THE RAILS CONSOLE
The Rails console is an important tool in the arsenal for any Rails developer, it gives you a direct connection to your application's ecosystem and lets you perform tasks such as:

+ Run database queries
+ Run application code
+ Perform full CRUD tasks with the database
+ Allows you to switch between making permanent database changes and running in a sandbox mode to test scripts out.

To startup the rails console, run the following command in the terminal:

`rails c`

This will open up a new Rails console session. We don't have any database tables or records yet, so we can't perform queries (but don't worry, we'll get to that soon enough!), but we can test it out to make sure that we can access methods from within the application.

Rails ships with a great set of view helpers, one of my favorites is the `pluralize` method that takes in a word and returns the plural value. So we can test that out in the console to make sure it's working. Run the following command in the Rails console:

`helper.pluralize(5, 'laptop')`

This should return "5 laptops", if you switch the 5 to 1, it will return "1 laptop", pretty cool, right? This means that the Rails console is working, to close the session run the command `control + d` and it will return you to the regular terminal session.

##Rails MVC
####MODEL VIEW CONTROLLER OVERVIEW
In order to ensure that applications have a specific organizational structure, the creators of Rails utilized the model-view-controller architecture. This concept can be a little abstract if you've never used it before, so let's give it a real world analogy.

In a restaurant kitchen we have three key components:

+ A chef that makes the food
+ A waiter that takes the order and brings out the food
+ A table where the food is served to the customer

Using this as an analogy, we can assign the following roles to each of our restaurant components:

+ **Model** - The model is the chef, it should manage the critical aspects of the application. One of my favorite tasks in a Rails application is working with the model files. This is where you can be very expressive with the custom algorithms that you want to utilize and you also have direct access to the specific database record. The logic of your application should mainly reside in the model files.

+ **Controller** - The controller is the waiter. In the same way that the waiter takes the order from the customer to the chef and then brings the food to the table, the controller transmits data requests from the user to the model, and then delivers data that is rendered in the view to the user.

+ **View** - The view is the table. A table shouldn't do anything besides sit there and hold the food when it is delivered. In like manner the views should not contain any programming logic, they should simply render what the controller sends it. One of the top issues I discover when I review a Rails application that has a large number of bugs is that the developer integrated too much logic directly into the view.

####ROUTING, FILE NAMING CONVENTIONS, AND DATA FLOW
Rails was created with the concept of convention over configuration and this holds true for how the MVC structure was setup. View files correspond directly to controller files, which speak directly with models. As an example, imagine that you have a blog that has a database table called `posts`. You will have the following set of files:

+ A `post.rb` model file that will contain: validations, database relationships, callbacks, and any custom logic for posts.

+ A `posts_controller.rb` file that will have methods that will manage data flow for the Post behavior, this will include the full set of CRUD features, the standard methods are: index, new, create, show, edit, update, and destroy.

+ A `views/` directory that will contain a corresponding view for each of the pages that the end user will access. For a CRUD based model, a few of the standard views would include: an index view to show all records, a show page that shows a specific record, and then new and edit pages that both render a form.

####REQUEST FLOW
![MVC Request Flow](https://s3.amazonaws.com/flatiron-bucket/readme-lessons/mvc_flow_updated.png)

####ROLES AND RESPONSIBILITIES

#####MODELS
At the end of the day the model file is a Ruby class. If it has a corresponding database table it will inherit from the `ActiveRecord::Base` class, which means that it has access to a number of methods that assist in working with the database. However, you can treat it like a regular Ruby class, allowing you to create methods, data attributes, and everything else that you would want to do in a class file. In a typical model file you will find your application's domain logic, extending the restaurant analogy the chef (your model) performs a number of tasks to create each meal that the waiter (controller) and especially the table (views) don't know anything about. Some of this domain logic would include items such as complex database queries, data relationships and custom algorithms.

It is important to remember to follow the single responsibility principle for your model class files. If any of the methods that you place in the model file perform tasks outside the scope of that specific model, they should probably be moved to their own class.

#####CONTROLLERS
As mentioned before, the controller is like the waiter in a restaurant. The controllers connect the models, views, and routes. To make the process more straightforward, think in terms of the following process:

+ The view looks to the controller and only has access to the instance variables that the controller makes available. Those instance variables will contain any/all data coming in from the database.

+ The routes file looks to the controller and ensures that the methods in the controller match the items in the routes file.

Remembering our restaurant analogy, the easiest way to think of the controller is that it manages data flow between the router, model, and views, in the same way that a waiter takes the order from a patron, relays the order details to the chef, and brings the meal out to the table.

#####VIEWS
In a Rails application, the view files should contain the least amount of logic of any of the files in the model-view-controller architecture. The role of the view is to simply render whatever it is sent from the database.

Rails also does a great job of supplying built in ActionView helper methods that you can implement to efficiently code the views. For example, if you wanted to create a `div` for a set of blog posts that you want to iterate over, you can implement the following code:

```ruby
<%= div_for(@post, class: "post-index-page") do %>
  <p><%= @post.title %> <%= @post.summary %></p>
<% end %>
```

Which is translated to the following HTML markup:

```html
<div id="post_42" class="post post-index-page">
  <p><strong>My Amazing Blog Post</strong> With an incredible summary</p>
</div>
```

Notice how the Action View helper enabled us to dynamically set the HTML tags without having to write any HTML code at all? You will discover that this is a very helpful tool as your views grow in size, the more Ruby you can write and the less HTML the cleaner your views will be, which results in them being easier to manage and scale.

##Rails Static Request
###ROUTING
How does your application know what view to render to users? This is where routing comes in. As a framework, Rails has a comprehensive routing system for both dynamic and static pages. Below are the differences between a static and dynamic route:

+ **Static route** - A static route will render a view that does not change, you typically will not send parameters to it. Examples would be a site's about or contact pages.

+ **Dynamic route** - Dynamic routes are pages that accept parameters and render different content based on those parameters. An example would be a blog's post page that contains a specific article.

In this lesson we're going to specifically cover static pages to ensure that you can get a firm understanding of how routing works in a Rails application.

Before we dive into the code and routing configurations, it helps to know how HTTP works at a high level. Below is the flow that takes place when a user attempts to go to a page on a Rails application:

1. A URL is entered into the browser, this is the HTTP request

2. That request is sent to the server where the application's router interprets the request and sends a message to the controller mapped to that route

3. The controller communicates with the view file mapped to the controller method

4. The server returns that HTTP response, which contains the view page that can be viewed in the browser

####IMPLEMENTING A STATIC ROUTE
Let's try this out in our application, I'm going to use a blogging application as a case study in this lesson.

To begin, startup the rails server and go to `localhost:3000/about` as you will see this throws a routing error: `No route matches [GET] "/about"`, to fix this, stop the rails server by pressing `control + c` (anytime you make a routing change you need to restart the rails server)

Now draw the route by opening the `config/routes.rb` file and add the following route inside of the `draw` block:

```ruby
get 'about', to: 'static#about'
```

Let's look at the components that makeup this route code:

+ **The HTTP verb** - in this case we're using the `get` HTTP verb

+ **The path** - `about` represents the path in the URL bar that the route will be mapped to

+ **The controller action** - `static#about` tells the Rails routing system that this route should be passed through the `static` controller's `about` action. If the term `action` sounds foreign, actions are just ruby speak for a method in a controller. So in the `StaticController` will be a method called `about` that gets called when a user goes to `/about`

Now start the rails server back up and go back to `localhost:3000/about` and click refresh, you should now see that the error message has changed, it's no longer complaining about not having a route, the error should now say: `uninitialized constant StaticController`

Let's fix this by creating a new controller for our static pages, add a new file to the application: `app/controllers/static_controller.rb`

This will create a blank controller file that we can use to map to the routing file. Since there are a number of methods built into the Rails controller system, you will also want the controller to inherit from the application controller. The new file should have code that looks like this:

```ruby
class StaticController < ApplicationController
end
```

The standard naming convention for controllers is the name of the controller followed by the word `Controller`.

If you refresh the browser now you will see a new error: `The action 'about' could not be found for StaticController`. This means that it found our controller (woot!) but couldn't find the action `about` in that controller. On a side note, since controllers are located within the `app` directory, you can make changes to controller files and see the result in the browser without having to restart the Rails server.

We're making good progress (even though we're using EDD - error driven development), and it's good to see each of the errors so that when you encounter these in your real world projects you will know how to fix them. This current error is fixed by adding the following method in the static controller:

```ruby
def about
end
```

Hitting refresh in the browser will give you a 'Template is missing' error, specifically it says: `Missing template static/about....` Also note that you do not have to restart the Rails server here, as long as your changes are within the `app` directory you can keep the server going, only code changes outside of the `app` directory require stopping and starting the Rails server.

We're very close to getting our view to show up. Rails gives us two options for how views are mapped between the controller and view files. It's important to understand the difference between explicit and implicit rendering for the views:

+ **Explicit rendering** - for explicit rendering, Rails lets you dictate what view file that you want to have the controller action mapped to.

+ **Implicit rendering** - for implicit rendering, Rails follows a standard convention that automatically looks for the view file with the same name as the controller action.

First let's try out explicit rendering, create a new directory in the view's directory called `static` and create a new file called `some_page.html.erb`. In that file add some basic HTML code, such as:

```html
<h1>Hello from some page</h1>
```

Inside the `about` method in the controller add the following code:

`render "static/some_page"` you can also use: `render "some_page"` (Rails will automatically look inside the view directory with the same name as the controller, but you can also give the full view path). It's typically considered a better practice to use the `render "some_page"` syntax, since it won't rely on the name of the directory (in case it gets changed later on). So the `about` method should look something like this:

```ruby
def about
  render "static/some_page"
end
```

If you refresh the `/about` page in the browser you will see our heading of **Hello from some page**

To compare that with how Rails utilizes implicit view rendering, create a new file in the static view directory called: `about.html.erb` and add some HTML code such as:

```html
<h1>Hello from the about page</h1>
```

Now in the controller, remove the `render` call completely. If you go and refresh the browser you will now see **Hello from the about page**.

Woah! How is an empty method generating the same behavior as when we were calling the view template directly? This follows along with the popular 'convention over configuration' pattern that Rails utilizes. This means that the Rails core team has built out a number of standardized processes, such as implicit view rendering to help make development life a little easier. It's not some kind of black code magic, under the scenes Rails has a large number of complex processes that make processes like this view rendering work properly.

So is explicit or implicit better? Typically you will discover that you will want to utilize the implicit workflow in your day to day coding practice, the rationale is quite practical. Imagine that you are taking over a legacy Rails project, as you are getting acclimated to the code, would you prefer that the previous dev followed a standard naming process or would you rather be forced to looking through each controller code to see how the controller actions were mapped to the views? Rails has always had the goal of making the development process as efficient as possible, which is why it is typically best to follow these types of implicit procedures. With that being said, it is important to understand how the views are mapped to the controller, which is why we walked through the explicit process.

####SUMMARY
In summary, you should now have a firm understanding of how to implement basic routing in your application for static pages, as a review, the process is below:

1. The server receives an HTML request from the client
2. The application processes the request through the route file
3. The route file maps the request through whatever controller method is called
4. The controller then responds with the view that belongs to that specific method and delivers it to the client