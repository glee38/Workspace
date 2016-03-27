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

**SINATRA CONTROLLERS: `APP.RB`**

`config.ru` requires a valid Sinatra Controller to `run`. A Sinatra Controller is simply a ruby class that inherits from `Sinatra::Base`. This inheritance transforms into a web application by giving it a Rack-compatible interface behind the scenes via the Sinatra framework. Open `app.rb`

```ruby
class Application < Sinatra::Base
 
  get '/' do
    "Hello, World!"
  end
 
end
```

We simply create a new class `Application` and inherit from `Sinatra::Base`. Our class constant could have been anything descriptive of the functionality provided by the class. The classes that inherit from `Sinatra::Base` and define the HTTP interface for our application are called Controllers.

In a single controller application, a single file defining the controller, like `app.rb`, will suffice. That controller will define every single URL and response of our application.

Controllers define an HTTP method using the sinatra routing DSL provided by methods like `get` and `post`. When you enclose these methods within a ruby class that is a Sinatra Controller, these HTTP routes are scoped and attached to the controller.

The final step in creating a controller is mounting it in `config.ru`. Mounting a controller means telling Rack that part of your web application is defined within the following class. We do this in config.ru by using `run Application` where `run` is the mounting method and `Application` is the controller class that inherits from `Sinatra::Base` and is defined in a previously required file.

The class name we defined in our application controller (`app.rb`) is just a normal Ruby class. We could have named it `MyToDoApp`:

```ruby
class MyToDoApp < Sinatra::Base
 
  get '/' do
    "Hello, World!"
  end
 
end
```

If this was our class name, we would need to change `config.ru` to run the appropriate class:

```ruby
run MyToDoApp
```

##Using the Shotgun Development Server
####SETUP
Make sure you run `bundle install` to install the gems in our `Gemfile`. If your operating system is OSX El Capitan, and you have an issue installing `EventMachine`, first check to make sure Open SSL is installed by entering `brew install openssl` in terminal. Once it's installed, enter `brew link openssl --force`.

####WHY SHOTGUN
[Shotgun](https://github.com/rtomayko/shotgun) is a small Ruby gem that makes it easier to develop Rack-based Ruby web applications locally by starting Rack with automatic code reloading. A gem is just a library of code that developers wrote and made free and available to the public. This gem lets us start Rack to have a development server running to test our app.

When you start an application with `shotgun`, all of your application code will be reloaded upon every request. That means if you change anything in your code and save it, when you hit 'Refresh' in your browser, your application will respond with the latest version of your code.

####INSTALLING SHOTGUN
You can install shotgun via `gem install shotgun`. You should also require it in an application's `Gemfile` so that you can install it and load it via Bundler. Shotgun is already in your gemfile, so go ahead and enter `bundle install` in terminal if you haven't already.

####STARTING AND STOPPING SHOTGUN
Within a rack or Sinatra application directory, you can start the application via shotgun by simply executing `shotgun` in your terminal. Use `shotgun -o 0.0.0.0` within the Nitrous IDE. You should see something like:

```
$ shotgun
== Shotgun/Thin on http://127.0.0.1:9393/
Thin web server (v1.6.3 codename Protein Powder)
Maximum connections set to 1024
Listening on 127.0.0.1:9393, CTRL+C to stop
```

That means your application is loaded and being served from port `9393`, the default shotgun port. The application will respond to requests at `http://127.0.0.1:9393` or more commonly, `localhost:9393` and will reload your code on every request until the process is terminated by typing `CTRL+C`. Go ahead and visit `localhost:9393` in the browser.

A port is just an endpoint on the server that is open for communication. It's typical for a server to regulate the open ports to make sure it can monitor requests appropriately. You'll notice that with rackup you visited the port `9292` but with shotgun `9393`.

A working server responding to a request and then exiting looks like:

![shotgun_server](https://dl.dropboxusercontent.com/s/0dwm67kbwvbope1/2015-09-15%20at%2011.12%20PM.png)

You can pass to `shotgun` most CLI arguments and flags that `rackup` accepts.

Now, with your shotgun server still running, change the text in the string in the controller action:

```ruby
get '/' do
  "Started my server using shotgun!"
end
```

Save your changes to `app.rb` and visit `localhost:9393` in the browser. This time, you should see the text `Started my server using shotgun`! as opposed to seeing the previous text, just like with rackup.

####TROUBLESHOOTING SHOTGUN
#####COMMAND NOT FOUND
When you run `shotgun` from your terminal you might get a Shell error that reads something like:

```
$ shotgun
-bash: shotgun: command not found
```

That means the shotgun gem isn't properly installed or available in your PATH. Try fixing it with:

```
$ gem install shotgun
```

If you still get that error, from within your application directory, try running:

```
$ bundle install
```

followed by

```
$ bundle exec shotgun
```

#####BUNDLER ERROR
You might get an error about `bundler` that will tell you to run `bundle install`. 

It'll look like this:

```
$ shotgun
bundler: command not found: shotgun
Install missing gem executables with `bundle install`
```

Just run `bundle install` and try `shotgun` again. If you still get the error try running via:

```
$ bundle exec shotgun
```

#####PORT IN USE
You also might get an error about a port being in use. It'll look like this:

```
$ shotgun
== Shotgun/Thin on http://127.0.0.1:9393/
Thin web server (v1.6.3 codename Protein Powder)
Maximum connections set to 1024
Listening on 127.0.0.1:9393, CTRL+C to stop
/Users/avi/.rvm/gems/ruby-2.2.2/gems/eventmachine-1.0.8/lib/eventmachine.rb:534:in `start_tcp_server': no acceptor (port is in use or requires root privileges) (RuntimeError)
```

This means you have another shotgun server running somewhere. Do you have another Terminal or Shell open running a web application or shotgun? You need to find that process or tab that is running a web application using shotgun and shut it down with `CTRL+C`.

You can also give `shotgun` a different port to use with the `-p` flag.

```
$ shotgun -p 4200
== Shotgun/Thin on http://127.0.0.1:4200/
Thin web server (v1.6.3 codename Protein Powder)
Maximum connections set to 1024
Listening on 127.0.0.1:4200, CTRL+C to stop
```

You'll notice the server started on port `4200` which is hopefully unoccupied. You can supply any port number. But it's best to terminate your servers rather than just start 100s on different ports.

##Sinatra Routes
####WHAT ARE ROUTES?
Every time a person clicks on a link, types in a URL, or submits a form, they are making HTTP requests to a specific URL on your application. Routes are the part of an application that connect HTTP requests to a specific method in your application code built to handle responding to such a request (that part of code is called a Controller Action).

Let's say we have a web application that helps doctors track the medications they've prescribed. The doctor would need an easy way to see all the medicines she's prescribed.

In the application's navigation is a link "All Medicines". When the user clicks on that link, it directs the browser via the link's `HREF` attribute to the URL "/medicines" on the domain. The URL "/medicines" must trigger the method in your application built to load the appropriate medicines and send an HTML response with that list of medicines.

Also in the application's navigation is a link "All Patients". When the user clicks on that link, it directs the browser via the link's `HREF` attribute to the URL "/patients" on the domain. The URL "/patients" must trigger the method built to load the appropriate patients and send an HTML response with a list of patients.

These mappings are called Routes.

Mapping `http://localhost:9393/medicines` to a Sinatra Action for a response:

![Sinatra Route Example 1](https://dl.dropboxusercontent.com/s/unlxkqbg841b1xh/2015-09-15%20at%209.46%20PM.png)

Mapping `http://localhost:9393/patients` to a Sinatra Action for a response:

![Sinatra Route Example 2](https://dl.dropboxusercontent.com/s/t3mgmc0qwr9hfsi/2015-09-15%20at%209.48%20PM.png)

Users interact with our application by requesting specific URLs via HTTP. URLs are the interface to a web application.

How does our application know what to show a user based on the web request they sent? Through the routes we program in our application. Being able to draw a route in a web application to respond to an HTTP request is the absolute first step in building anything on the web.

In Sinatra, a route is constructed by pairing an HTTP method/verb, like `GET` or `POST` with a URL-matching pattern, i.e. a string that matches what users type in to their browser when they want to visit our webpage. We'll see an example in just a moment.

####HOW DO ROUTES WORK?

Routes match the web request sent by a client to some code in our application that tells the app what data and templates to send back to the client.

Let's go into more detail on our two routes, `/medicines` and `/patients`, from the example application above.

When our doctor types into the browser, `http://localhost:9393/medicines`, our application gets a request: `GET /medicines`.

Our Sinatra application will match this request to a route that is defined in a controller.

The matching route defined in the controller would look like this:

```ruby
get '/medicines' do
    # some code to get all the medicines
    # some code to render the correct HTML page
 end
```

Once the request has been matched to the route in the controller, called the **controller action**, then it executes the code inside of the controller action's block, as shown below:

```ruby
# medicines_controller.rb
 
get '/medicines' do
  @medicines = Medicine.all
 
  erb :'medicines/index.html.erb'
end
```

**Advanced**: You might be wondering what this line does for us: `erb :'/medicines/index.html.erb`. We'll learn much more about ERB soon. For now, all you need to know is that that line of code identifies a file that contains a combination of HTML and Ruby code and sends it back to the client to be rendered in the browser.

Let's run through this specific scenario.

1. The HTTP request verb, `GET` matches the `get` method in our controller. The `/medicines` path in the HTTP request matches the `/medicines` path in our controller method. Yay! We've successfully matched the request to a controller action!

2. Once our app has found its match, it will run the code in the block that accompanies the route. In this case, the block will query the `Medicine` table for all of its medicines. The collection of `Medicine` instances that such a query returns is stored in the variable `@medicines`.

3. Finally, the `@medicines` collection of objects is rendered via the `index.html.erb file`: `views/medicines/index.html.erb`. This file is the HTML (+ some Ruby code––more on that later) that we want our users to see when they request to see all of the medicines. The HTML returned from the template is sent by the controller action as a response to the user.

4. If no matching route for the web request is found, our application will respond with a 404 informing the user's browser that our application cannot find a match for that request URL.

####BREAKING DOWN ROUTE DEFINITION
Now that we know how routes work, let's take a closer look at their syntax.

Sinatra is what is known as a Domain Specific Language, or DSL. A DSL is a specialized, situation-specific language. Sinatra was built expressly for the purpose of creating web applications with Ruby. It is written with Ruby and the code we'll be using to build our Sinatra apps is Ruby code.

The route below:

```ruby
get '/medicines' do 
    # some code to get the medicines and render the correct HTML file
end
```

is actually a plain old-fashioned Ruby method that is getting called with an argument and a block.

Here, the `get` method is called with an argument of `'/medicines'` and is being invoked with a block (the code between the `do`/`end` keywords.

Here's another way to write it that might look familiar:

```ruby
get('/medicines') { some code }
```

The `get`, or the `post`, or `delete` methods for that matter, will be invoked if Sinatra matches the HTTP method (`GET`, `POST`, etc) and the URL, in this case `'/medicines'`, to a route defined in the controller.

**Advanced**: If you're curious, check out the [Sinatra source code](https://github.com/sinatra/sinatra/blob/master/lib/sinatra/base.rb#L1367), especially that code that defines the `#get`, `#post`, `#patch` and `#delete` methods.
