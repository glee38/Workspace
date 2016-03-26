#Rack and the Internet
##How the Internet Works
####INTRO
How many times a day do you use the internet? How many times do you load a different web page? Think about how many times you do this in a year! In order to be a developer-- and especially a web developer-- it's incredibly important to understand how the web works. From here on out, you are no longer just a user of the internet. You are a creator of the web.

####CLIENT AND SERVER
So seriously, how does this:

```
https://www.youtube.com/user/AdeleVEVO
```

Turn into this:

![youtube](https://s3.amazonaws.com/learn-verified/request-intro.png)

The internet operates based on conversations between the client (more familiarly known as the browser) and the server (the code running the web site you're trying to load). By typing in that URL into your browser, you (the client) are *requesting* a web page. The server then receives the request, processes it, and sends a *response*. Your browser receives that response and shows it to you. These are the fundamentals of the web. Browsers send requests, and servers send responses. Until today you have always been a client. Moving forward you will be building the server. This means processing requests, creating responses, and sending them back to the client.

We will be writing our servers using Ruby and a few different frameworks. But your browser doesn't know, nor does it care, what server it talks to. How does that work? How can a server that was written 15 years ago still work with a browser written 15 months or days ago?

In addition, you can use multiple clients! You can use Chrome, Safari, Internet Explorer, Opera, and many others. All of those browsers are able to talk to the same server. Let's take a closer look at how this occurs.

####HTTP OVERVIEW
Being able to switch out both the server or the client happens because the way browsers and servers talk is controlled by a contract or *protocol*. Specifically it is a protocol created by Tim Berners-Lee called the **H**yper **T**ext **T**ransfer **P**rotocol or HTTP. Your server will receive requests from the browser that follow HTTP. It then responds with an HTTP response that all browsers are able to parse.

`HTTP` is the language browsers speak. Every time you load a web page, you are making an `HTTP` request to the site's server, and the server sends back an `HTTP` response.

In the example above, the client is making an `HTTP GET` request to YouTube's server. YouTube's server then sends back a response and the client renders the page in the browser.

![example](https://s3.amazonaws.com/learn-verified/requests.png)

####REQUESTS

**URI**

When you make a request on the web, how do you know where to send it to? This is done through **U**niform **R**esource **I**dentifiers or URIs. You've probably also heard of these as URLs. Both are fine. Let's look at the URI we used up top.

`http://www.youtube.com/adelevevo`

This URI is broken into three parts:

+ `http` - the protocol
+ `youtube.com` - the domain
+ `/adelevevo` - the resource

The `protocol` is the way we're sending our request. There are several different types of internet protocols (SMTP for emails, HTTPS for secure requests, FTP for file transfers). To load a website, we use HTTP.

The `domain name` is a string of characters that identifies the unique location of the web server that hosts that particular website. This will be things like `youtube.com` and `google.com`.

The `resource` is the particular part of the website we want to load. YouTube has millions and millions of channels and videos, so the specific resource we want is `/adelevevo` (because we can't get Hello out of our heads).

An analogy that works well is thinking of an apartment building. The domain is the entire building. Within that building though there are hundreds of apartments. We use the specific resource (or sometimes called path) to figure out that we care about apartment 4E. The numbering/lettering system is different for every apartment building just like how a server has its resources laid out is a bit different for every website. For example doing a search on Google ends in a URL like this `https://www.google.com/search?q=URI` but searching for URI on Facebook leads to this URL `https://www.facebook.com/search/top/?q=uri`.

**HTTP VERBS**

When you're making a request, not only do you want to give all the details of your request, you also need to specify what action you would like the server to do. We do this with HTTP Verbs. With the same resource, you may want more than one action to occur. Using a browser, almost all requests are `GET` requests. This just means "hey server, please GET me this resource". There are a few other verbs though. What if we want to send some data from the user to the server? This is done with a `POST` request. There are a ton of verbs, and we'll go further into them later, but here is a full list:

VERB | Description
-----|------------
HEAD | Asks for a response like a GET but without the body
GET	 | Retrieves a representation of a resource
POST | Submits data to be processed in the body of the request
PUT	 | Uploads a representation of a resource in the body of the request
DELETE | Deletes a specific resource
TRACE | Echoes back the received request
OPTIONS | Returns the HTTP methods the server supports
CONNECT | Converts the request to a TCP/IP tunnel (generally for SSL)
PATCH | Apply a partial modification of a resource

**REQUEST FORMAT**

Our client so far has made a request to YouTube's server. In this case, a request to `/adelevevo`. The server then responds with all the code associated with that resource `<!doctype html> .....</html>`, including all images, CSS files, JavaScript files, videos, music, etc.

When the client makes a request, it includes other items besides just the URL in the "headers." The request header contains all the information the server needs in order to fulfill the request: the type of request, the resource (path), the domain as well as some other metadata like what type of browser is making this request. The request header would look something like this.

![request_header](https://s3.amazonaws.com/learn-verified/request-header.png)

####RESPONSES
Once your server receives the request, it will do some processing (run code you wrote!) and then send a response back. The server's response headers look something like this:

![response_header](https://s3.amazonaws.com/learn-verified/response-headers.png)

The servers response is separated into two sections, the headers and the body. The headers are all of the metadata about the response. This includes things like content-length (how big is my response) and what type of content it is. The headers also include the status code of the response. The *body* of the response is what you see rendered on the page. It is all of that HTML/CSS that you see! Most of the data of a response is in the body, not in the headers.

####STATUS CODES
Every time there is a successful response (you'll know it's successful because the page will load without any errors), it's a status code of `200`, but there are other status codes and it's good to get familiar with them. You've probably seen the second most popular status code, `404`. This means "file not found." Status codes are separated into categories based on their first digit. Here are the different categories:

+ 100's - informational
+ 200's - success
+ 300's - redirect
+ 400's - error
+ 500's - server error

A full list of status codes is [up on Wikipedia](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

####SERVERS
It's important to note that there are two different types of webapps: static and dynamic. A `static` webapp is one that doesn't change. The content doesn't change unless a developer opens up a HTML file and modifies the content of that file. A `dynamic` webapp are sites where the content changes based on user input (e.g. Facebook, Twitter, Yelp, etc.). Every time you visit the site, the content is most likely different because someone else gave a review of that restaurant, or sent out a new tweet, or commented on that image you liked. These are the types of apps you'll be building.
The flow of request and response changes slightly based on a static or a dynamic webapp.
When the client wants to load a static site, the client makes a request, the server finds the file on a disk, and sends it back. Done and Done.
It gets a little bit more complex with a webapp. The client makes a request, the server runs application code (think of this as your Ruby code), and returns a dynamically generated response.

##Inspecting the Web with Rack
####WHY RACK?
We've learned how the web works so far using abstract concepts. Problem is, when you are dealing with massive websites like YouTube or Facebook it's hard to fully understand the moving parts because there are so many of them. Thankfully, there is a gem for Ruby that will help us create a web server at its simplest. This gem is called [Rack](https://rack.github.io/) and it is what Rails is built on top of. Before we get to the complexity of Rails, let's first understand HTTP in its simplest form.

Let's start by getting the mechanics of Rack working.

####SETTING UP RACK

To work with Rack we need to create a new class that responds to a single method: `#call`. All this method needs to do is return a response which consists of the status code, any headers, and the body. This can all be done using the `Rack::Response` object.

Using this, let's create a basic web app. Follow along with the below instructions.

We first create a `Rack::Response` object, then add some text "Hello, World" to the body, and complete the response with the `#finish` method. By default, Rack sets our status codes and headers. Don't worry about the `env` input. This holds all of the *request* info in it and we will use it later!

```
class Application
 
  def call(env)
    resp = Rack::Response.new
    resp.write "Hello, World"
    resp.finish
  end
 
end
```

The above is the code that will be run whenever there is a request received. But we have one more step. We need to actually set up an HTTP web server that will receive the HTTP request, send it through the above `#call` method, and then serve the response to the browser. We do this using a `config.ru` file and the `rackup` command. Our `config.ru` file should look like this:

```
#config.ru
require_relative "./application.rb"
 
run Application.new
```

To run this code we then run rackup `config.ru`. If you're using an Integrated Development Environment, such as Nitrous, you may need to use `rackup config.ru -b 0.0.0.0`. Everything goes as planned, you'll see:

```
[2015-11-27 16:48:22] INFO  WEBrick 1.3.1
[2015-11-27 16:48:22] INFO  ruby 2.1.3 (2014-09-19) [x86_64-darwin13.0]
[2015-11-27 16:48:22] INFO  WEBrick::HTTPServer#start: pid=11275 port=9292
```

Notice it says `port=9292`. If you open your browser and go to `http://localhost:9292/` you should see `Hello, World`. In Nitrous, use the 'Preview' feature on port 9292. Congrats! You just made your first web app!

Let's deconstruct this URL a little bit though. The URL is `http://localhost:9292/`. The protocol is `http`. That makes sense, but the domain is `localhost:9292`. What's going on there? `localhost` is normally where a server like `google.com` goes. In this case, since you are running the server on your computer, `localhost` is the server name of your own computer. Nobody else can get that URL though. That's good for right now. This allows you to play around with writing websites without the security concerns of opening it up to the entire web. The last part of that URL is the `:9292` section. This the "port number" of your server. Don't worry too much about this, but you may want to run multiple servers on one computer and having different ports allows them to be running simultaneously without conflicting. The resource that you are requesting is `/`. This is effectively like saying the home or default.

Now it's your turn. Modify your app to introduce yourself by saying "Hello, my name is ". To exit the running web server and get back to your terminal press CTRL-C. **You will have to do this every time you change your code**.

##Dynamic Web Apps with Rack
####CREATING A DYNAMIC WEB APP
Making web apps that always give the same response are boring. Programming is fun because of its ability to create dynamic responses that change depending on the input. A dynamic web app in Rack is pretty straightforward. Let's say we wanted to create a simple slots game.

First, let's set up our basic Rack app:

```
class Application
 
  def call(env)
    resp = Rack::Response.new
    resp.write "Hello, World"
    resp.finish
  end
 
end
```

Then run it with `rackup config.ru`. If we go to `localhost:9292` in our browser, we should see "Hello, World". Let's liven things up a bit. The amazing part of Rack and everything (like Rails) that is built on top of Rack is that it's *just Ruby*. If you were writing a command line slots game generator, you would first need to generate three numbers between 1 and 20. You could do that like this:

```
num_1 = Kernel.rand(1..20)
num_2 = Kernel.rand(1..20)
num_3 = Kernel.rand(1..20)
```

Then, to check to see if you won or not, we'd have an if statement like this:

```
num_1 = Kernel.rand(1..20)
num_2 = Kernel.rand(1..20)
num_3 = Kernel.rand(1..20)
 
if num_1==num_2 && num_2==num_3
  puts "You Win"
else
  puts "You Lose"
end
```

So how do we now make this application work on the web? Almost none of the code actually is specific to a command line interface. The only parts that require a command line are the two `puts` lines. All that needs to change to adapt this for the web is a different way than `puts` to express output to our user. Because this is the web, that means adding it to our response. Instead of `puts` now we'll use the `#write` method in our `Rack::Response` object.

Remember that to modify our web server, we have to first exit out of the running server by typing CTRL-C. Then open up your Application and modify it to look like this:

```
class Application
 
  def call(env)
    resp = Rack::Response.new
 
    num_1 = Kernel.rand(1..20)
    num_2 = Kernel.rand(1..20)
    num_3 = Kernel.rand(1..20)
 
    if num_1==num_2 && num_2==num_3
      resp.write "You Win"
    else
      resp.write "You Lose"
    end
 
    resp.finish
  end
 
end
```

Notice that we only changed the `puts` statements into `resp.write` statements. That's it! Web servers are just big ruby apps that respond to the user in an HTTP response rather than via `puts` statements. Let's give the user a bit more information about what numbers they received by writing the numbers to the response as well. The `#write` method can be called many times to build up the full response. The response isn't sent back to the user until `#finish` is called.

```
class Application
 
  def call(env)
    resp = Rack::Response.new
 
    num_1 = Kernel.rand(1..20)
    num_2 = Kernel.rand(1..20)
    num_3 = Kernel.rand(1..20)
 
    resp.write "#{num_1}\n"
    resp.write "#{num_2}\n"
    resp.write "#{num_3}\n"
 
    if num_1==num_2 && num_2==num_3
      resp.write "You Win"
    else
      resp.write "You Lose"
    end
 
    resp.finish
  end
 
end
```

The `\n`s are just a special character which gets rendered by the browser as a new line. Kill your running server with CTRL-C and re-run it and refresh your browser. Feel free to cheat a bit and change the random numbers to just be between one and two. That way you can test that both work.

##The HTTP Request
####HTTP REQUESTS
An HTTP Request that our browser sends to the server contains two main sections of information. One is headers and the other section is the resource or path requested (for example, `/search` or `/profile_name`). Let's break down the second section since there's a lot of information that can be stored in it!

**THE PATH**

The path that is requested is the resource that the client wants. Since your server can contain a lot of functionality, the path signifies which specific part of your server it wants. If we were creating a simple shopping cart application, for example, we can think of a few different paths that are required:

/items | List all items available
-------|-------------------------
/cart | List items in cart

How would we implement this in our Rack app? The path lives in the HTTP request, and to get to it we have to inspect the `env` part of our `#call` function. In the `env` variable is all of the information contained in the request. Thankfully, Rack has a great way of parsing all this information for us. It looks like this:

```
class Application
 
  def call(env)
    resp = Rack::Response.new
    req = Rack::Request.new(env)
    resp.finish
  end
 
end
```

This [Rack::Request](http://www.rubydoc.info/gems/rack/Rack/Request) instance now has a ton of useful methods. If we look through the documentation, it has a method called `#path`. This will return the path that was requested.

Before we inspect the `#path`, let's set up what we would do if someone wanted to see all of our items:

```
class Application
 
  @@items = ["Apples","Carrots","Pears"]
 
  def call(env)
    resp = Rack::Response.new
    req = Rack::Request.new(env)
 
    @@items.each do |item|
      resp.write "#{item}\n"
    end
 
    resp.finish
  end
```

Now we put all of your items out. The problem though is that this will list all of your items *no matter what path you put in*. Give it a try. Create your Rack file with the above code. Type l`ocalhost:9292/items`, `localhost:9292/cart`, `localhost:9292/flatiron/is/awesome`. All of those URLs work since we're not filtering for path. In other words, no matter what the request is, we send the same response. Let's filter so that this only works for the `/items` path using the `#path` method of our `Rack::Request` object:

```
class Application
 
  @@items = ["Apples","Carrots","Pears"]
 
  def call(env)
    resp = Rack::Response.new
    req = Rack::Request.new(env)
 
    if req.path.match(/items/)
      @@items.each do |item|
        resp.write "#{item}\n"
      end
    else
      resp.write "Path Not Found"
    end
 
    resp.finish
  end
```

Great! With this we can now do different things depending on the path.

**USER INPUT VIA THE PATH**

What if users wanted to check and see if we have `Apples` available in our list of items? How do other websites handle getting user queries? If we go to GitHub and type in "apples" in the search query, we get a URL that looks like this: `https://github.com/search?q=apples`. We have a domain of `github.com`, path of `search`, and then a `?` character. After that character comes `q=apples`. There is our search!

The section after the `?` is called the `GET` parameters. If you notice in the above example, `GET` params come in key/value pairs. The key in GitHub's case would be `q` and the value is `apples`. The matching Ruby data structure that is also a key/value store would be a `Hash`! Thankfully, Rack provides the mechanism to parse the `GET` params and return them to us in a standard `Hash`. If we wanted to implement a `/search` route that accepted a `GET` param with the key `q` it would look something like this:

```
class Application
 
  @@items = ["Apples","Carrots","Pears"]
 
  def call(env)
    resp = Rack::Response.new
    req = Rack::Request.new(env)
 
    if req.path.match(/items/)
      @@items.each do |item|
        resp.write "#{item}\n"
      end
    elsif req.path.match(/search/)
 
      search_term = req.params["q"]
 
      if @@items.include?(search_term)
        resp.write "#{search_term} is one of our items"
      else
        resp.write "Couldn't find #{search_term}"
      end
 
    else
      resp.write "Path Not Found"
    end
 
    resp.finish
  end
```