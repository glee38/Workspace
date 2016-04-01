#Sessions
##Sessions and Cookies
####HOW DO YOUR WEBSITES RECOGNIZE YOU?
When you visit Facebook, chances are, you'll find you're logged in and be taken to your timeline directly. If you check your Gmail account, you might see advertisements relating to search terms you typed into Google earlier that day. When you shop on Amazon, your shopping cart stays full while you continue to shop all the way up until you checkout. How is this possible? Is your computer spying on you all the time? The answer is, sort of.

Before we can understand what is so amazing about the kind of persistence and recognition we've come to expect from our web applications, we need to understand something about how the web works.

> HTTP is a stateless protocol, treating each request as an independent transaction that is unable to use information from any previous requests. This means there is no way within the hypertext transfer protocol to remember a user’s identity from page to page...

What? Then how does Facebook remember me?

> instead, web applications requiring user login must use a session, which is a semi-permanent connection between two computers (such as a client computer running a web browser and a server running Rails).
–– Michael Hartl, The Ruby on Rails Tutorial

Since HTTP is stateless, we need the browser to be responsible for holding some sort of simple state. On each HTTP request, the server sends some information for the browser. Cookies were created to store that data. A **cookie** is effectively just storing a hash on the browser that then gets sent with every request.

Servers then realized that there was a use case for just storing data very temporarily just while a user is using their website. We call that one usage a session.

A **session** is simply an object, like a hash, that stores data describing a client's interaction with a website at a given point in time. The session hash lives on the server. Your application can access it in any of your controllers at any point in time.

So, how does a web application know who is interacting with it? By using **cookies and session data**.

When you send a request to http://www.facebook.com, the application, on the server, will generate and store some data about you. The application will keep that data in a session hash and it will put that data into a small text snippet called a cookie. The cookie gets sent back to you, the client, and is stored in your browser. Cookies persist from one page to the next when you are browsing the web and are sent back to the application on the server any subsequent time you send another request to http://www.facebook.com. When that happens, the cookie data will be compared to the session data to help the application determine what steps to take and what information to send back to you.

####HOW DO COOKIES WORK?
Here's the general flow:

1. You visit a webpage, www.facebook.com

2. The web application that is Facebook receives that request and creates a cookie with some information about you as a user.

3. Facebook sends that cookie back to the browser where it is stored temporarily. The cookie will last until you log out of Facebook, close your browser or clear your cache.

4. Every subsequent request you, the client, sends to http://www.facebook.com sends the cookies back to the server where Facebook can access it, get information out of it and alter it.

####WHAT IS CONTAINED IN A COOKIE?
Cookies will usually have a URL to the website that generated it, the date on which it was created (and the date on which it expired, if applicable), and other pertinent information that has been requested by the web application (such as remembered login information, user preferences, etc).

####SESSION COOKIES VS. PERSISTENT COOKIES
There are two primary kinds of cookies: session and persistent.

**Session cookies** allow a website to keep track of your movement from page to page for that specific session (from the time you log in to the time you log out/close the browser). Session cookies simply allow you to navigate through a site without repeatedly having to authenticate yourself on every new page you visit within the web application domain. Session cookies expire every time you log out or navigate away from the website.

Let's walk through an example of what would happen without a session cookie. If you were on Amazon, added an item to your shopping cart, and then selected the "Checkout" option, the new page would have no recollection of your prior activity. Your shopping cart would always be empty, even though you explicitly added an item earlier. There would be no record of your activities for that session.

Later on, we'll learn how to use the cookie and session objects to build apps that allow users to log in and log out.

**Persistent cookies** allow a website to remember your user information and preferences for *future* visits. A persistent cookie is stored on your computer, while a session cookie is temporarily stored in your web browser. Persistent cookies allow for faster page loading, automatic login and user authentication, and access to other potential web application features because much of the data that these features require will be hanging out in a persistent cookie, stored on your computer. This means you don't have to go through the cycle of sending a web request to a site and waiting to receive data back from the server in order for your browser to have your login info, for example. Persistent cookies are usually created on the first visit to the page after you have created an account, and typically don't expire unless the web application has a protocol in place for cookie expiration, or the user clears their browser's cookie cache.

What would happen if we didn't have persistent cookies? We would have to go to Facebook's login page and type in our email address and password every single time we wanted to visit Facebook. Our user settings and preferences would not be remembered, and the quality of the user's experience would go way down.

####COOKIES AND SESSIONS WORK TOGETHER
You can think of cookies as client-side counterparts to sessions. They store information in your browser or on your computer and are visible only to the server that created them. Client browsers send cookies to remote servers along with each web request. Then the web application on the server can read the cookie, associate it with an existing session (if any) and decide how to respond. For example, if there is no cookie received, the application might show the login page. If a cookie is received and it's data doesn't match the data stored in the session on the server-side of the application, the app might show the login page with the user name pre-filled, having gotten that data from the cookie. If a cookie is received that does match the data in a session on the server-side of the application, the app might respond with that user's data.

##Mechanics of Sessions
####SETTING UP A SESSION
Before we dive into the mechanics of setting up a session, it's important to note that a session is basically just a hash that stores data on the server, and passes the data to the client as a cookie. You can access the data stored in the session in the same way you would any hash in Ruby.

And now to set up the session...

In Sinatra, you want to set up your session in your controller, `app.rb` by using Sinatra's `configure block` to enable sessions in your app:

```ruby
configure do
  enable :sessions
  set :session_secret, "secret"
end
```

The `configure` block above is a part of built-in settings that control whether features are enabled or not. In this case, we're enabling the sessions feature.

The first line of the configure block `enable :sessions`, turns sessions on. The next line `set :session_secret, "secret"` is an encryption key that will be used to create a `session_id`. A `session_id` is a unique string of letters and numbers that is unique per session and is stored in the browser cookie. You can actually set your session secret to anything that you want. Don't worry too much about understanding how the `session_id` works. It's just part of the mechanics of getting a secure private session working. You won't need to interact with it pretty much ever.

The `session_secret` is a pretty minimal security feature, but the basic idea is that setting your `:session_secret` to a word that other people don't know makes it harder for someone to create a fake session_id and hack into your site without signing up or signing in.

####USING SESSIONS
In order to keep track of a current user throughout a session, you would want to set up the `session` hash to store the `user_id` in the hash during a controller action. Because we haven't covered how to set up log in and log out, we can use the `session` hash to store the user's name:
 
```ruby
get '/hey' do 
  @session = session
end
```

In the example above, we have a route `/hey` that gets processed by a GET request. Because we enabled sessions in our app, every controller action has access to the session hash.

We stored our session hash in the instance variable `@session` so that our views will have access to the session data. In this case `@session` looks like this:

```ruby
@session = {
  "session_id"=>  
    "dd32f512ee239ad74aa6f10c8cad37ce28d6c6922eff252ed641b1017130fe22", 
  "csrf"=> "040e9777d4dfae03bb1e6498f2a75482", 
  "tracking"=>{ 
    "HTTP_USER_AGENT"=> "e193e9e937caa9a19ca483f046281aae77d2216b", 
    "HTTP_ACCEPT_LANGUAGE"=> "66eae971492938c2dcc2fb1ddc8d7ec3196037da"
  }
}
```

You can access information from the hash just like you would any hash. `@session["session_id"]` will return `"dd32f512ee239ad74aa6f10c8cad37ce28d6c6922eff252ed641b1017130fe22"`.

You can also modify and add data to the session hash by adding a key-value pair:

```ruby
get '/hey' do 
  session["name"] = "Victoria"
  @session = session
end
```

####VIEWING SESSIONS
#####VIEWING IN DEVELOPER TOOLS
Even though you set up a session on the server, you can view the contents of the session in the browser as a cookie using Developer Tools.

Visit any website that requires you to log in and make sure that you're logged in already before completing the next steps. We'll be using learn.co in this example.

Go ahead and open up the Developer Tools. In Chrome, you can do this by right clicking and selecting `Inspect Element`. Once the developer tools are open, click the `Resources`. On the left side, you'll see a section that says `Cookies` and it will bring up all the cookies loaded on the site. You can see that several of them say `Session`.

![session](https://s3.amazonaws.com/learn-verified/browser-cookies.png)

#####VIEWING IN CHROME SETTINGS
You can see the details of these cookies in the Chrome browser by selecting the hamburger icon in the top right corner and selecting settings:

![chrome_view](https://s3.amazonaws.com/learn-verified/chrome-settings.png)

Scroll all the way to the bottom the page and select the link `Show advanced settings...`. Next, scroll down till you see `Privacy` and select `Content settings...`:

![privacy](https://s3.amazonaws.com/learn-verified/privacy-content-settings.png)

And last, select `All Cookies and Data`:

![cookies_and_data](https://s3.amazonaws.com/learn-verified/all-cookies-data.png)

This will bring up a list of all the sites that your browser has stored cookies from. Search for `learn.co` and click to see a list of all the cookies Learn keeps. You should see one called `_feyonce_session`. That's the session cookie learn uses to keep track of a current user.

####CLEARING SESSIONS
There are several ways to clear a session cookie. The first is to simply log out of the site. At that point, you automatically clear any browsing data.

First, there is a way to clear the cookies with the developer tools. Simply right click on a session and select either `Delete` or `Delete All from learn.co`

![delete_session](https://s3.amazonaws.com/learn-verified/clear-cookies.png)

As soon as you remove the cookies, you'll notice that more likely than not, you'll be logged out of the site.

Second, you can open an `Incognito Window` in Chrome which will not store any cookies. An Incognito Window allows you to browse without Chrome storing your browsing history and cookie store. You can open a browser in Incognito by selecting `File < New Incognito Window`.

If you log into Learn.co in the Incognito Window, it will create the cookies needed to run that session, but will not have cached cookies. This can be helpful when debugging session and cookie issues.
