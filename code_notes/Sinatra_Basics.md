#Sintatra Basics
##What is Sinatra?
####WEB APPLICATIONS
How does Twitter have different pages for each of its 300 million users? How can AirBnB support over 40 million guests with over 1.5 million listings worldwide? All of this is possible because of web applications.

####WHAT'S A WEB FRAMEWORK?
A web application framework (WAF) is a software framework that is designed to support the development of dynamic websites, web applications, web services and web resources. The framework aims to alleviate the overhead associated with common activities performed in web development.

Building dynamic web applications in any language is a complex job requiring intimate knowledge of hundreds of technologies and specifications. The good news, however, is that many of these requirements are universal and every web application must conform to these standards.

For example, any robust web application will need to handle request routing and provide a mechanism for the application to respond to different URLs with the appropriate response. Even a simple blog application has to handle a request to GET '/posts' to show all the recent blog posts vs a request to GET '/authors' to list all the authors.

Similarly, web applications require the ability to render templates to produce consistently structured dynamic content. A GET request to `/posts/1` must render the HTML for the first post just as a request to GET '/posts/2' will render identically structured HTML but with content for the second post. This is possible because of templates.

Web frameworks take all these routine and common requirements of any web application and abstract them into code and patterns that provide these functionalities to your application without requiring you to build them yourself.

Frameworks provide structure and libraries that allow you to focus on **your** application and not **applications in general**. The bigger the framework, the more you can rely on it to provide you with common needs. The smaller the framework, the more you'll have to build things yourself.

####WHAT IS SINATRA?
Sinatra is a Domain Specific Language implemented in Ruby that's used for writing web applications. Created by Blake Mizerany, Sinatra is Rack-based, which means it can fit into any Rack-based application stack, including Rails. It's used by companies such as Apple, BBC, GitHub, LinkedIn, and more.

Essentially, Sinatra is nothing more than some pre-written methods that we can include in our applications to turn them into Ruby web applications.

Unlike Ruby on Rails, which is a Full Stack Web Development Framework that provides everything needed from front to back, Sinatra is designed to be lightweight and flexible. Sinatra is designed to provide you with the bare minimum requirements and abstractions for building simple and dynamic Ruby web applications.

In addition to being a great tool for certain projects, Sinatra is a great way to get started in web application development with Ruby and will prepare you for learning other larger frameworks, including Rails.

####WHY SINATRA BEFORE RAILS
We've all heard of "Ruby on Rails" and how powerful it is. You can build impressive web applications in mere hours! How amazing. Most people, when they learn Rails for the first time, literally say "It's like magic!". But we're developers, and we know that magic isn't real and that other smart developers just built an impressive framework.

That means it's important to understand the basic concepts of Rails before diving into Rails itself. **Enter Sinatra**.

Sinatra is considered a *light weight* framework where the responsibility of app structure and communication falls solely on the developer. Sinatra doesn't give you a lot to get started with. There is no way to auto-generate files and directories, no way for the app to make assumptions about routes, or "Sinatra magic".

Because of this, working with Sinatra allows you to dive in deep with the major concepts of MVC, a system for building web applications that governs 90% of the worlds' apps. You are required to manually set up routes and connect them to other pieces of your application. Without this manual setup, your application does not automatically know how to communicate with your database or what HTML files to load in the browser. And even more importantly, without a manual setup, you lose connection to the major components of a web application, and in particular, all the moving pieces of MVC, a system for building web applications that governs 90% of the worlds' apps.

So introduce yourself to Sinatra. Get to know it, and know it well. The better your foundation, the more you'll be able to know (and like) Rails.

####THE SINATRA DSL
Any application that requires the `sinatra` library will get access to methods like: `get` and `post`. These methods provide the ability to instantly transform a Ruby application into an application that can respond to HTTP requests.

####BASIC SINATRA APPLICATIONS
First, make sure Sinatra is installed by running `gem install sinatra` in your terminal.

The simplest Sinatra application would be:

File: `app.rb`

```ruby
require 'sinatra'
 
get '/' do
  "Hello, World!"
end
```

You could start this web application by running `ruby app.rb`. You'll see something similar to:

```
$ ruby app.rb
== Sinatra (v1.4.6) has taken the stage on 4567 for development with backup from Thin
Thin web server (v1.6.3 codename Protein Powder)
Maximum connections set to 1024
Listening on localhost:4567, CTRL+C to stop
```

This is telling us that Sinatra has started a web application running on your computer listening to HTTP requests at port `4567`, the Sinatra default. If you start this application and navigate to http://localhost:4567 you'll see "Hello, World!" in your browser. Go back to your terminal running the Sinatra application and stop it by typing `CTRL+C`. You should see:

```
Listening on localhost:4567, CTRL+C to stop
^CStopping ...
Stopping ...
== Sinatra has ended his set (crowd applauds)
```

This is the most basic Sinatra application structure and is actually pretty uncommon. More commonly, Sinatra is used in a modular style encapsulated by Controller Classes and booted via the `config.ru` Rack convention.

##Sinatra from Scratch

###BASIC SINATRA APP
####SINATRA GEM
It's important to note that Sinatra is just a gem. It's a library of code that developers wrote to allow us to build light-weight web applications quickly. If you take a look at our `Gemfile` (a list of all the gems our application uses), you will see the Sinatra gem listed.

The first thing you need to do is enter in terminal `bundle install`. Just like software has different versions that require you to update your mobile apps, gems have newer versions. `bundle install` will lock in the current versions of the gems for your application, that way if any updates happen, your app won't break. It keeps the versions locked in a file called `Gemfile.lock` that is created for you.

**`APP.RB`**

The `app.rb` file is the heart and soul of a Sinatra application. This is our application controller. The application controller handles all incoming requests to our app, and sends back the appropriate responses to the client.

The first line of `app.rb` is just requiring the Sinatra gem so that we can incorporate its functionality.

On the next line, we define a class `App` and have it inherit from `Sinatra::Base`. This way, any instance of our class App will have all the functionality of the Sinatra class.

Inside our class we have a Sinatra method define, or controller action. This method responds to a `GET` request to the root url and displays the text `Hello, World!` in the browser.

**STARTING THE APP**

To actually check if our app is working in the browser, enter `rackup app.rb` in your terminal.

Sinatra relies on Rack for its middleware. Because we have the Sinatra gem listed in our Gemfile, we automatically have the Rack middleware setup.

Once your server is running, visit `localhost:9292` in the browser to see `Hello, World!` displayed.

##Sinatra Basics
####MODULAR SINATRA APPLICATIONS
Web applications, even simple Sinatra ones, tend to require a certain degree of complexity. For example, your application might have multiple routes, route-handlers, and configuration. To handle this, Sinatra is more commonly used through the Modular Sinatra Pattern (over the classical, single file `app.rb` pattern).

**`CONFIG.RU`**

The first new convention this pattern introduces is a `config.ru` file. The purpose of `config.ru` is to detail to Rack the environment requirements of the application and start the application.

A common 'config.ru' might look like:

File: `config.ru`

```ruby
require 'sinatra'
 
require_relative './app.rb'
 
run Application
```

In the first line of `config.ru` we load the Sinatra library. The second line requires our application file, defined in `app.rb`. The last line of the file uses `run` to start the application represented by the ruby class `Application`, which is defined in `app.rb`.

SINATRA CONTROLLERS: APP.RB
config.ru requires a valid Sinatra Controller to run. A Sinatra Controller is simply a ruby class that inherits from Sinatra::Base. This inheritance transforms into a web application by giving it a Rack-compatible interface behind the scenes via the Sinatra framework. Open app.rb
class Application < Sinatra::Base
 
  get '/' do
    "Hello, World!"
  end
 
end
We simply create a new class Application and inherit from Sinatra::Base.Our class constant could have been anything descriptive of the functionality provided by the class. The classes that inherit from Sinatra::Base and define the HTTP interface for our application are called Controllers.
In a single controller application, a single file defining the controller, like app.rb, will suffice. That controller will define every single URL and response of our application.
Controllers define an HTTP method using the sinatra routing DSL provided by methods like get and post. When you enclose these methods within a ruby class that is a Sinatra Controller, these HTTP routes are scoped and attached to the controller.
The final step in creating a controller is mounting it in config.ru. Mounting a controller means telling Rack that part of your web application is defined within the following class. We do this in config.ru by using run Application where run is the mounting method and Application is the controller class that inherits from Sinatra::Base and is defined in a previously required file.
The class name we defined in our application controller (app.rb) is just a normal Ruby class. We could have named it MyToDoApp:
class MyToDoApp < Sinatra::Base
 
  get '/' do
    "Hello, World!"
  end
 
end
If this was our class name, we would need to change config.ru to run the appropriate class:
run MyToDoApp

